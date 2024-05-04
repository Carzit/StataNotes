# Stata Notes

## Configs
### sysdir 
显示Stata各相关文件夹路径，包括`STATA` `BASE` `SITE` `PLUS` `PERSONAL` 和 `OLDPLACE`。  
使用 `sysdir set folder_name folder_path`来指定相关文件夹的路径 
### memory 
显示目前存储空间  
### query memory 
查看目前实际设定的存储空间  
### set memory
设定存储空间的大小  
### set matsize  
设定最大矩阵阶数  
### set maxvar 
设定最大变量数（最小设定为2048）  
### help limits 
显示Stata的各种极限

## SSC相关
### ssc new
显示 SSC 最近新增或更新的软件包摘要。

### ssc hot
显示 SSC 最受欢迎的软件包摘要。

### ssc describe pkgname
显示 SSC 中名为`pkgname`的软件包的介绍。

### ssc install pkgname [, all replace]
下载 SSC 中名为`pkgname`的软件包
- `all` 指定除了正在安装的程序和帮助文件之外，还将与包关联的任何辅助文件下载到当前目录。辅助文件是不以 .ado 或 .sthlp 结尾的文件，通常包含数据集或新命令的使用示例。
- `replace` 允许覆盖。否则如果已存在同名软件包，则会拒绝下载。  

### ssc uninstall pkgname
卸载名为`pkgname`的软件包

## 数据的输入与输出
### set obs n 
设置样本量为n。  
若clear则为创建n条空数据，若非clear则n不得小于原样本量  

### input [type] [varlist] [, automatic, label]
手动输入数据。
- `type`指定输入变量的数据类型，仅在指定`varlist`时可用。
- `varlist`可指定从第1行输入的各个新变量名，注意不能存在已有变量名。varlist未指定时，则从表最后1行开始输入已有变量的各个值。  
- `automatic`可启用自动为输入的非数字数据创建值标签。  
- `label`可让用户输入标签而不是为与值标签相关变量输入值？

### edit
打开数据编辑器

### sysuse filename [if exp] [in range] [, clear nolable]
导入`sysdir`设置的`BASE`路径对应目录下的内置数据

### use [varlist] [if exp] [in range] using filename [, clear nolable]
导入`filename`路径对应的stata数据  
- `varlist`可指定导入指定的变量名（列）  
- `if exp`可指定导入符合条件表达式的样本（行）  
- `in range`可指定导入符合范围的样本（行）  

### import excel [varlist] using filename [, sheet() firstrow clear]
`varlist`可指定导入指定的变量名（列）  
导入`filename`路径对应的excel数据  
- `sheet()`可指定读取的sheet名称，否则默认读取第一张sheet  
- `firstrow`可指定第一行为变量名，否则以excel形式的"A", "B", ...形式对变量命名

### import delimited [varlist] using "filename.csv" [, delimiter() encoding() clear]
导入`filename`路径对应的文本分隔数据（如csv）   
- `varlist`指定导入指定的变量名（列）   
- `delimiter`指定csv文件的分隔符  
- `encoding`指定csv文件的编码  
- `nonames`不使用第一行作为变量名  

### save filename [, nolabel replace all orphans emptyok]
将内存中的数据保存为.dta格式数据
> - nolabel omits value labels from the saved dataset.  The associations between variables and value-label names, however, are saved along with the dataset label and the variable labels.  
> - replace permits save to overwrite an existing dataset.  
> - all is for use by programmers.  If specified, e(sample) will be saved with the dataset.  You could run a regression; save mydata, all; drop _all; use mydata; and predict yhat if e(sample).  
> - orphans saves all value labels, including those not attached to any variable.  
> - emptyok is a programmer's option.  It specifies that the dataset be saved, even if it contains zero observations and zero variables.  If emptyok is not specified and the dataset is empty, save responds with the message "no variables defined".  

### export excel [varlist] using filename [, sheet() firstrow nolabel replace]
将内存中的数据保存为到`filename`路径对应的excel文件
- `sheet`指定保存的sheet名称  
- `firstrow`指定保存的excel第一行为变量名或变量标签
- `nolabel`对于已被打上值标签的变量，不以值标签保存而是以原本的数值进行保存
- `replace`允许覆盖同名已有文件  

### export delimited [varlist] using filename [, delimiter() novarnames nolabel replace]
将内存中的数据保存为到`filename`路径对应的文本分隔数据文件（如csv）   
`varlist`可指定变量名（列）进行保存。
- `delimiter()`指定文本分隔符
- `novarnames`在第1行不保存变量名（列名）  
- `nolabel`对于已被打上值标签的变量，不以值标签保存而是以原本的数值进行保持
- `replace`允许覆盖同名已有文件  

## 数据的合并
### merge

### append


## 数据的概览与描述

### count [if] [in]
统计样本（行）数。
可使用`if` `in` 进行条件约束。

### list [varlist] [if] [in] [,nolabel means(varlist) sum(varlist) N(varlist) show_options]
列出数据。
- `nolabel`显示时不以值标签显示，而以真实数值显示。
- `means()`可额外列出当前列表中指定变量名（列）的平均值    
- `sum()`可额外列出当前列表中指定变量名（列）的和  
- `N()`可额外列出当前列表中指定变量名（列）的非缺失值的个数

### describe [varlist] [, simple short fullnames numbers]
展示数据文件路径、数据标签、样本数、变量数、排序根据、指定变量（列）的变量名、变量标签、变量数据类型、显示格式、值标签。  
注意不可使用`[if]` `[in]`进行切片。  
- `varlist`指定时将不显示显示数据文件路径、数据标签、样本数、变量数、排序根据  
- `simple`仅显示变量名  
- `short`仅显示数据文件路径、数据标签、样本数、变量数、排序根据  
- `fullnames`显示时不因过长而省略变量名
- `numbers`显示时在变量名前附上变量的序号  

### summarize [varlist] [if] [in] [weight] [, detail]
展示所列数据中各变量的的有效样本数(Obs.)、均值(Mean)、标准差(Std. dev.)、最小值(Min)、最大值(Max)。  
- `detail`额外显示方差(Variance)、偏度(Skewness)、峰度(Kurtosis)、 1%/5%/10%/25%/50%/75%/90%/95%/99%的分位数、  1%/5%/10%/25%区间内的最小值 和 75%/90%/95%/99%区间内的最大值。   

