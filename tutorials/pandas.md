Pandas 数据分析笔记
一、Pandas 框架概述
1. Pandas 介绍
作用：Python 的第三方包，用于数据清洗、处理以及分析，是商业和工程领域流行的结构化数据工具集。
优势：基于 Numpy 构建，运行速度快；有处理缺失数据的 API；具备强大灵活的分组、聚合、转换功能。
适用场景：处理单机大数据集（相对于 Excel）；大数据 ETL 数据仓库中数据清洗及处理环节。
======================================================================================
2. 安装 Pandas
打开 cmd 窗口，输入以下命令：
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple/ pandas
Anaconda 默认已安装 Pandas 和 Numpy。
======================================================================================
3. Pandas 初体验
导入数据集到 data 目录。
创建 Python 脚本，导入 pandas 库：
import pandas as pd
加载数据：
df = pd.read_csv('./1960 - 2019全球GDP数据.csv', encoding='gbk')
======================================================================================
数据查询与操作：
# 查询中国的GDP
china_gdp = df[df.country == '中国']
china_gdp.head(10)
# 设置年份为索引
china_gdp = china_gdp.set_index('year')
# 绘制中国GDP变化曲线
china_gdp.GDP.plot()
对比多国 GDP 曲线：
us_gdp = df[df.country == '美国'].set_index('year')
jp_gdp = df[df.country == '日本'].set_index('year')
# 修改列名使图例显示为各国名称
jp_gdp.rename(columns={'GDP': '日本'}, inplace=True)
china_gdp.rename(columns={'GDP': '中国'}, inplace=True)
us_gdp.rename(columns={'GDP': '美国'}, inplace=True)
# 画图并添加图例
jp_gdp['日本'].plot(legend=True)
china_gdp['中国'].plot(legend=True)
us_gdp['美国'].plot(legend=True)
# 解决中文显示问题
import matplotlib as plt
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False
======================================================================================
二、Pandas 数据结构与数据类型
1. Pandas 数据结构和数据类型
核心数据结构为 DataFrame 和 Series，层级关系为 DataFrame 包含 Series，Series 包含索引列和数据列。
2. Series 对象
定义：类似于一维数组，由 values（数据）和 index（索引）组成。
======================================================================================
创建方法：
import pandas as pd
# 使用默认自增索引
s2 = pd.Series([1, 2, 3])
# 自定义索引
s3 = pd.Series([1, 2, 3], index=['A', 'B', 'C'])
# 使用字典创建
dst = {'A': 1, 'B': 2, 'C': 3, 'D': 4, 'E': 5, 'F': 6}
pd.Series(dst)
# 使用 numpy 创建
import numpy as np
pd.Series(np.arange(10))
s4 = pd.Series([i for i in range(6)], index=[i for i in 'ABCDEF'])
# 获取索引
s4.index
# 获取值
s4.values
# 通过索引获取数据
s4['A']
======================================================================================
属性：
# 形状
data.shape
# 行索引
data.index
# 列索引
data.columns
# 值
data.values
# 转置
data.T
======================================================================================
方法：
# 显示前 n 行
data.head(5)
# 显示后 n 行
data.tail(5)
索引设置：
# 修改行列索引值
stu = ["学生_" + str(i) for i in range(data.shape[0])]
data.index = stu
# 重设索引
data.reset_index()  # drop=False，不删除原来索引
data.reset_index(drop=True)  # 删除原来的索引值
# 以某列值设置为新的索引
df = pd.DataFrame({'month': [1, 4, 7, 10], 'year': [2012, 2014, 2013, 2014], 'sale': [55, 40, 84, 31]})
df.set_index('month')
df = df.set_index(['year', 'month'])
======================================================================================
Pandas 的数据类型
Pandas 数据类型	说明	对应的 Python 类型
Object	字符串类型	string
int	整数类型	int
float	浮点数类型	float
datetime	日期时间类型	datetime 包中的 datetime 类型
timedelta	时间差类型	datetime 包中的 timedelta 类型
category	分类类型	无原生类型，可以自定义
bool	布尔类型	bool（True,False）
nan	空值类型	None
======================================================================================
查看数据类型的 API：
s1.dtypes
df1.dtypes
df1.info()  # s 对象没有 info() 方法
======================================================================================
特殊类型演示：
# datetime 类型
import pandas as pd
dates = pd.to_datetime(['2024-09-01', '2024-09-02', '2024-09-03'])
# timedelta 类型
start_date = pd.to_datetime('2024-09-01')
end_date = pd.to_datetime('2024-09-05')
delta = end_date - start_date
# category 类型
categories = pd.Series(['apple', 'banana', 'apple', 'orange'], dtype='category')
======================================================================================
三、Pandas 基本数据操作
1. 数据集
# 读取文件
data = pd.read_csv("./data/stock_day.csv")
# 删除一些列
data = data.drop(["ma5", "ma10", "ma20", "v_ma5", "v_ma10", "v_ma20"], axis=1)
======================================================================================
2. 索引操作
直接使用行列索引（先列后行）：
data['open']['2018-02-27']
结合 loc 或者 iloc 使用索引：
# 使用 loc，只能指定行列索引的名字
data.loc['2018-02-27':'2018-02-22', 'open']
# 使用 iloc，可以通过索引的下标去获取
data.iloc[:3, :5]
======================================================================================
3. 赋值操作
data['close'] = 1
data.close = 1
======================================================================================
4.排序操作
DataFrame 排序：
# 按开盘价升序排序
data.sort_values(by="open", ascending=True).head()
# 按多个键排序
data.sort_values(by=['open', 'high'])
# 对索引进行排序
data.sort_index()

