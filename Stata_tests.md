# 1 变量T检验

## 1.1 命令

### **ttest `varname`==`#` [if] [in] [, level(`#`)]**  
单一样本t检验，测试`varname`的均值是否为`# ` 
- `level()`置信水平。默认为 `level(95)`。
  
### **ttest `varname1`==`varname2` [if] [in] , unpair [unequal level(`#`)]**  
两独立样本t检验，测试`varname1`的均值是否等于`varname2`  
- `level()`置信水平。默认为 level(95)。  
  
### **ttest `varname1`==`varname2` [if] [in] [level(`#`)]**  
配对样本t检验，测试按顺序配对的每个`varname1 - varname2`元素的均值是否为0
- `level()`置信水平。默认为 `level(95)`。
  
### **ttest `varname` [if] [in], by(`catvar`) [unequal level(`#`)]**  
样本分组t检验，测试由catvar定义的两个组之间`varname`的均值是否相等。  
注意`catvar`变量应当有且只有2种不同值。  
- `unequal`不假定未配对的数据具有相等的方差。  
- `level()`置信水平。默认为 `level(95)`。  

### **ttesti `n` `mean` `sd` `mu`**  
单一样本t检验(快速)  
输入样本数`n`, 样本均值`mean`, 标准差`sd`和假设检验目标值`mu`

### **ttesti `n1` `mean1` `sd1` `n2` `mean2` `sd2`**  
两独立样本t检验(快速)
输入类似，分别为两组两本的样本数、样本均值和标准差。

### **testi `n` `mean_diff` `sd_diff`**  
配对样本t检验(快速)  
输入配对样本的样本数`n`, 配对样本之差的均值`mean_diff`, 配对样本之差的标准差`sd_diff`。



## 1.2 示例
    
    sysuse auto,clear
    
    // 变量T检验
    ttest price==6000 if foreign==0 //对于样本中`foreign`变量为0的样本，检验假设：变量`price`==0
	
	ttest price ,by( foreign ) level(99)
	ttest price ,by( foreign ) level(90)
	ttest price ,by( foreign ) level(95)

    // 变量快速T检验
    sum price if foreign==0

    
    Variable |        Obs        Mean    Std. dev.       Min        Max
    ---------+---------------------------------------------------------
       price |         52    6072.423    3097.104       3291      15906

	
	ttesti 52 6072.423 3097.104 6000  // 


	sum price if foreign==0 //52    6072.423    3097.104
	sum price if foreign==1 //22    6384.682    2621.915
	
	ttesti 52 6072.423 3097.104 22 6384.682 2621.915

# 2 Wald检验 回归系数T检验
## 2.1 命令
*注意1：使用命令前必须先运行过回归命令；*  
*注意2：无论同方差还是异方差，区别只在回归命令的可选参数robust，假设检验的命令相同。*

### **lincom `exp`, [level(`#`)]**  
报告`exp`的估计值、标准误、t统计量、p值和置信区间。
- `exp`为线性回归各自变量系数的线性组合表达式。可以直接使用`varname`代指系数，也可以使用_b[`varname`]。


### **test `varname`=`exp`**  
报告`varname`=`exp`假设的t统计量的平方(W统计量)。


## 2.2 示例  
    reg price mpg rep78 trunk
	
	lincom mpg+rep78-1     	//报告t = 1.22
    lincom mpg+rep78-1, l(90)   //给出90%置信区间估计，默认是95%的CI
	test mpg+rep78=1	//报告F = 1.49，是t值的平方

# 3 Wald检验 回归系数F检验
*注意1：使用命令前必须先运行过回归命令；*  
*注意2：无论同方差还是异方差，区别只在回归命令的可选参数robust，假设检验的命令相同。*

## 3.1 命令
### **test `varnames`**
报告`varnames`中各变量对应系数均为0这一假设下的F统计量。
可以直接使用`varname`代指系数，也可以使用_b[`varname`]。