### codebook [varlist] [if] [in] [weight] [, all header notes tabulate(#) problems detail compact]
- `all`显示除了缺失值外的所有报告，相当于同时使用`header`和`notes`。
- `header`额外显示数据集的名称和最后保存的日期。
- `notes`显示对各变量标注的所有注释。
- `tabulate()`指定用于确定变量是分类变量还是连续变量的不同值的数量。缺失值不包含在此计数中。默认为9。当不同值超过`#`个时，该变量被归类为连续变量。扩展缺失值将包含在表格中。
- `problems`问题报告。指定在表格末生成一个摘要报告，描述已诊断的潜在问题，包括： 
- - 标有未定义值标签的变量
- - 值标记不完整的变量
- - 常量变量，包括始终缺失的变量
- - 前导、尾随和嵌入空格字符串变量
- - 字符串变量中嵌入二进制 0 (\0)
- - 非整数值日期变量


### inspec


### tabstat varlist [if] [in] [weight] [, by() statistics(statnames)]
以表格形式显示指定的统计量。  
注意必须指定`varlist`,且`varlist`中不包含字符串数据格式的变量。    
- `by()`可指定变量名分组输出统计量，与一般的`by varname:`用法类似，但不需预先`sort`   
- `statistics()`指定输出的统计量`statnames`。可用的`statnames`如下：  

| StatName |      Definition      |
|:--------:|:--------------------:|
|   mean   |          平均值         |
|  count   |      无均值的观测值的计数      |
|    n     |      无均值的观测值的计数      |
|   sum    |         加和的值         |
|   max    |          最大值         |
|   min    |          最小值         |
|  range   |    范围，即`max`-`min`   |
|    sd    |          标准差         |
| variance |          方差          |
|    cv    |   变异系数，即`sd`/`mean`  |
|  semean  |   均值的标准误，即`sd`/`√n`  |
| skewness |          偏度          |
| kurtosis |          峰度          |
|    p1    |         1%分位数        |
|    p5    |         5%分位数        |
|   p10    |        10%分位数        |
|   p25    |        25%分位数        |
|  median  |      中位数，即50%分位数     |
|   p50    |        50%分位数        |
|   p75    |        75%分位数        |
|   p90    |        90%分位数        |
|   p95    |        95%分位数        |
|   p99    |        99%分位数        |
|   iqr    |  四分位间距，即`p75`-`p25`  |
|    q     | 相当于指定`p25` `p50` `p75` |

- `labelwidth()` 指定用于显示 `by()` 变量标签的最大宽度为`#`,8 ≤ `#` ≤ 32。默认值为16。 
- `varwidth()` 指定用于显示变量名称的最大宽度,8 ≤ `#` ≤ 32。。默认值为12。 
- `columns()` 指定列显示模式。`columns(variables)`以变量为列；`columns(statistics)`以统计量为列。不指定时，默认为`columns(variables)`。
- `format[()]` 指定显示格式。 使用`format`时，在表格中的显示格式与原变量一致；使用`format(%fmt)`以指定对表格中所有  
- `nototal`无汇总。指定不报告总体统计数据。 只有当使用`by()`时才能使用；否则，不使用`by()`的场合只有总体统计数据，`notatal`将不会显示任何数据。
- `missing` 将`by()`指定变量的缺失值像任何其他值一样处理，并且显示它们的统计信息。默认情况下不报告`by()`缺失组的统计信息。如果`by()`变量是字符串变量，则`by()=="" `被认为意味着缺失。
- `nosparator`无分割线。指定不显示`by()`类别之间的分隔线。  
- `longstub`指定表的左单元格变宽，以便除了 `by(varname)` 的类别之外，它还可以包含统计信息或变量的名称。如果未指定 `by(varname)`，则忽略 `longstub`。如果指定`varwidth()`，则自动启用`longstub`
- `save` 指定在 `r()` 中返回汇总统计信息。总体（无条件）统计数据在矩阵 `r(StatTotal)` 中返回（行是统计数据，列是变量）。条件统计数据在矩阵 `r(Stat1)`, `r(Stat2)`, ...中返回，并在宏 `r(name1)`, `r(name2)`, ...中返回相应变量的名称。


> 样式参考
> 
> ```
> 列变量-短单元-无分隔线格 样式
> 
>             make |     price       mpg  displa~t
> -----------------+------------------------------
>      AMC Concord |      4099        22       121
>                  |         .         .         .
>                  |      4099        22       121
>        AMC Pacer |      4749        17       258
>                  |         .         .         .
>                  |      4749        17       258
>       AMC Spirit |      3799        22       121
>                  |         .         .         .
>                  |      3799        22       121
> -----------------+------------------------------
>            Total |  4215.667  20.33333  166.6667
>                  |  485.6267  2.886751  79.09699
>                  |      3799        17       121
> ------------------------------------------------
> 
> 列变量-长单元 样式
> 
> make                Stats |     price       mpg  displa~t  
> --------------------------+------------------------------  
> AMC Concord          Mean |      4099        22       121  
>                        SD |         .         .         .  
>                        p1 |      4099        22       121  
> --------------------------+------------------------------  
> AMC Pacer            Mean |      4749        17       258  
>                        SD |         .         .         .  
>                        p1 |      4749        17       258  
> --------------------------+------------------------------  
> AMC Spirit           Mean |      3799        22       121  
>                        SD |         .         .         .  
>                        p1 |      3799        22       121  
> --------------------------+------------------------------  
> Total                Mean |  4215.667  20.33333  166.6667  
>                        SD |  485.6267  2.886751  79.09699  
>                        p1 |      3799        17       121  
>
> 列统计量-长单元 样式
> 
> make             Variable |      Mean        SD        p1
> --------------------------+------------------------------
> AMC Concord         price |      4099         .      4099
>                       mpg |        22         .        22
>                  displa~t |       121         .       121
> --------------------------+------------------------------
> AMC Pacer           price |      4749         .      4749
>                       mpg |        17         .        17
>                  displa~t |       258         .       258
> --------------------------+------------------------------
> AMC Spirit          price |      3799         .      3799
>                       mpg |        22         .        22
>                  displa~t |       121         .       121
> --------------------------+------------------------------
> Total               price |  4215.667  485.6267      3799
>                       mpg |  20.33333  2.886751        17
>                  displa~t |  166.6667  79.09699       121
> ---------------------------------------------------------
> 
> ```


