#xlsx

xlsx是一个Excel 2007+(XLSX/XLSM/XLSB)文件的读写库。对于读，你可以直接调用xlsx的函数。对于写，你需要按照xlsx规定的格式将数据封装为对象，并传递给xlsx函数，执行后就会生成你要的内容。

##Cell对象和Ranage对象

Cell 指的是一个单元格。
Range 指的是一个区域的单元格。

一个Cell对象的存储方式`{c:C, r:R}`。C代表列，R代表行。行和列的起始索引为0，例如，B5单元格的表示方法是`{c:1, r:4}`。

一个Range对象的存储方式`{s:S, e:E}`。S代表开始的单元格，E代表结束的单元格。两个单元格中间包含的范围就是Range对象表达的范围。例如，A3:B7表示`{s:{c:0, r:2},e:{c:1, r:6}}`。

下面的代码表示遍历一个Range的所有Cell。

	for(var R = range.s.r; R <= range.e.r; ++R) {
	  for(var C = range.s.c; C <= range.e.c; ++C) {
	    var cell_address = {c:C, r:R};
	  }
	}



###Cell对象

	属性         含义

	 v         原始数据
	 t         数据类型  b  Boolean,  n  Number,  e  error,  s  String,  d  Date 


##Worksheet对象

每个不以!开始的键值都会表示为一个Cell。

worksheet[address] 返回address所指定的Cell对象。

特殊的键值:

+ ws['!ref']。 输入一个Range对象，表示worksheet的范围。
+ ws['!cols']。输入一个对象数组。表示各个列的宽度。
+ ws['!merges']。输入一个Range数组。表示所有要合并的位置。合并后的单元格坐标为左上角的坐标（行列最小值）。

```
width = Truncate([{Number of Characters} * {Maximum Digit Width} + {5 pixel padding}] /{Maximum Digit Width} * 256) / 256
```
##Workbook对象

workbook.SheetNames是一个有序的sheet合集。
wb.Sheets[sheetname] 返回一个worksheet对象。
wb.Props存储的是一些标准的属性。wb.Custprops存储的是自定义属性。



##Utils函数

utils.encode_cell函数，计算当前插入位置。例如：

	var cell_ref = XLSX.utils.encode_cell({c:0,r:0});

变量cell_ref保存的是A1。

---
#xlsx格式信息

OpenXML `http://msdn.microsoft.com/library/gg607163`