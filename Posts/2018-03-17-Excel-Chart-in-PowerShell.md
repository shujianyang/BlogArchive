---
title: Excel Chart in PowerShell
date: 2018-03-17 20:00:00
---

Equipped with Office Interop and the ability to manipulate COM objects, Windows
PowerShell is capable of generating rich Excel sheets, which can be very helpful
in analyzing and displaying data.

One minor problem is depending on different versions of Office, some features may
act differently. For example, PowerShell can be used to create charts in an Excel
file:

```
$excel = New-Object -comobject Excel.Application
$workbook = $excel.Workbooks.Add()
$sheet = $workbook.Worksheets.Item(1)

$objCharts = $worksheet.ChartObjects()
$objChart = $objCharts.Add(100, 100, 100, 100)

$dataRange = $worksheet.range("A1:E4")
$objChart.Chart.SetSourceData($dataRange, 2)
```

The default chart type used here will be Bar chart. To change the chart type, in
a system installed with newer version of Office, the code will be:

`$objChart.Chart.ChartType = [Microsoft.Office.Interop.Excel.XLChartType]::xl3DPieExploded`

This creates a 3D Exploded Pie chart.

However, in a system with early Office 2007, this XLChartType enum will not be
recognized. To make the same chart, an integer must be used instead:

`$objChart.Chart.ChartType = 70`

For a list of all the XLChartType enums and corresponding integer values, refer
to the following official document:  
[XlChartType Enumeration (Excel)](https://msdn.microsoft.com/en-us/vba/excel-vba/articles/xlcharttype-enumeration-excel)

See also:

Chart types examples:  
[Available chart types in Office](https://support.office.com/en-us/article/available-chart-types-in-office-a6187218-807e-4103-9e0a-27cdb19afb90)

C# Office Interop programming guide:  
[How to: Access Office Interop Objects by Using Visual C# Features](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/interop/how-to-access-office-onterop-objects)