### tab1 varlist [if] [in] [weight] [, subpop(), missing, nofreq, nolabel, plot, sort]
对`varlist`中的每个变量，以每个不同的值为类别，生成单个分类变量的汇总表格。显示频数(Freq.)、百分比(Percent)和累计百分比(Cum.)
- `subpop()`不显示指定变量中频数为0的类别
- `missing` 将变量的缺失值像任何其他值一样处理。
- `nofreq`禁用频数统计。不显示频数的统计。
- `nolabel`禁用标签显示。对于存在指定值标签映射集的变量，不以值标签显示，而是以真实数值显示。
- `plot`对频数统计结果附带星点图。注意将取消百分比和累计百分比统计量的显示。
- `sort`将表的呈现顺序按频数大小降序排列

### tab2 varlist [if] [in] [weight] [,stat_options missing, nofreq, nolabel, firstonly]
对`varlist`中的每个变量，两两组合，生成两个分类变量的列联表分析。显示频数(Freq.)。

- `stat_options`:

| options  |     Definition     |
|:--------:|:------------------:|
|   chi2   |    皮尔逊卡方统计量 χ2     |
| exact(#) |     费舍精确检验统计量      |
|  gamma   |   古德曼-克里斯卡尔 伽马值    |
|  lrchi2   |      卡方似然比统计量      |
|   taub   | Kendall's tau-b统计量 |
|    V     |    Cramer V统计量     |
|  cchi2   |  在每个单元格报告皮尔逊卡方统计量  |
| clrchi2 | 在每个单元格报告卡方似然统计量 |
| cell | 在每个单元格报告单元格在全部样本中的百分比 |
| expected | 在每个单元格报告单元格的期望频数 |

- `missing` 将变量的缺失值像任何其他值一样处理。
- `nofreq`禁用频数统计。不显示频数的统计。
- `nolabel`禁用标签显示。对于存在指定值标签映射集的变量，不以值标签显示，而是以真实数值显示。
- `firstonly`仅显示包含`varlist`中第一项变量的双向表。

### tabulate varname1 [varname2] [options]
指定`varname1`时，表现即为`tab1`；同时指定`varname1`和`varname2`时，表现即为`tab2`。

### table (rowspec) (colspec) [(tabspec)] [if] [in] [weight] [, options]
多维列联表分析。

### ci [means/variances/proportions] [varlist] [if] [in] [weight] [, options]
>待写

## 值分组修饰符
### by varlist [, sort rc0]: stata_cmd
以`varlist`的值为依据进行分组，对`varlist`中变量值相同的每组观测值，重复`stata_cmd`命令。 
- `sort`自动排序。不使用`sort`时,数据必须以`varlist`顺序排序（显式运行`sort varlist`）
- `rc0`错误抛出。指定即使`stata_cmd`在其中一个by-group中产生错误，那么`by`仍然对剩余的by-group运行`stata_cmd`。当不使用`rc0`时，默认操作发生错误时停止。当 stata cmd 是估计命令并且某些分组的观测值不足时，rc0 特别有用。

### bysort varlist [, rc0]: stata_cmd
以`varlist`的值为依据进行分组，对`varlist`中变量值相同的每组观测值，重复`stata_cmd`命令。   
自动排序，相当于使用`sort`的`by`。
- `rc0`错误抛出。指定即使`stata_cmd`在其中一个by-group中产生错误，那么`by`仍然对剩余的by-group运行`stata_cmd`。当不使用`rc0`时，默认操作发生错误时停止。当 stata cmd 是估计命令并且某些分组的观测值不足时，rc0 特别有用。


## 标签操作
### label data ["label"]
修改数据集标签，"label"不填则为空

### label variable varname ["label"]
修改变量`varname`标签，"label"不填则为空

### label define lblname # "label" [ # "label" ... ] [, add modify replace nofix]
定义值标签映射集，以`lblname`为映射名称（标识）， `#`为真实值 `"label"`为对应标签，可以如此定义多条。  
定义`"labelname"`为`""`时，该条映射为空，不存在。常用于`modify`模式中在某个值映射集中删除某条值映射规则。   
- `add`启用添加，当`lblname`为已有的值标签映射集名称时，允许向其中添加未定义的值标签映射；但不允许修改已定义的值标签映射  
- `modify`启用修改，当`lblname`为已有的值标签映射集名称时，允许修改已定义的值标签映射，同时也允许向其中添加未定义的值标签映射（`modify`包含`add`）  
- `replace`启用替换，当`lblname`为已有的值标签映射集名称时，此次定义内容将覆盖原值标签映射   
- `nofix`防止加宽，当`lblname`为已有的值标签映射集名称时，阻止新定义的值标签映射使原显示格式加宽

### label values varlist [lblname] [, nofix]
对指定变量`varlist`应用以`lblname`为标识的值标签映射集，`lblname`不填则为空

### label dir
显示已有的所有的值标签映射名称集

### label list [lblnames]
显示已有的所有的值标签映射集中所有的值标签映射集，指定`lblname`时仅显示该指定值标签映射集

### label copy lblname_old lblname_new [, replace]
将以`lblname_old`为标识的值标签映射集复制给`lblname_new`。  
注意`lblname_old`必须为已存在的值标签映射集名称，不使用`replace`时`lblname_new`必须为新的值标签映射集名称。
- `replace`启用替换，当`lblname_new`为已有的值标签映射集名称时，允许拷贝`lblname_old`覆盖原值标签映射  

### label drop lblnames
删除以`lblnames`为标识的值标签映射集
特别地，可指定`lblnames`为`_all`来删除所有值标签映射集

### label save [lblnames] using filename [, replace]
将以`lblnames`为标识的值标签映射集保存到do文件`filename`中，`lblnames`未指定时保存所有。  
- `replace`启用替换，当`filename`为已有的文件名称时，允许覆盖原文件




## 数据类型转换
|  Type  |      Minimum       |      Maximum      | Closest to 0 |   Bytes    |
|:------:|:------------------:|:-----------------:|:------------:|:----------:|
|  byte  |        -127        |        100        |      1       |     1      |
|  int   |     -3.2767e+4     |     3.2740e+4     |      1       |     2      |
|  long  |  -2.147483647e+9   |  2.147483620e+9   |      1       |     4      |
| float  | -1.70141173319e+38 | 1.70141173319e+38 |    1e-38     |     4      |
| double | -8.9884656743e+307 | 8.9884656743e+307 |    1e-323    |     8      |
|  str#  |         /          |         /         |      /       | 最大长度为#的字符串 |
> ### 类型与颜色
> 对于一般颜色默认配置，无标签的数值类型以 **黑色/白色** 显示，打上数值映射标签的以标签显示的数值类型以 **蓝色/蓝色** 显示，字符串类型以 **红色/黄色** 显示。  
> （前者为白色配置，后者为深色配置）


### recast type varlist [, force]
将变量名为`old_varlist`的各变量转换为指定数据类型`type`，原地操作。  
注意该方法似乎不能用于字符串与数值数据之间的转换。  
- `force`强制转换。适用于将`double`类型转换为`float`类型,可能造成舍入误差及不能转换带来的缺失值。

### encode old_varname [if] [in], generate(new_varname) [label(lblname) noextend]
将变量名为`old_varname`的字符串变量转换为数值变量。  
- `generate()`必须填写，`new_varname`为指定新生成的数值变量的变量名。  
- `label()`指定要创建或使用的值标签的名称，如果指定的值标签已存在，则添加到该值标签。如果未指定 label()，则编码将使用与新变量相同的标签名称。  
>注意encode创建的值标签映射的键从1开始，值从第一个样本开始；若键或值已被定义，则不覆盖直接进入下一个进行创建。
- `noextend`不对值标签映射进行拓展。在指定`lblname`时，如果`varname`中包含的值不存在于`lblname`中，则不对`varname`进行编码。否则，默认情况下，标签（名称）中不存在的任何值都将添加到该标签中。

### decode old_varname [if] [in] , generate(new_varname) [maxlength(#)]
将变量名为`old_varname`的数值变量转换为字符串变量。转换的映射规则由`old_varname`的值标签指定。  
- `generate()`必须填写，`new_varname`为指定新生成的字符串变量的变量名。  
- `maxlength(#)`指定新生成的字符串变量的最大长度。  

### tostring old_varlist, generate(new_varlist) [tostring_options]
将变量名为`old_varlist`的各数值变量转换为各字符串变量。 
- `generate()`必须填写，`new_varlist`为指定新生成的字符串变量的各变量名。  
特别地，如果不希望另外创建新变量，希望在原有变量上原地修改，可以使用`tostring old_varlist, replace`

### destring old_varlist, generate(new_varlist) [destring_options]
将变量名为`old_varlist`的各字符串变量转换为各数值变量。 
注意对于使用该方法的字符串变量，必须为可强制转换为数值的形式，如 1.345e+10 或 12345   
- `generate()`必须填写，`new_varlist`为指定新生成的数值变量的各变量名。
特别地，如果不希望另外创建新变量，希望在原有变量上原地修改，可以使用`destring old_varlist, replace`

## 数据显示格式
### 数值类型数据  
`%` `[Align Identifier]` `[LeadingZero Identifier]` `[TotalDigits Number]` `.` `[After-decimal-point Digits Number]` ` [Format Indentifier][Comma Indetifier]`

| Indentifier Type | Sign |            Description            |
|:----------------:|:----:|:---------------------------------:|
|      Align       |  -   |                左对齐                |
|      Align       |  空   |                右对齐                |
|   LeadingZero    |  0   |       缺少的总数位长度改为从前补0，仅适用于f        |
|   LeadingZero    |  空   |              不作补0处理               |
|      Format      |  g   | 普通显示格式，对于小数点后全为0的数位会省略,尽量多得显示有效数位 |
|      Format      |  f   |      固定显示格式，对于小数点后全为0的数位会保留       |
|      Format      |  e   |              指数显示格式               |
|      Comma       |  c   |          对于整数，每3位加一`,`分隔          |
|      Comma       |  空   |              不进行逗号分隔              |

### 数值类型数据  
`%` `[Align Identifier]` `[MaxLength Number]` `s`

| Indentifier Type | Sign |            Description            |
|:----------------:|:----:|:---------------------------------:|
|      Align       |  -   |                左对齐                |
|      Align       |  空   |                右对齐                |
|      Align       |  ~   |                居中对齐，仅能用于display                |




## 变量操作
### rename old_varname new_varname [, options]
重命名变量名。  
注意该命令不支持批量操作。  

### sort varlist [if] [, stable]
以`varlist`为索引变量升序排序。  
对于数值变量，由小到大排序；对于字符变量将逐个字符进行比较。  
一些浅显的经验表明 `空字符`<`符号字符`<`数字`<`大写字母`<`小写字母`  
- `stable` 指定 `varlist` 中具有相同变量值的观测值在排序数据中保持与之前相同的相对顺序。  

> 例如，考虑以下数据  
> 
> | X | Y |
> |:-:|:-:|
> | 3 | 1 |
> | 1 | 2 |
> | 1 | 1 |
> | 1 | 3 |
> | 2 | 4 |
>
> 在`sort X`的情况下，因为排序方式未指定变量`Y`，对于多个相同的变量值`X`=1，对应的行会随机排列，结果不唯一。  
> 一种方案是额外指定`Y`，命令为`sort X Y`，此时结果为
>
> | X | Y |
> |:-:|:-:|
> | 1 | 1 |
> | 1 | 2 |
> | 1 | 3 |
> | 2 | 4 |
> | 3 | 1 |
> 
> 另一种方案是使用stable以保持对于相同的索引变量，对应行保持原数据排列顺序。`sort X, stable`，此时结果为
> 
> | X | Y |
> |:-:|:-:|
> | 1 | 2 |
> | 1 | 1 |
> | 1 | 3 |
> | 2 | 4 |
> | 3 | 1 |

### gsort [+|-] varlist [, generate(new_varname) mfirst]
以`varlist`为索引变量升序排序。  
对于`varlist`中每个变量，变量名前均可分别指定`+`指示升序（从小到大）或`-`指示降序（从大到小）。  
- `generate()`指定以`new_varname`为变量名，额外生成从1开始的一列序数变量。
- `mfirst`缺失值优先。在不启用时，无论升序排列或逆序排列，缺失值都会在非缺失值之后；启用后，逆序排列时，缺失值将会在非缺失值之前（符合缺失值本身大于任何数值的特性）。

### order varlist [, first last before(varname) after(varname) alphabetic] 
移动`varlist`各变量到指定位置（列顺序变换）   
注意，`aorder`和`move`命令已过时，其功能已被`order`完全取代。
- `first` 移到最前。在不指定options时，默认使用`first`
- `last` 移到最后
- `before()` 移到指定的`varname`前
- `last()` 移到指定的`varname`后
- `alphabetic` 按变量名的字母顺序排序

### generate [type] new_varname [:lblname] = exp [if] [in] [, before(varname) | after(varname)]
生成新的变量`new_varname`。
`type`指定变量类型，`new_varname`指定新生成变量的变量名，`lblname`指定新生成变量使用的值映射集，`exp`指定生成的表达式。变量位置默认在最后一列。
- `before(varname)`指定新生成变量位置位于`varname`前。
- `after(varname)`指定新生成变量位置位于`varname`后。

### replace old_varname = exp [if] [in] [, nopromote]
将已有的变量`old_varname`的值替换。  
`exp`指定生成的表达式。
- `nopromote`防止替换提升变量类型以适应更改。
> 例如，考虑存储为整数类型（byte、int 或 long）的变量，并假设您用非整数替换某些值。默认情况下，replace 将变量类型更改为浮点型（float 或 double），从而正确存储更改后的值。  
> 同样，如果替换值是整数但绝对值对于当前存储类型来说太大，则replace 会将 byte 和 int 变量提升为更长的整数（int 和 long）。替换将字符串提升为更长的字符串。 nopromote 阻止替换执行此操作；相反，替换值会被截断以适合当前的存储类型。

## 缺失值操作

### mvencode varlist [if] [in], mv() [override]
将`varlist`各数值变量内的缺失值赋上指定的数值。   
注意，字符串变量不会存在缺失值。  
特别地，可以指定`varlist`为`_all`以指定全部变量。  
- `mv()`指定缺失值编码规则。`mv(#)`指定所有缺失值均被替换为数值`#`；`mv(mvc=#)`指定特定编码为`mvc`的缺失值被替换为`#`；对于多种编码可以在括号内使用` \ `进行分隔以批量指定，如mv(.=99\.a=98\.b=97)；最后也可以指定`mv(... \ else=#)`以对其余缺失值统一赋值。  
缺失值编码可为`.`或`.a`到`.z`  
- `override`指定覆盖 mvencode 提供的保护。如果没有此选项，如果`mv()`中指定的某数值在原本的变量的数据中已存在，mvencode 将拒绝进行请求的更改。

### mvdecode varlist [if] [in], mv()
将`varlist`各数值变量内的指定数值值转换为上缺失值。  
注意，字符串变量不允许存在缺失值。   
特别地，可以指定`varlist`为`_all`以指定全部变量。  
- `mv()`指定缺失值解码规则。`mv(numlist)`指定所有`numlist`内的数值均被替换为缺失值`.`；`mv(numlist=mvc)`指定所有`numlist`内的数值均被替换为编码为`mvc`的缺失值；对于多种编码可以在括号内使用` \ `进行分隔以批量指定。

## 随机数生成
|             Exp              |      Name       |                                  Description                                   |
|:----------------------------:|:---------------:|:------------------------------------------------------------------------------:|
|          rbeta(a,b)          |    贝塔分布随机变量     |             `a` and `b` are the beta distribution shape parameters             |
|        rbinomial(n,p)        |    二项分布随机变量     |         `n` is the number of trials and `p` is the success probability         |
|         rcauchy(a,b)         |    柯西分布随机变量     |            `a` is the location parameter and `b` is the scale parameter            |
|          rchi2(df )          |    卡方分布随机变量     |                                                                                |
|       rexponential(b)        |    指数分布随机变量     |                                   scale `b`                                    |
|         rgamma(a,b)          |    伽马分布随机变量     |        `a` is the gamma shape parameter and `b` is the scale parameter         |
|   rhypergeometric(N ,K,n)    |    超几何分布随机变量    |                                                                                |
|       rigaussian(m,a)        |    逆高斯分布随机变量    |                        mean `m` and shape parameter `a`                        |
|        rlaplace(m,b)         |   拉普拉斯分布随机变量    |                        mean `m` and scale parameter `b`                        |
|         rlogistic()          |   逻辑斯蒂分布随机变量    |                     mean `0` and standard deviation `π/√3`                     |
|         rlogistic(s)         |   逻辑斯蒂分布随机变量    |              mean `0`, scale `s`, and standard deviation `sπ/√3`               |
|        rlogistic(m,s)        |   逻辑斯蒂分布随机变量    |              mean `m`, scale `s`, and standard deviation `sπ/√3`               |
|       rnbinomial(n,p)        |    负二项分布随机变量    |                                                                                |
|          rnormal()           |   标准正态分布随机变量    |     normal distribution with a mean of `0` and a standard deviation of `1`     |
|          rnormal(m)          |    正态分布随机变量     |               `m` is the mean and the standard deviation is `1`                |
|         rnormal(m,s)         |    正态分布随机变量     |               `m` is the mean and `s` is the standard deviation                |
|         rpoisson(m)          |    泊松分布随机变量     |                          `m` is the distribution mean                          |
|            rt(df)            |     t分布随机变量     |                         `df` is the degrees of freedom                          |
|          runiform()          |    均匀分布随机变量     |                                interval `(0, 1)`                                 |       
|        runiform(a,b)         |    均匀分布随机变量     |                                interval `(a, b)`                                 |      
|       runiformint(a,b)       |   整数均匀分布随机变量    |                                interval `[a, b]`                                 |   
|        rweibull(a,b)         |    韦伯分布随机变量     |                              shape `a` and scale `b`                               |
|       rweibull(a,b,g)        |    韦伯分布随机变量     |                        shape `a`, scale `b`, and location `g`                        |
|       rweibullph(a,b)        |  韦伯比例风险分布随机变量   |                              shape `a` and scale `b`                               | 
|      rweibullph(a,b,g)       |  韦伯比例风险分布随机变量   |                        shape `a`, scale `b`, andlocation `g`                         |

## 随机分布函数
| 随机分布 |            概率密度函数 PDF(x)             | 累计分布函数 CDF(x) | 累计分布尾部 1-CDF(x)  |   逆累计分布函数ICDF(p)   |    逆累计分布尾部 ICDF(1-p)    |
|:----:|:------------------------------------:|:-------------:|:----------------:|:------------------:|:-----------------------:|
| 贝塔分布 |            betaden(a,b,x)            | ibeta(a,b,x)  | ibetatail(a,b,x) |  invibeta(a,b,x)   |   invibetatail(a,b,x)   |
| 二项分布 |           binomialp(n,k,p)           | binomial(n,k,p) | binomialtail(n,k,p) | invbinomial(n,k,p) | invbinomialtail(n,k,p)  |
| 卡方分布 |            chi2den(df, x)            | chi2(df, x) | chi2tail(df, x) | invchi2(df, x) |   invchi2tail(df, x)    |
|  F分布   |          Fden(df1, df2, f)           |       F(df1, df2, f)       |  Ftail(df1, df2, f)  |  invF(df1, df2, f)  |  invFtail(df1, df2, f)  |
| 非中心F分布 |          nFden(df1, df2, f)          |      nF(df1, df2, f)       | nFtail(df1, df2, f)  | invnF(df1, df2, f)  | invnFtail(df1, df2, f)  |
|  伽马分布  |         gammaden(a, b, g, x)         |        gammap(a, x)        |   gammaptail(a, x)   |   invgammap(a, x)   |   invgammaptail(a, x)   |
| 超几何分布  |     hypergeometricp(N, K, n, k)      | hypergeometric(N, K, n, k) |                      |                     |                         |
| 负二项分布 |          nbinomialp(n,k,p)           |      nbinomial(n,k,p)      | nbinomialtail(n,k,p) | invnbinomial(n,k,p) | invnbinomialtail(n,k,p) |
| 标准正态分布 |             normalden(z)             |         normal(z)          |                      |    invnormal(p)     |                         |
| 正态分布 | normalden(x,σ)均值为1， normalden(x,μ,σ) |                            |                      |                     |                         |
| 泊松分布 |            poissonp(m, k)            |       poisson(m, k)        |  poissontail(m, k)   |   invpoisson(m,k)   |  invpoissontail(m, k)   |
| t分布 |             tden(df, t)              |          t(df, t)          |     ttail(df, t)     |     invt(df, t)     |     invttail(df, t)     |
| 非中心t分布 |             ntden(df, t)             |         nt(df, t)          |    nttail(df, t)     |    invnt(df, t)     |    invnttail(df, t)     |

>注：  
>∫(0~x)PDF(t)dt = CDF(x)  
>CDF(x) = p <-> ICDF(p) = x

## 矩阵
### matrix [define] mat_name = matrix expression
矩阵表达式定义矩阵或矩阵元素的数值。

### matrix [input] mat_name = (#, # \ #, #)
手动定义矩阵数值。  
同一行内各元素间使用`,`分隔，换行使用`\`，`#`允许数值或缺失值`.`。

### matrix list mat_name
显示矩阵的类型、形状、内容。  
注意，`mat_name`必须为矩阵名称（标识符），不可以为表达式或切片索引。但是单个矩阵元素视为标量，可以使用`diplay`命令打印。

### matrix rownames mat_name row_names
将矩阵`mat_name`的列名改为`row_names`各列名。

### matrix colnames mat_name col_names
将矩阵`mat_name`的列名改为`row_names`各列名。

### mat_name[#row,#col]
矩阵索引。  
`#row`,`#col`从`1`开始计数。允许使用单个数字`#`、对应行名或列名、切片`[#..#]` `[...#]` `[#...]`。
矩阵创建时默认行名为`r1`,`r2`,...，默认列名为`c1`,`c2`,...

## 命令与程序

### program [define] program_name [, class byable() properties(namelist)]
定义程序。  

- `class`声明返回类型。可选取`nclass`,`rclass`,`eclass`或`sclass`。`nclass`声明所定义的程序不会在 `r()`、`e()` 或 `s()` 中返回结果，默认。`rclass`声明所定义的程序在 `r()` 中返回结果。这是使用 `return` 命令完成的。`eclass` 声明所定义的程序返回 `e()` 中的结果或修改 `e()` 中已有的结果。这是使用 `ereturn` 命令完成的。 `sclass` 声明所定义的程序返回 `s()` 中的结果。这是使用 `sreturn` 命令完成的。 如果程序没有显式声明对应`class`，则它可能无法直接修改对应结果类型。
- `byable()`允许使用程序执行语句添加`by varlilst`前缀。可选用byable(recall)或byable(onecall)。
- `properties`使用资源？

>使用`program`实现函数编写
> 
>       program myweirdexample 
>           version 13 
>           syntax varlist(numeric), Generate(namelist) 
>
>           local nold : word count `varlist'
>           local nnew : word count `generate' 
>           if `nold' != `nnew' { 
>               display as err "`generate' does not match `varlist'" 
>               exit 198 
>           }
>
>           local i = 1 
>           quietly foreach v of local varlist { 
>               local new : word `i' of `generate' 
>               gen `new' = 0.5 * `v' if `v' == 1 
>               replace `new' = 31 - `v' if `v' == 2 
>               replace `new' = `v' - 2 if `v' == 3             
>               local ++i 
>           }
>       end



