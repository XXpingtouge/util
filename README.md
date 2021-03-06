# 工作中常用的自定义工具整合

> 日常工作中整理的一些经常使用的方法。
>
> 有可能不是特别通用，因为是工作中抽取的，可能会有一点业务逻辑在里面。
>
> 不定期更新方法，不定期添加类。
>
> 此次迁移主要是整理了代码风格与包名结构，并写了具体的测试类，以及重写了说明文档

##  **说明**

***注释采用的是方法注释，但举例使用的是测试用例。请勿吐槽。***

## 功能列表

### 字符串相关

######  获取去掉连字符的UUID

```java
String uuid = UtilString.randomUUID();
```
######  清理字符串中的HTML标签

```java
String cleanStr = UtilHtml.cleanHtmlLabel(htmlStr);
```
######  生成加密盐工具

```java
// 默认4位随机数
String randomSalt = UtilString.randomSalt();
// 指定位数随机数
String randomSalt = UtilString.randomSalt(6);
```

### Json字符串与xml字符串的转换

######  Json串转xml

```java
String jsonStr = UtilJsonAndXml.jsonToXml(json);
```
###### xml转Json串

```java
String xmlStr = UtilJsonAndXml.XmlToJson(xml);
```

### 日期相关

#### 日期转换

###### 字符串转换java.util.Date

```java
// 默认转换方式 yyyy-MM-dd HH:mm:SS
Date date = UtilDate.stringToJavaUtilDate("2018-01-24 18:54:58");
// 指定转换方式
Date date = UtilDate.stringToJavaUtilDate("2018-01-24","yyyy-MM-dd");
```
###### java.util.Date转字符串

```java
// 默认转换方式 yyyy-MM-dd HH:mm:SS
String dateStr = UtilDate.javaUtilDateToString(new Date());
// 指定转换方式
String dateStr = UtilDate.javaUtilDateToString(new Date(), "yyyy:MM:dd HH-mm-sss");
```
###### java.sql.Date转java.util.Date

```java
java.sql.Date sqlDate = UtilDate.sqlDateToUtilDate(new java.sql.Date(11111232));
```
###### java.util.Date转java.sql.Date

```java
java.util.Date utilDate = UtilDate.javaUtilDateToSqlDate(new Date());
```
###### DateTime转java.sql.Date

```java
// 这个方法本来是用于Mybatis自定义插件的，但是插件没有什么实际使用场景，故此方法没什么实际用途
java.sql.Date sqlDate = UtilDate.dateTimeToSqlDate(DateTime.now());
```
#### 文件相关

##### PDF

###### PDF文件转image图片

```java
/**
 * PDF文件转image图片
 * 
 * 参数一 pdf文件路径
 * 参数二 把转换的图片保存在哪里(路径)
 */
String filePath = UtilPDF.pdfTransformImg("xxxx.pdf","xxxxx/xxxx");
```
##### 文件操作

###### 复制文件

```java
/**
 * 2018-01-25 更新
 * 参数一 原文件
 * 参数二 目标文件全路径
 */
boolean copyFileStatus = UtilFile.copyFileToDirectory(file,filePath);
```

###### 删除文件

```java
boolean deleteStatus = UtilFile.deleteFile("/Users/xuan/Desktop/a.txt");
```
###### 获取唯一文件名

```Java
/**
 * 参数 原文件名
 */
String fileName = UtilFile.uniqueFileName(originalFilename);

/**
 * 2018-01-25 更新 
 * 参数一 原文件名
 * 参数二 是否保留原文件名 true:保留 false:不保留
 */
String fileName = UtilFile.uniqueFileName(originalFilename,true);
```
#### EXCEL表格文件

##### 导入excel

##### 导入Excel表格并生成对应实体类的集合

```java
	/**
     * 导入Excel表格并生成对应实体类的集合
     * <b>[强制条件] Excel表头必须与实体类的属性名称一致</b>
     * 注：
     * 1.由于效率问题,当前暂支持一个工作薄的导入
     * 2.表头必须以 (中文名-实体对象属性名)为头。否则导入失败
     *
     * @param excelFile  Excel表格文件 支持.xls .xlsx
     * @param clazz 映射的实体类类型
     *
     * @return 实体集合
     *
     * @throws StreamException      文件读取异常
     * @throws FileException        文件异常
     * @throws StreamCloseException 流关闭异常
     */
List<T> list = UtilExcelImport.importExcel(excelFile, clazz);
```

##### 导入一个Excel文件

