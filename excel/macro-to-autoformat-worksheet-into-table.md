# Macro to automformat worksheet into a table 

In newer version of excel, its very easy to create table and format them into many styles. But to do so, you need to
select the entire range of data and then stylize it. I wanted to make this a bit simple. 

I wrote a macro that would auto select the used range and format those cells into table. While converting it into table
it will change the font and its size, unwrap the columns, autofit them etc..

Macro in general are stored on a worksheet level. To enable a macro across any workbook, we need to save them as .xlam
format - this is a Excel Add-in format. The steps to enable the add-in is descibed in the link below

[Add a .xlam file as a Add-in] 



Here is the macro I use. You can link this to a quick action so its even faster to apply 

```

Sub formatCurrentSheet()
    Application.ScreenUpdating = False

    Dim filterArray() As Variant
    Dim currentFilterRange As Variant
    Dim isFilterApplied As Boolean
    
    currentFilterRange = Null
    isFilterApplied = False
    
    'ifivot exists on aage, then skip it
    'check if multiple filters exists - handle it
    ActiveWindow.Zoom = 90
    With Application.ActiveSheet:
        .Cells.Font.Name = "Calibri"
        .Cells.Font.Size = 10
        .Cells.Interior.ThemeColor = xlThemeColorDark1
        .Cells.Interior.PatternTintAndShade = 1
        .UsedRange.Borders.LineStyle = xlContinuous
        .UsedRange.Borders.Weight = xlThin
        .UsedRange.Borders.ThemeColor = 1
        .UsedRange.Borders.TintAndShade = -0.349986266670736
    
        .UsedRange.Rows(1).Interior.ThemeColor = xlThemeColorLight2
        .UsedRange.Rows(1).Font.ThemeColor = xlThemeColorDark1
        .UsedRange.Rows(1).Font.Bold = True
        .UsedRange.Rows(1).HorizontalAlignment = xlCenter
        .UsedRange.AutoFilter
        .UsedRange.Columns.AutoFit
    End With
    
    Application.ScreenUpdating = True
    
End Sub


```