### program dir
显示内存中的所有程序名称。  

### program drop program_names
删除程序。  

### program list [program_names]
显示程序的详细定义。

### return list [, all]
### return clear
### return scalar name = exp
### return local name = exp
### return local name ["]string["]
### return matrix name [=] matname [, copy]
### return add

> 区分 r()（返回结果）和 return()（正在组装将返回的结果）。
> 程序(programe)实际上将结果存储在 return() 中。然后，当程序完成时，return() 中的所有内容都会复制到 r() 中。因此，您编写的程序可以使用其他程序的 r() 结果，并且不存在冲突。

### capture [:] command
capture 执行命令，抑制其所有输出（包括错误消息，如果有）并发出返回码零。命令生成的实际返回代码存储在内置标量`rc`中。   
capture 可以与 {} 结合生成捕获块，抑制命令块的输出。  

### simulate [exp_list], reps(#) [options] : command


### 

## OLS回归

### regress dep_varname [indep_varlist] [if] [in] [weight] [, noconstant vce(vcetype) level(#)] 
进行回归。指定`regress`命令后第一个变量名为因变量`dep_varname`，其后各变量名均为自变量`indep_varlist`。当只指定`dep_varname`时，相当于只做常数项回归。  

```
.  regress mpg weight foreign

      Source |       SS           df       MS      Number of obs   =        74
-------------+----------------------------------   F(2, 71)        =     69.75
       Model |   1619.2877         2  809.643849   Prob > F        =    0.0000
    Residual |  824.171761        71   11.608053   R-squared       =    0.6627
-------------+----------------------------------   Adj R-squared   =    0.6532
       Total |  2443.45946        73  33.4720474   Root MSE        =    3.4071

------------------------------------------------------------------------------
         mpg | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
-------------+----------------------------------------------------------------
      weight |  -.0065879   .0006371   -10.34   0.000    -.0078583   -.0053175
     foreign |  -1.650029   1.075994    -1.53   0.130      -3.7955    .4954422
       _cons |    41.6797   2.165547    19.25   0.000     37.36172    45.99768
------------------------------------------------------------------------------
```