```java
/**
     * 导入一个Excel文件
     * <b>
     * Excel文件中若有日期格式的单元格，
     * 必须对此单元格的表头添加批注，否则日期将以天数记录
     * </b>
     *
     * @param file              Excel文件
     * @param isNeedTableHeader 是否需要表头 true:需要表头 false:不需要表头
     *
     * @return 文件中所有Sheet数据封装成的Json数据数组。数组以Sheet为单位
     */
String[] strings = UtilExcelImport.importExcel(excelFile,isNeedTableHeader);
```

##### 从Excel中导入数据到实体集合中

```java
/**
     * 从Excel中导入数据到实体集合中
     * <b>
     * 要求：
     * 1. 实体类字段属性顺序需与Excel文件中列顺序一致，排除serialVersionUID字段后，数量应与列数量一样多
     * 2. 实体类对象数组顺序需与Excel中Sheet表中保存的数据对应的对象保持一致。
     * </b>
     *
     * @param file             Excel文件
     * @param targetClassArray 要导入的实体类对象数组
     *
     * @return 实体集合对象
     *
     * @throws FileException                  文件找不到异常
     * @throws ParamException                 入参不正确异常
     * @throws FileTypeException              文件类型异常
     * @throws IOException                    IO异常
     * @throws ArrayIndexOutOfBoundsException 传入实体类数组长度与文件中工作表长度不一致
     */
Object[] objects =  UtilExcelImport.importExcelIntoEntity(excelFile,new Class[]{});
```

##### 导出excel

```java
/**
 * 导出到Excel表方法
 * 
 * 参数一 表头 可为空 若为空则为实体类中文意义
 * 参数二 表中的数据
 * 参数三 工作薄名 若为空 则默认为WookBook
 * 参数四 实体类类型
 * 参数五 完成的文件名 （文件全路径 + 文件名）
 * 返回值 生成的文件名
 */
String fileName = UtilExcelExport.UtilExcelExport();

/**
 * 导出到Excel表方法
 * 
 * 参数一 表头 可为空 若为空则为实体类中文意义
 * 参数二 表中的数据
 * 参数三 工作薄名 若为空 则默认为WookBook
 * 参数四 实体类类型
 * 返回值 生成的XSSFWorkBook对象, 可以以流的形式输出
 */
XSSFWorkbook xssfWorkBook = UtilExcelExport.UtilExcelExport();
```
##### MultipartFile 文件上传

```java
/**
 * 参数一 待上传文件
 * 参数二      文件保存目录 全路径
 * 返回值 将要保存的服务器文件名
 */
String fileName = UtilFile.uploadFile(multipartFile, filePath);
```

​

## 提交日志

### 2018-04-25

1. 新增多工作表导入方法，返回结果为json字符串数组。要求工作表必须有表头，如果表头中含有时间类型的数据列，要求在此列表头添加批注，批注内容包含date或者日期即可，否则时间将以1990年1月1日起到时间列的天数返回。
2. 修改单元格数据获取工具类，添加日期单元格的判断。
3. 添加多工作表导入方法。此方法支持导入数据后自动封装成实体对象集合。且，此对象可以有父类关系。**要求：Excel文件中sheet表必须有表头，否则会丢失一行数据**
4. 将以流形式读入工具的方法私有化。提供一个公共的入口方法

### 2018-03-23

1. 修改单元格数据转换成实体时的逻辑。
   1. 实体类若有java.util.Date类属性时，对应单元格格式应已"yyyy-MM-dd"的方式输入，否则将出现异常。
   2. 实体类中如果有java.lang.Byte类属性时，对应单元格数据应不大于byte取值范围。

### 2018-02-01

1. 修正实体类有父类时，父类属性设置失败问题。
2. 修正单元格中有空格时导致的数据丢失问题。

    ps: 目前只支持一层继承关系。

### 2018-01-25

1. 添加新方法：
   1. 复制文件：UtilFile.copyFileToDirectory(File file, String filePath) {
   2. 获取唯一文件名：UtilFile.uniqueFileName(String originalFilename, boolean keepFileName);
2. 清理代码。

### 2018-01-24：

  1. 整合旧资源库代码至新资源库。
  2. 更新项目名，包名，类名。
  3. 删除方法：
    1. 获取服务器文件名：FileUtil.getServerFileName(fileName,hasChar);
    2. 获取分页第一条序列：PackingUtils.getFirstPageNum(pageNum,pageSize);
    3. 从请求路径中获取http://ip:port/porjectName：ServletTools.getDomainAndContextPath(request);

