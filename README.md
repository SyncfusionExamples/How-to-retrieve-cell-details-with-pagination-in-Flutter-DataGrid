# How to retrieve cell details when tapping cells with pagination without overriding the handlePageChange method in Flutter DataGrid.

In this article, we will show you how to retrieve cell details when tapping cells with pagination without overriding the handlePageChange method in [Flutter DataGrid](https://www.syncfusion.com/flutter-widgets/flutter-datagrid).

Initialize the [SfDataGrid](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/SfDataGrid-class.html) widget with all the required properties. To support sorting and filtering across all pages, maintain a list of all rows in [DataSource.effectiveRows](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/DataGridSource/effectiveRows.html). When using pagination, the rowIndex provided corresponds to the rows available on the current page. By utilizing the [onPageNavigationEnd](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/SfDataPager/onPageNavigationEnd.html) callback, which is triggered when page navigation is successfully completed, you can obtain the respective `pageIndex`. This allows you to accurately calculate the rowIndex and retrieve the correct details on [onCellTap](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/SfDataGrid/onCellTap.html) when a cell is tapped.

```dart
int currentPageIndex = 0;
 
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Syncfusion Flutter DataGrid'),
      ),
      body: LayoutBuilder(builder: (context, constraint) {
        return Column(children: [
          SizedBox(
              height: constraint.maxHeight - _dataPagerHeight,
              width: constraint.maxWidth,
              child: _buildDataGrid(constraint)),
          SizedBox(
            height: _dataPagerHeight,
            child: SfDataPager(
              delegate: employeeDataSource,
              pageCount: (employees.length / _rowsPerPage).ceil().toDouble(),
              onPageNavigationEnd: (pageIndex) {
                setState(() {
                  currentPageIndex = pageIndex;
                });
              },
            ),
          )
        ]);
      }),
    );
  }

  Widget _buildDataGrid(BoxConstraints constraint) {
    return SfDataGrid(
      source: employeeDataSource,
      rowsPerPage: _rowsPerPage,
      columnWidthMode: ColumnWidthMode.fill,
      onCellTap: (DataGridCellTapDetails details) {
        // Avoid processing header row taps.
        if (details.rowColumnIndex.rowIndex > 0) {
          // Calculate the zero-based index across all pages.
          int globalRowIndex = (currentPageIndex * _rowsPerPage) +
              (details.rowColumnIndex.rowIndex - 1);

          showDialog(
              context: context,
              builder: (BuildContext context) => AlertDialog(
                  title: const Text('Tapped Content'),
                  actions: <Widget>[
                    TextButton(
                        onPressed: () => Navigator.pop(context),
                        child: const Text('OK'))
                  ],
                  content: Text(employeeDataSource.effectiveRows[globalRowIndex]
                      .getCells()[details.rowColumnIndex.columnIndex]
                      .value
                      .toString())));
        }
      },
      columns: <GridColumn>[
        GridColumn(
            columnName: 'id',
            label: Container(
                padding: const EdgeInsets.all(16.0),
                alignment: Alignment.center,
                child: const Text(
                  'ID',
                ))),
        GridColumn(
            columnName: 'name',
            label: Container(
                padding: const EdgeInsets.all(8.0),
                alignment: Alignment.center,
                child: const Text('Name'))),
        GridColumn(
            columnName: 'designation',
            label: Container(
                padding: const EdgeInsets.all(8.0),
                alignment: Alignment.center,
                child: const Text(
                  'Designation',
                  overflow: TextOverflow.ellipsis,
                ))),
        GridColumn(
            columnName: 'salary',
            label: Container(
                padding: const EdgeInsets.all(8.0),
                alignment: Alignment.center,
                child: const Text('Salary'))),
      ],
    );
  }
```

You can download this example on [GitHub](https://github.com/SyncfusionExamples/How-to-retrieve-cell-details-with-pagination-in-Flutter-DataGrid).