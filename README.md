    # msexcel-builder

A simple and fast library to create MS Office Excel(>2007) xlsx files(Compatible with the OpenOffice document format). 

Features:

* Support workbook and multi-worksheets.
* Custom column width and row height, cell range merge.
* Custom cell fill styles(such as background color).
* Custom cell border styles(such as thin,medium).
* Custom cell font styles(such as font-family,bold).
* Custom cell border styles and merge cells.
* Text rotation in cells.

## Getting Started

Install it in node.js:

```
npm install msexcel-builder
```

```javascript
var excelbuilder = require('msexcel-builder');
```

Then create a sample workbook with one sheet and some data.

```javascript
  // Create a new workbook file in current working-path
  var workbook = excelbuilder.createWorkbook('./', 'sample.xlsx')
  
  // Create a new worksheet with 10 columns and 12 rows
  var sheet1 = workbook.createSheet('sheet1', 10, 12);
  
  // Fill some data
  sheet1.set(1, 1, 'I am title');
  for (var i = 2; i < 5; i++)
    sheet1.set(i, 1, 'test'+i);
  
  // Save it
  workbook.save(function(err){
    if (err)
      throw err;
    else
      console.log('congratulations, your workbook created');
  });
```

or return a JSZip object that can be used to stream the contents (and even save it to disk):

```
   workbook.generate(function(err, jszip) {
     if (err)
       throw err;
     else {
       var buffer = jszip.generate({type: "nodebuffer"});
       require('fs').writeFile(workbook.fpath + '/' + workbook.fname, buffer, function (err) {
     }
   });
```

## API

### createWorkbook(save_path, file_name)

Create a new workbook file.

* `save_path` - (String) The path to save workbook.
* `file_name` - (String) The file name of workbook.

Returns a `Workbook` Object.

Example: create a xlsx file saved to `C:\test.xlsx`

```javascript
var workbook = excelbuilder.createWorkbook('C:\','test.xlsx');
```

### Workbook.createSheet(sheet_name,column_count,row_count)

Create a new worksheet with specified columns and rows

* `sheet_name` - (String) worksheet name.
* `column_count` - (Number) sheet column count.
* `row_count` - (Number) sheet row count.

Returns a `Sheet` object

Notes: The sheet name must be unique within a same workbook.  No error checking or collision-resolution mechanisms are currently applied.

Also the sheet name is cleaned, replacing disallowed characters `[]\/*:?` with a dash `-`

Example: Create a new sheet named 'sheet1' with 5 columns and 8 rows

```javascript
var sheet1 = workbook.createSheet('sheet1', 5, 8);
```

### Workbook.save(callback)

Save current workbook.

* `callback` - (Function) Callback function to handle save result.

Example:

```javascript
workbook.save(function(err){
  console.log('workbook saved ' + (err?'failed':'ok'));
});
```

### Workbook.cancel()

Cancel to make current workbook,drop all data.

### Sheet.set(col, row, val)

Set the cell data.

* `col` - (Number) Cell column index(start with 1).
* `row` - (Number) Cell row index(start with 1).
* `val` - (String) Cell data.  May be a string or number.

No returns.

Example:

```javascript
sheet1.set(1,1,'Hello ');
sheet1.set(2,1,'world!');
```

## Sheet.width(col, width)
## Sheet.height(row, height)

Set the column width or row height

Example:

```javascript
sheet1.width(1, 30);
sheet1.height(1, 20);
```

## Sheet.align(col, row, align)
## Sheet.valign(col, row, valign)
## Sheet.wrap(col, row, wrap)
## Sheet.rotate(col, row, angle)

Set cell text align style and wrap style

* `align` - (String) align style: 'center'/'left'/'right'
* `valign` - (String) vertical align style: 'center'/'top'/'bottom'
* `wrap` - (String) text wrap style:'true' / 'false'
* `rotate` - (String) Numeric angle for text rotation: '90'/'-90'

Example:

```javascript
sheet1.align(2, 1, 'center');
sheet1.valign(3, 3, 'top');
sheet1.wrap(1, 1, 'true');
sheet1.rotate(1, 1, 90);
```

## Sheet.font(col, row, font_style)
## Sheet.fill(col, row, fill_style)
## Sheet.border(col, row, border_style)

Set cell font style, fill style or border style

* `font_style` - (Object) font style options 
The options may contain:

  * `name` - (String) font name
  * `sz` - (String) font size
  * `family` - (String) font family
  * `scheme` - (String) font scheme
  * `bold` - (String) if bold: 'true'/'false'
  * `iter` - (String) if italic: 'true'/'false'
  * `color` - (String) font color as HEX RGB or ARGB value (e.g. `"2266AA"` or `"FF2266AA`"`)

* `fill_style` - (Object) fill style options
The options may contain:

  * `type` - (String) fill type: such as 'solid'
  * `fgColor` - (String) front color, as HEX RGB or ARGB value (e.g. `"2266AA"` or `"FF2266AA`"`)
  * `bgColor` - (String) background color

* `border_style` - (Object) border style options
The options may contain:

  * `left` - (String) style: 'thin'/'medium'/'thick'/'double'
  * `top` - (String) style: 'thin'/'medium'/'thick'/'double'
  * `right` - (String) style: 'thin'/'medium'/'thick'/'double'
  * `bottom` - (String) style: 'thin'/'medium'/'thick'/'double'

Example:

```javascript
sheet1.font(2, 1, {name:'黑体',sz:'24',family:'3',scheme:'-',bold:'true',iter:'true', color: 'FFAA00'});
sheet1.fill(3, 3, {type:'solid',fgColor:'2266aa',bgColor:'64'});
sheet1.border(1, 1, {left:'medium',top:'medium',right:'thin',bottom:'medium'});
```

## Sheet.merge(from_cell, to_cell)

Merge some cell ranges

* `from_cell` / `to_cell` - (Object) cell position
The cell object contains:

  * `col` - (Number) cell column index(start with 1)
  * `row` - (Number) cell row index(start with 1) 

Example: Merge the first row as title from (1,1) to (5,1)

```javascript
sheet1.merge({col:1,row:1},{col:5,row:1});
```
## Sheet.autoFilter(filter_spec)
The argument may be a range (e.g. `"$A1:$J12"`) or `true` in which case the entire sheet domain is used as the range.


## Testing

There is a nascent Mocha test suite.
```
> npm test
```

A number of these tests currently writes output files and test for exact matches
against a reference file located in `test/files/`.

It's possible that a future feature extension might
break tests for innocent reasons (e.g. by writing additional XML to the workbook)
in which case, visually inspect the output file and update the reference file.

## Release notes

v0.2.0
* Write numbers as numbers
* Write fill and font colors using hex ranges (ported from https://github.com/aloteot/msexcel-builder and applied into coffeescript)
* Apply autofilters
* Add mocha testing


v0.1.0
* Generate JSZip object, dropping need to generate temporary files on disk.
* Removed dependency on `fs-extra` and `exec` and `easy-zip`.
* Added dependency on `js-zip`.
* Removed method `save` and replaced it with `generate(callback)` that returns a JSZip object.
* This now theoretically should be able to run in the browser, though that is not tested.
* Also refactored base Excel files so they are read from code rather than from disk.

v0.0.2:
* Switch compress work to easy-zip to support Heroku deployment.

v0.0.1: Includes

* First release.
* Using 7z.exe to do compress work, so only support windows now.