### **test `exp1`=`exp2`[=. . . ]**
报告`exp1`=`exp2`[=. . . ]这一假设下的F统计量。
可以直接使用`varname`代指系数，也可以使用_b[`varname`]。
```
例如：
test educ _cons
test (educ=0) (_cons=0)
test educ = _cons = 0
test _b[educ] _b[_cons]
test (_b[educ]=0) (_b[_cons]=0)
test _b[educ]=_b[_cons]=0
test (_b[_cons]=2*_b[educ]) (_b[educ12]=3*_b[educ6_8])
都是合法的操作
```

### **lincom `exp`, [level(`#`)]**  
报告`exp`的估计值、标准误、t统计量、p值和置信区间。
- `exp`为线性回归各自变量系数的线性组合表达式。可以直接使用`varname`代指系数，也可以使用_b[`varname`]。


## 3.2 手动构造
    
    quietly reg lsalary lsales ceoten lmktval profits //无约束模型回归
    scalar SSR_u=e(rss)  //无约束模型的RSS
    scalar df=e(df_r)  //无约束模型的自由度

    quietly reg lsalary lsales ceoten  //约束模型回归
    scalar SSR_r=e(rss)  //约束模型的RSS
    scalar q=2 //排除性约束个数（这个需要手动指定）

    scalar F_stat=((SSR_r - SSR_u) / q) / (SSR_u / df)  //计算F统计量
    scalar F_conf=invF(q,df,0.95)   //计算5%置信水平上的置信区间边界(方法1)
    scalar F_conf=invFtail(q,df,0.05)  //计算5%置信水平上的置信区间边界(方法2)
    scalar p_value=Ftail(q,df,fstat) //计算P值，即在F(q,df)分布中x>F_stat时的概率

    //报告
    dis "The F test Statistic is: " F_stat
    dis "The 5% Critical Value is:" F_conf
    dis "The P-Value is:          " p_value

# 4 大样本检验 LM统计量（拉格朗日乘数统计量）
## 4.1 手动构造
    reg lwage educ tenure nonwhite female married //约束模型回归
    predict res, residual     //获取各样本的残差

    reg res educ exper exper2 tenure nonwhite female married//残差对无约束模型回归
    scalar q=2 //排除性约束个数（这个需要手动指定）

    scalar LM = e(r2) * e(N) //计算LM统计量
    scalar c_value = invchi2(q,0.95) //计算5%置信水平上的置信区间边界
    scalar p_value = chi2tail(q,LM) //计算P值，即在chi2(q)分布中x>LM时的概率

    //报告
    dis "LM test Statistic is: " LM
    dis "Critical Value is: " c_value
    dis "P-Value is: " p_value

# 5 不同组间回归检验 Chow检验(邹至庄检验)
    use wage1.dta, clear
    gen expersq =exper^2 
    gen tenuresq=tenure^2

    //虚拟变量和数值变量的交叉项
    gen femedu = female*educ
    gen femexper = female*exper
    gen femexpersq = female*expersq
    gen femtenure = female*tenure
    gen femtenuresq = female*tenuresq
      
### 5.1 在含有k个解释变量和1个截距项的模型中，假设各组间的截距和斜率都不存在任何差异
#### 5.1.1 Method 1 
    reg lwage educ exper expersq tenure tenuresq fem*  //包含所有交互项的回归

    test female = femedu = femexper = femexpersq = femtenure = femtenuresq  = 0 //F检验
    test female femedu femexper femexpersq femtenure femtenuresq //same test results //F检验（同上，只是写法不同）