生成各种汇总统计数据以及回归系数表。   

在左上角，回归报告了方差分析 (ANOVA) 表。  
列标题 `SS`、`df` 和 `MS` 分别代表`平方和`、`自由度`和`均方`（即平方和除以自由度）。`模型平方和`相当于`SSE=∑(y_hat - E(y))^2`，`残差平方和`相当于`SSR=∑(y-y_hat)^2`，两者相加的`Total`即为 `SST=∑(y-E(y))^2`。
> 在此示例中，总平方和为 2,443.5：模型占了 1,619.3，还有 824.2 未解释。由于回归包含一个常数，因此总和反映了去除均值后的总和，模型的平方和也是如此。该表还显示，总共有 73 个自由度（计算为 74 个观测值减去平均去除的 1 个），其中 2 个被模型消耗，剩下 71 个为残差。

方差分析表的右侧显示了其他汇总统计数据。
回归使用总观测值（样本数）为74。
与 ANOVA 表关联的 F 统计量为 69.75。该统计量有 2 个分子自由度和 71 个分母自由度。  
F 统计量检验除常数之外的所有系数均为零的假设。观察到较大或更大的 F 统计量的概率报告为 0.0000，这是 Stata 指示小于 0.00005 的数字的方式。  
回归的拟合优度 `R^2` 为 0.6627，根据自由度 (R^2 a) 调整的 R^2 为 0.6532。  
均方根误差（标记为 Root MSE）为 3.4071。它是方差分析表中报告的残差均方误差的平方根。

