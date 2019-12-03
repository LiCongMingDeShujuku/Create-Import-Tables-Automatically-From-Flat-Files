![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 从平面文件自动创建导入表
#### Create Import Tables Automatically From Flat Files
**发布-日期: 2018年02月16日 (评论)**

![#](images/01-Create-Import-Tables-Automatically-From-Flat-Files.png?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
以下逻辑（logic）自动从平面文件创建导入表。系统是数字型的，并且使用名称[IMPORT00]创建表，并且对于执行的每个导入表将向上计数01,02,03。确保为平面文件添加正确的路径，并指定正确的值以获取正确的列。
它首先查看构建列的第一行单值。在这个例子中，第一行我以“Issue＃”开头，来识别列名。空字符串以逗号分隔的没有值的列将被删除。
下面是逻辑（logic）：



## English
The following logic automatically creates import tables from flat files. The system is numeric, and tables are created with the name [IMPORT00], and will upcount 01, 02, 03 for each import that is carried out. Be sure to add the proper path to your flat file, and designate the proper value to get the correct columns.

It first looks to the first single row of values to build your columns. In this example my first row betings with “Issue #” which I used to identify the column names. Columns that have no value where an empty string was separated by commmas “,,” is removed.
Here’s the logic:


---
## Logic
```SQL
use [compliance];
set nocount on
 
if object_id('tempdb..#import_file') is not null
    drop table  #import_file
create table    #import_file ([alldata] varchar(max))
 
bulk insert #import_file from 'C:\SQLIMPORTS\FY18Q1.csv' with (rowterminator = '\n', fieldterminator = ',')
 
declare @new_table varchar(255) = (select top 1 isnull([table_name], 'IMPORT00') from information_schema.tables where [table_name] like 'import%[0-9][0-9]' order by [table_name] desc)
select  @new_table = upper(left(@new_table, 6)) + format(cast(right(@new_table, 2) as int) + 1, '00')
 
declare @get_columns    varchar(max) = (select top 1 * from #import_file where [alldata] like 'issue #%')
declare @create_table   varchar(max) = (select  'create table [' + @new_table + '] ' +  char(10) + '    ' + '([' + replace(replace(@get_columns, ',,', ','), ',', ']            nvarchar(max)' + char(10) + ',    [') + '] nvarchar(max))')
exec    (@create_table)
 
declare @sql    varchar(255)    = ('select * from [' + @new_table + ']')
exec    (@sql)


```
One point. The logic creates the table; but does not automatically populate it. You’ll need to your import process (statement) to get the values across.
Here is an example of the build process which is run automatically.

要注意的一点是， 逻辑（logic）创建表，但不会自动填充表。 你需要使用导入程序（语句）来获取值。
以下是构建自动运行程序的示例。

![#](images/02-Create-Import-Tables-Automatically-From-Flat-Files.png?raw=true "#")

```SQL
use [compliance];
set nocount on
 
if object_id('tempdb..#import_file') is not null
    drop table  #import_file
create table    #import_file ([alldata] varchar(max))
 
bulk insert #import_file from 'C:\SQLIMPORTS\FY18Q1.csv' with (rowterminator = '\n', fieldterminator = ',')
 
declare @new_table varchar(255) = (select top 1 isnull([table_name], 'IMPORT00') from information_schema.tables where [table_name] like 'import%[0-9][0-9]' order by [table_name] desc)
select  @new_table = upper(left(@new_table, 6)) + format(cast(right(@new_table, 2) as int) + 1, '00')
 
declare @get_columns    varchar(max) = (select top 1 * from #import_file where [alldata] like 'issue #%')
declare @create_table   varchar(max) = (select  'create table [' + @new_table + '] ' +  char(10) + '    ' + '([' + replace(replace(@get_columns, ',,', ','), ',', ']            nvarchar(max)' + char(10) + ',    [') + '] nvarchar(max))')
exec    (@create_table)
 
declare @import_process varchar(max)
set     @import_process = (select 'bulk insert [' + @new_table + '] from ''C:\SQLIMPORTS\FY18Q1.csv'' with (rowterminator = ''\n'', fieldterminator = '','')')
exec    (@import_process)
 
declare @sql    varchar(255)    = ('select * from [' + @new_table + ']')
exec    (@sql)


```

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

