# 概述

apache-poi 是用于操作 office 文件的工具包，包括 excel ,word ,ppt 等等。

## excel

excel 由于历史原因，分为 xls 和 xlsx 两种格式。对应的处理类为 HSSFWorkbook 和 XSSFWorkbook。

前者是旧版本，数据量只支持到6w多，后者可支持 100w多，如果业务不需要兼容旧版本，推荐使用 xlsx。

如果需要支持 xlsx ,则需要 poi-ooxml ,会自动依赖 poi 。

```xml
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>4.1.2</version>
</dependency>
```

### 创建工作簿

创建工作簿 workbook，一个workbook相当于一个xlsx文件。

一个workbook至少需要一个 sheet,相当于表格

```java
Workbook wb = new XSSFWorkbook();
Sheet s1 = wb.createSheet();

// 写入文件
try (OutputStream os = new FileOutputStream("poi/a.xlsx")) {
    wb.write(os);
} catch (IOException e) {
    e.printStackTrace();
}
```

### 创建表格

每个 sheet 相当于一个表格，在 文件底部左侧显示。可以有多个sheet

```java
// 不指定名称，则使用默认名称 Sheet0 依次类推
Sheet s1 = wb.createSheet();
// 指定名称
Sheet s2 = wb.createSheet("s2");
// sheet名称不能包含部分特殊字符,不能超过31个字符，否则无法创建
// 可以使用WorkbookUtil创建安全的文件名，不合法的字符将被替换为' '
String sheetName = "a:\\*?/[]a";
String safeSheetName = WorkbookUtil.createSafeSheetName(sheetName);
Sheet s3 = wb.createSheet(safeSheetName);
```

### 创建单元格

通过 sheet 创建 行 row，通过 行 row 创建 每一行的单元格 cell，再给cell设置值。行和单元格的索引从 0 开始。

```java
Sheet s1 = wb.createSheet();
Row row1 = s1.createRow(0);
Cell cell1 = row1.createCell(0);
cell1.setCellValue(1);
```

也可以在一行链式调用

```java
s1.createRow(1).createCell(0).setCellValue(1);
```

可以设置多种数据类型，如double，string，boolean，富文本。

```java
Row row2 = s1.createRow(1);
// double
row2.createCell(0).setCellValue(1);
row2.createCell(1).setCellValue(1.22);
// string
row2.createCell(2).setCellValue("hello poi");
// boolean
row2.createCell(3).setCellValue(true);
// rich text
CreationHelper creationHelper = wb.getCreationHelper();
RichTextString richMan = creationHelper.createRichTextString("rich man");
row2.createCell(4).setCellValue(richMan);
```

还有多种时间类型

直接设置时间对象，不会有格式，直接以数值的形式显示

```java
Row row3 = s1.createRow(2);
row3.createCell(0).setCellValue(new Date());
```

需要通过设置单元格样式指定格式

```java
CreationHelper creationHelper = wb.getCreationHelper();
CellStyle cellStyle = wb.createCellStyle();
cellStyle.setDataFormat(creationHelper.createDataFormat().getFormat("m/d/yy h:mm"));

Cell row3Cell1 = row3.createCell(1);
row3Cell1.setCellValue(new Date());
row3Cell1.setCellStyle(cellStyle);
```

还可以设置 Calendar,LocalDateTime, LocalDate 等时间对象。

```java
Cell row3Cell2 = row3.createCell(2);
row3Cell2.setCellValue(Calendar.getInstance());
row3Cell2.setCellStyle(cellStyle);

Cell row3Cell3 = row3.createCell(3);
row3Cell3.setCellValue(LocalDateTime.now());
row3Cell3.setCellStyle(cellStyle);

Cell row3Cell4 = row3.createCell(4);
row3Cell4.setCellValue(LocalDate.now());
row3Cell4.setCellStyle(cellStyle);
```

### 读取工作簿

使用 WorkbookFactory 读取文件或流。可以是 xls 或 xlsx

```java
// 读取文件
Workbook wb = WorkbookFactory.create(new File("poi/a.xlsx"));
// 读取流，需要更多的内存
Workbook wb2 = WorkbookFactory.create(new FileInputStream("poi/a.xlsx"));
```

#### 遍历

遍历父元素，可以得到子元素。可以使用 for-each。

但是会跳过空行和空单元格。

```java
for (Sheet sheet : wb) {
    for (Row row : sheet) {
        for (Cell cell : row) {
            String cellValue = cell.getStringCellValue();
            System.out.println(cellValue);
        }
    }
}
```

如果需要控制所有行，则需要手动遍历

获取单元格的时候可以指定策略，用于区分 Blank 和 null

```java
Sheet sheet = wb.getSheetAt(0);
// 从第一条数据所在行开始，到最后一条数据进行遍历
int firstRowNum = sheet.getFirstRowNum();
int lastRowNum = sheet.getLastRowNum();

for (int i = firstRowNum; i <= lastRowNum; i++) {
    Row row = sheet.getRow(i);
    if (row != null) {
        int lastCellNum = row.getLastCellNum();
        for (int y = 0; y <= lastCellNum; y++) {
            Cell cell = row.getCell(y);
            //Cell cell = row.getCell(y, Row.MissingCellPolicy.CREATE_NULL_AS_BLANK);
            if (cell != null) {

            } else {
                System.out.println("第" + (i + 1) + "行，第" + (y + 1) + "列为空值");
            }
        }
    } else {
        System.out.println("第" + (i + 1) + "行为空行");
    }
}
```

#### 获取单元格内容

#### 



### 对齐与样式

#### 行高

以点为单位设置的行高，设置为 -1 则为默认值

```java
row1.setHeightInPoints(50);
```

以缇为单位设置行高，更精确 。1 缇 = 1/20 个点

```java
row1.setHeight((short) 500);
```

设置 0 高度

```java
row1.setZeroHeight(true);
```

#### 对齐

通过样式设置 alignment 设置左右对齐（水平）。verticalAlignment设置上下对齐（垂直）。

```java
Cell cell = row1.createCell(0);
cell.setCellValue("hello");

CellStyle cs = wb.createCellStyle();
cs.setAlignment(HorizontalAlignment.CENTER);
cs.setVerticalAlignment(VerticalAlignment.BOTTOM);

cell.setCellStyle(cs);
```

#### 边框

可以分别给上下左右四个边框设置样式和颜色。

```java
Row row = s1.createRow(2);
Cell cell = row.createCell(2);
cell.setCellValue("hello");

CellStyle cs = wb.createCellStyle();
cs.setBorderBottom(BorderStyle.THIN);
cs.setBottomBorderColor(IndexedColors.AQUA.index);
cs.setBorderTop(BorderStyle.DASH_DOT);
cs.setTopBorderColor(IndexedColors.GREEN.index);
cs.setBorderRight(BorderStyle.DOUBLE);
cs.setRightBorderColor(IndexedColors.RED.index);
cs.setBorderLeft(BorderStyle.HAIR);
cs.setLeftBorderColor(IndexedColors.BLUE.index);

cell.setCellStyle(cs);
```

### 填充和颜色

### 合并单元格

### 使用字体

### 修改工作簿

### 单元格中使用换行