最下方的表格显示了每个因变量相应的的回归系数、标准误、t统计量、双边显著性检验、95%水平的置信区间。

#### Args
- `vce()`指定报告的标准误的类型。其中包括从渐近理论 (ols) 派生的类型、对某些类型的错误指定具有鲁棒性（鲁棒性）、允许组内相关性（簇 clustvarlist）以及使用 bootstrap 或 jackknife 方法的类型

|VCE Type|                                                                                                     Description                                                                                                     |
|:---:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| `vce(ols)` |                                                                                            使用标准方差估计器,进行普通最小二乘回归。默认使用该项。                                                                                             |
| `vce(cluster clustvarlist)` |指定标准误由`clustvarlist`中的一个或多个变量定义的组内存在组内相关性，从而放宽了观察结果独立的通常要求。例如，vce(cluster clustvar1) 产生集群稳健标准误差，允许观察结果在 `clustvar1` 定义的组之间独立，但在组内不一定独立。您还可以输入 vce(cluster clustvar1 clustvar2 . . . clusterp) 来解释由 p 变量形成的组内的相关性（多路聚类） |
| `vce(hc2 [ clustvar ][ , dfadjust ])`||
| `vce(hc3)`||

- `level()`指定置信度`#`。默认为95。
- `beta`要求报告标准化贝塔系数而不是置信区间。贝塔系数是通过首先将所有变量标准化为均值 0 和标准差 1 获得的回归系数。