#### 5.1.2 Method II: F statistic
    * Step 1:  unrestricted model: seperate regression
    //分组回归，分别获取两组回归的SSR和样本数（观测值），计算无约束模型的自由度
    quietly reg lwage educ exper expersq tenure tenuresq if female==0
    scalar SSR_1 = e(rss)
	scalar n_1 = e(N)
    quietly reg lwage educ exper expersq tenure tenuresq if female==1
    scalar SSR_2 = e(rss)
    scalar n_2 = e(N)
	scalar df_ur= n1 + n2 - 2 * (e(df_m) + 1) //无约束模型自由度

    *Step 2: restricted model: pooled regression
    //遵从假设，将两组合并进行回归
    quietly reg lwage educ exper expersq tenure tenuresq //约束模型，仅选取数值变量作为自变量，不包含虚拟变量和交叉项
    scalar SSR_p  = e(rss) //约束模型的SSR
	scalar q = 6    //约束条件数：1个截距+5个斜率
    
    * Step 3: Construct Chow test
    scalar F_stat = ((SSR_p - (SSR_1 + SSR_2)) / q) / ((SSR_1 + SSR_2) / df_ur) // 计算F统计量
    scalar p_value = 1 - F(q, df_ur, F_stat)   // 计算p值
    display "F statistics = " F_stat
    display "p-value      = " p_value
      
 
### 5.2 允许组间的截距不同，检验所有变量的系数是否不同
  
    * Step 1: unrestricted model:seperate regression
    quietly reg lwage educ exper expersq tenure tenuresq if female==0
    scalar SSR_1 = e(rss)
	scalar n_1 = e(N)

    quietly reg lwage educ exper expersq tenure tenuresq if female==1
    scalar SSR_2 = e(rss)
	scalar n_2 = e(N)

    scalar df_ur = n_1 + n_2 - 2 * (e(df_m) + 1)  //无约束模型自由度

    * Step 2: restricted model: pooled regression
    quietly reg lwage female educ exper expersq tenure tenuresq //约束模型，选取数值变量和虚拟变量作为自变量，不包含交叉项
    scalar SSR_p  = e(rss) //约束模型的SSR
	scalar q  = 5   //约束条件数：5个斜率(不包含截距)

    * Step 3: Construct Chow test
    scalar F_stat  = ((SSR_p-(SSR_1+SSR_2)) / q) / ((SSR_1+SSR_2) / df_ur)  // 计算F统计量
    scalar P  = 1 - F(q,df_ur,F_stat)  // 计算p值
    display "F statistics = " F_stat
    display "p-value      = " p_value
 

### 5.3 允许组间的截距和某一变量的系数（斜率）不同（以`educ`为例），检验其他变量的系数是否不同

    * Step 1:  unrestricted model: seperate regression	 
    quietly reg lwage educ exper expersq tenure tenuresq if female==0
    scalar SSR_1 = e(rss)
	scalar n_1 = e(N)
    quietly reg lwage educ exper expersq tenure tenuresq if female==1
    scalar SSR_2 = e(rss)
	scalar n_2 = e(N)
    scalar df_ur = n_1 + n_2 - 2 * (e(df_m) + 1)//无约束模型自由度

    * Step 2: restricted model: pooled regression
    gen educ_female=educ*female
    quietly reg lwage female educ educ_female exper expersq tenure tenuresq //约束模型，选取数值变量、虚拟变量和educ与虚拟变量的交互项作为因变量
    scalar SSR_p  = e(rss)  
    scalar q  = 4  //约束条件数：4个斜率(不包含截距和educ的斜率)

    * Step 3: Construct Chow test
    scalar F_stat  = ((SSR_p - (SSR_1+SSR_2))/q) / ((SSR_1+SSR_2)/df_ur)  // 计算F统计量
    scalar p_value  = 1 - F(q, df_ur, F_stat)  // 计算p值
    display "F statistics = " F_stat
    display "p-value      = " p_value

# 6 异方差性检验 BP检验(布罗施-帕甘检验)
## 6.1 命令： 
### **estat hettest [,rhs normal iid fstat]**
- `rhs`指定使用回归模型的所有解释变量（right-hand side variables）来进行检验。没有这个选项的话，默认是只使用被解释变量的估计值`y_hat`。
- `normal` 指定进行LM检验时假设模型扰动项是正态分布的,*与`iid`,`fstat`不兼容*
- `iid` 指定进行LM检验时假设独立同分布,当存在自相关或怀疑存在异方差时不使用*与`normal`,`fstat`不兼容*
- `fstat`指定报告F统计量，否则将报告LM统计量*与`normal`,`iid`不兼容*