Series 排序：
# 按值排序
data['p_change'].sort_values(ascending=True).head()
# 按索引排序
data['p_change'].sort_index().head()
======================================================================================
四、DataFrame 运算
1. 算法运算
python
运行
# 加法
data['open'].add(1)
# 减法
data['open'].sub(1)
======================================================================================
2. 逻辑运算符
逻辑运算符号：
python
运行
# 筛选 open 大于 23 的数据
data[data["open"] > 23].head()
# 多个逻辑判断
data[(data["open"] > 23) & (data["open"] < 24)].head()
逻辑运算函数：
python
运行
# query 函数
data.query("open<24 & open>23").head()
# isin 函数
data[data["open"].isin([23.53, 23.85])]
======================================================================================
3. 统计运算
describe：
python
运行
data.describe()
统计函数：
python
运行
# 最大值
data.max(0)
# 方差
data.var(0)
# 标准差
data.std(0)
# 中位数
df = pd.DataFrame({'COL1': [2, 3, 4, 5, 4, 2], 'COL2': [0, 1, 2, 3, 4, 2]})
df.median()
# 最大值的位置
data.idxmax(axis=0)
# 最小值的位置
data.idxmin(axis=0)

累计统计函数：
python
运行
# 排序
data = data.sort_index()
# 对 p_change 进行求和
stock_rise = data['p_change']
stock_rise.cumsum()
# 绘制累计求和结果
import matplotlib.pyplot as plt
stock_rise.cumsum().plot()
plt.show()

apply 自定义运算：
python
运行
data[['open', 'close']].apply(lambda x: x.max() - x.min(), axis=0)
======================================================================================
五、文件读取与存储
1. CSV
read_csv：
python
运行
data = pd.read_csv("./data/stock_day.csv", usecols=['open', 'close'])
to_csv：
python
运行
# 保存数据
data[:10].to_csv("./data/test.csv", columns=['open'])
# 不保存索引
data[:10].to_csv("./data/test.csv", columns=['open'], index=False)
======================================================================================
2. MySQL
安装相关包：
python
运行
pip install pymysql==1.0.2 -i https://pypi.tuna.tsinghua.edu.cn/simple/
pip install sqlalchemy==2.0.0 -i https://pypi.tuna.tsinghua.edu.cn/simple/
准备数据：
python
运行
import pandas as pd 
df = pd.read_csv('./csv示例文件.csv', sep=',', index_col=[0]) 
创建数据库操作引擎对象并指定数据库：
python
运行
from sqlalchemy import create_engine
engine = create_engine('mysql+pymysql://root:123456@127.0.0.1:3306/test?charset=utf8')

将数据写入 MySQL 数据库：
python
运行
df.to_sql('test_pdtosql', engine, index=False, if_exists='append')

从数据库中加载数据：
python
运行
# 读取整张表
pd.read_sql('test_pdtosql', engine)
# 使用 SQL 语句获取数据
pd.read_sql('select name,AKA from test_pdtosql', engine)
======================================================================================
3. JSON
read_json：
python
运行
json_read = pd.read_json("./data/Sarcasm_Headlines_Dataset.json", orient="records", lines=True)

to_json：
python
运行
json_read.to_json("./data/test.json", orient='record