#### Results
|  Name   |  Type  |       Description       |
|:-------:|:------:|:-----------------------:|
| e(mss)  | scalar |          模型平方和          |
| e(rss)  | scalar |          残差平方和          |
| e(df_m) | scalar |          模型自由度          |
| e(df_r) | scalar |          残差自由度          |
|  e(N)   | scalar |          总样本数           |
|  e(F)   | scalar |          F统计量           |
|  e(r2)  | scalar |          拟合优度           |
| e(r2_a) | scalar |         调整拟合优度          |
| e(rmse) | scalar |    均方根误差（残差均方误差的平方根）    |
|  e(ll)  | scalar |                         |
| e(ll_0) | scalar |                         |
| e(rank) | scalar |                         |
|  e(b)   | matrix |  各变量系数与常数项`_cons`的估计值   |
|  e(V)   | matrix | 各变量系数与常数项`_cons`的两两的协方差 |
| e(sample) | function | 函数，当观测值在回归的样本中时返回`true`，常用于`if`条件句 |
可使用 `ereturn list` 调出所有`e()`结果概览,  
使用`display e(...)`显示`scalar`类型的结果。  
使用`matrix list e(...)`显示`matrix`类型的结果。  

对于参数矩阵`e(V)`，也可以使用`_b[varname`索引每个变量的对应系数。

#### 回归后估计命令
|Command| Definition|
|:----:|:----:|
|dfbeta| DFBETA influence statistics|
|estat hettest| tests for heteroskedasticity|
|estat imtest| information matrix test|
|estat ovtest| Ramsey regression specification-error test for omitted variables|
|estat szroeter| Szroeter’s rank test for heteroskedasticity|
|estat vif| variance inflation factors for the independent variables|
|estat esize| η2 and ω2 effect sizes|
|contrast| contrasts and ANOVA-style joint tests of estimates|
|estat ic| Akaike’s and Schwarz’s Bayesian information criteria (AIC and BIC)|
|estat summarize| summary statistics for the estimation sample|
|estat vce| variance–covariance matrix of the estimators (VCE)|
|estat (svy)| postestimation statistics for survey data|
|estimates| cataloging estimation results|
|forecast1| dynamic forecasts and simulations|
|hausman| Hausman’s specification test|
|lincom| point estimates, standard errors, testing, and inference for linear combinations of coefficients|
|linktest| link test for model specification|
|lrtest2| likelihood-ratio test|
|margins| marginal means, predictive margins, marginal effects, and average marginal effects|
|marginsplot| graph the results from margins (profile plots, interaction plots, etc.)|
|nlcom| point estimates, standard errors, testing, and inference for nonlinear combinations of coefficients|
|predict| predictions, residuals, influence statistics, and other diagnostic measures|
|predictnl| point estimates, standard errors, testing, and inference for generalized predictions|
|pwcompare| pairwise comparisons of estimates|
|suest| seemingly unrelated estimation|
|test| Wald tests of simple and composite linear hypotheses|
|testnl| Wald tests of nonlinear hypotheses|


### estimates store est_name 
将当前估计结果存储到内存，标识符为`est_name`。  
会自动创建`f"_est_{est_name}"样式的变量以记录该`est_name`估计模型所使用的样本（相当于`e(sample)`）。

### estimates restore est_name 
读取标识符为`est_name`的估计结果，作为当前估计模型。

### estimate save filename
将当前估计结果保存为文件，命名为`filename`。

### estimate use filename
读取文件名为`filename`的估计结果，作为当前估计模型。

### predict [type] new_varname [if] [in] [, xb residual stdp score]
使用内存中已生成的模型进行预测，生成预测结果`new_varname`。   
可以指定`if`或`in`语句以限定用于预测的样本范围。特别地，可以使用`if e(sample)`以只使用模型计算时所用数据进行生成。
- `xb` 计算线性预测结果。
- `residual`计算残差。
- `stdp` 计算预测结果的标准误。
- `score` calculate first derivative of the log likelihood with respect to xj β

## T检验
### ttest varname==# [if] [in] [, level(#)]
单一样本t检验，测试`varname`的均值是否为`#`
- `level()`置信水平。默认为 `level(95)`。