### **estat hettest `varnmaes` [,rhs iid fstat]**  
检验异方差性是否取决于`varnames`指定的各变量。  
*注意`varnames`中变量名不仅局限于回归的自变量，非模型中的变量也可以*
- `rhs`指定使用`varnames`中属于模型解释变量的变量来进行检验。

## 6.2 手动构造
    quietly reg narr86 avgsen avgsensq pcnv ptime86 qemp86 inc86 black hispan //OLS回归
    predict u_hat, residual //获取残差
    gen u_hat_square=uhat^2 //残差平方

    // RHS时
    quietly reg u_hat_square avgsen avgsensq pcnv ptime86 qemp86 inc86 black hispan //残差平方对各认为与异方差性相关的解释变量做回归
    test avgsen avgsensq pcnv ptime86 qemp86 inc86 black hispan //报告F统计量, F~(k, n-k-1)

	scalar LM_stat = e(N)*e(r2)    //计算LM统计量, LM~chi2(k)
    scalar n = e(N)
    scalar df = e(N) - e(df_m) -1 //即e(df_r), 自由度取决于假设认为有多少解释变量与异方差性相关，即u_hat_square回归中的解释变量数k
	scalar p_LM = chi2tail(e(df_m), LM_stat)

    // 认为存在模型外变量`totime`与异方差性相关时
    qui reg uhat2 avgsen avgsensq pcnv ptime86 qemp86 inc86 black hispan tottime
    test avgsen avgsensq pcnv ptime86 qemp86 inc86 black hispan tottime //报告F统计量, F~(k, n-k-1)  



# 7 异方差性检验 White检验(怀特检验)
## 7.1 命令： 
### **estat imtest, white**  
报告White检验的LM统计量。  

### **whitetst**  
外部命令，报告White检验的LM统计量。 

### 7.2 手动构造
#### 7.2.1  一般White检验
    quietly reg narr86 avgsen pcnv
    predict e, residual

    gen e2=e^2
    gen avgsen2=avgsen^2
    gen pcnv2=pcnv^2
    gen avgsen_pcnv=avgsen*pcnv

    qui reg e2 avgsen pcnv avgsen2 pcnv2 avgsen_pcnv
    scalar LM_stat =_N*e(r2) // 计算LM统计量， LM~chi2(k), k为所有回归元数，包括解释变量、平方项和交叉项
    scalar p_value=chi2tail(e(df_m), LM_stat) //由LM~chi2(9)计算p值

    dis "The LM test Statistic is:" LM_stat
    dis "The P-Value is:" p_value

#### 7.2.2 使用拟合值y_hat进行回归的特殊White检验
    quietly reg narr86 avgsen pcnv
    predict u_hat, residual
    predict y_hat

    gen y_hat_square = yhat^2
    gen u_hat_square = uhat^2

    reg u_hat_square y_hat y_hat_square
    scalar LM_stat = _N*e(r2) // 计算LM统计量
    scalar p_value = chi2tail(e(df_m), LM_stat)  //p-value: P(chi2>NR2)

    dis "The LM test Statistic is:" LM_stat
    dis "The P-Value is:" p_value  

# 8 回归设定误差检验 RESET检验

## 8.1 命令
### **estat ovtest [, rhs]**  
对回归模型进行RESET检验，报告相应的F统计量和p值。  
- `rhs`指定使用回归模型的所有解释变量（right-hand side variables）来进行检验。没有这个选项的话，默认是只使用被解释变量的估计值`y_hat`。

### **linktest**
对回归模型进行linktest检验，报告相应的回归结果。  
不同于RESET，linktest是y对y_hat,y_hat^2做回归


# 9 过度识别检验 Sargan检验
## 9.1 命令
### **estat overid**
（ivregress后）
原假设：所有工具变量都是有效的，即工具变量与误差项无关。
备择假设：至少有一个工具变量是无效的，即工具变量与误差项相关。

# 10 内生性检验 Durbin-Wu-Hausman检验

ivregress 2sls Y (X = M) Z
### **estat endogenous**

# 弱工具变量检验
ivreg2 Y1 (Y2 = Z1 Z2) X1 X2 X3, first