### ttest varname1==varname1 [if] [in] , unpair [unequal level(#)]
独立样本t检验，测试`varname1`的均值是否等于`varname2`
- `level()`置信水平。默认为 `level(95)`。

### ttest varname1==varname1 [if] [in] [ level(#)]
配对样本t检验，测试按顺序配对的每个`varname1 - varname2`元素的均值是否为`0`
- `level()`置信水平。默认为 `level(95)`。

### ttest varname [if] [in], by(catvar) [unequal level(#)]
样本分组t检验，测试由`catvar`定义的两个组之间`varname`的均值是否相等。  
注意`catvar`变量有且只有2种不同值。
- `unequal`不假定未配对的数据具有相等的方差。
- `level()`置信水平。默认为 `level(95)`。

## F检验
### test coef_list
Wald检验：`coef_list`中各变量在估计中的系数均为0。  
注意需要内存中已存在最近的一次估计模型。

### test exp=exp[=. . . ]
可以直接使用`varname`代指系数，也可以使用`_b[varname]`。  
注意不能使用`e(b)[varname]`，其只记录一个数值，无法进行F检验。  
注意需要内存中已存在最近的一次估计模型。

### lincom exp [, eform or hr shr irr rrr level(#) display(#)]
报告`exp = 0`的系数线性组合的点估计、标准误差、t 或 z 统计、p 值和置信区间。  
可以直接使用`varname`代指系数，也可以使用`_b[varname]`。   
注意需要内存中已存在最近的一次估计模型。



## estout
> 注意，外部命令，需要安装`estout`包。  
> ssc install estout, all replace 

### esttab [est_names] [using filename] [, options estout_options ]
对估计结果，生成发布样式的表格。 

`est_names`可以指定一个或多个估计结果，允许使用`*` `?`匹配字符，并允许使用`.`字符代指当前统计结果。不指定`est_names`时默认使用当前估计结果。  
`fileaname`指定生成结果保存的文件名。可指定文件后缀名为`.smcl` `.txt` `.csv` `.rtf` `.html` `.tex` 和 `.md`，当`options`中未指定保存格式时默认使用文件名中指定的后缀，否则默认为`smcl`。未指定`using filename`时，不保存文件，仅将结果显示在屏幕上。    

#### Labeling Options
- `label` 指定使用变量标签代替变量名称（并且使用估计集标题代替估计集名称）。此外，常数项标签打印`Constant`而不是`_cons`。  
- `interaction(string)` 指定用作交互术语分隔符的字符串（仅在 Stata 11 或更高版本中相关）。 默认是交互（“#”）。 对于 tex 和 booktabs，默认值是交互（“$\times$”）。
- `title(string)` 可用于提供表格的标题。 如果指定，字符串将打印在表的顶部。   
- `mtitles`（不带参数）指定对于每个模型，存储的估计集的标题（或者，如果为空，则为名称）将打印为表标题中的模型标题。 如果 mtitles 是
         省略，默认是使用因变量的名称或标签作为模型的标题（请参阅 depvar 选项）。 或者，使用 mtitles(list) 指定模型标题列表。
         如果标题包含空格，请将其括在双引号中，例如 mtitles(“模型 1”“模型 2”).
- `nomtitles` 禁止打印模型标题。
- `depvars` 将模型的（第一个）因变量的名称（或标签）打印为表标题中的模型标题。 这是默认设置。 指定nodepvars以使用存储的名称
         估计集作为标题。
- `numbers` 在表标题中包含一行，其中包含连续的型号。默认启用。   
- `nonumbers` 禁止打印型号。
- `coeflabels(name label [...])` 指定系数的标签。 成对指定名称和标签，如有必要，将标签用双引号引起来。
- `notes` 在表格末尾打印注释，解释重要性符号和显示的统计数据的类型。默认启用。  
- `nonotes`禁止打印注释。  
- `addnotes(list)` 可用于在表格底部添加更多文本行。 包含空格的行必须用双引号括起来。
#### Document Format Options
|  Option   |     Definition      |                                                                         Remark                                                                          |
|:---------:|:-------------------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------:|
|  `smcl`   |   生成一个 SMCL 格式的表    |                                                            显示在 Stata 结果窗口或 Stata 查看器中。此项为默认                                                             |
|  `fixed`  |  生成固定格式的 ASCII 表。   |                                                                                                                                                         |
|   `tab`   | 生成一个制表符分隔的 ASCII 表。 |                                                                                                                                                         |
|   `csv`   | 生成CSV表。使用`,`作为分隔符。  | 为了防止 Excel 解释表格单元格的内容，他们用双引号括起来，前面加等号（即 `="..."`）。 如果指定 `plain` 选项，则表格单元格将用双引号引起来，且不带前导等号。如果您想保留表格的格式，第一种方法是合适的。 如果您想在 Excel 中使用表的内容进行进一步计算，则适合使用第二种方法。 |
|  `scsv`   |  生成CSV表。使用`;`作为分隔符。  |                                                               这适用于某些非英语版本的 Excel（例如德语版本）。                                                               |
|   `rtf`   | 生成富文本格式表以供文字处理器使用。  |                                                                                                                                                         |
|  `html`   | 生成一个简单的 HTML 格式的表格。 |                                                                                                                                                         |
|   `tex`   |  生成一个 LaTeX 格式的表格。  |                                                                                                                                                         |
|`booktabs` |  生成一个 LaTeX 格式的表格   |                                                                与 LaTeX 的 booktabs 包一起使用。                                                                |
|   `md`    | 生成 Markdown 格式的表格。  |                    Native Markdown 没有对表格的特定支持，但 GitHub Flavored Markdown 和 MultiMarkdown 则支持。 选项 mmd 可以用作md 的同义词（在本例中默认文件后缀为“.mmd”）。                    |

## mediation
> 注意，外部命令，需要安装`mediation`包。  
> ssc install mediation, all replace  

### medeff (regress M T x) (regress Y T M x) [if] [in], treat(T) mediate(M)
计算自变量T通过自变量M影响因变量Y的mediation effect。  
前一个括号中填入以中介变量`M`为因变量的回归命令，前一个括号中填入以因变量`Y`为因变量的回归命令。
- `treat()`指定处理变量。
- `mediate()`指定中介变量。