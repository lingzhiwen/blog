---
title: python
---
1.读取sqlite
```
import sqlite3
conn = sqlite3.connect('test.db')
cursor=conn.cursor() 
cursor.execute("select * from TABLE") #该例程执行一个 SQL 语句
rows=cursor.fetchall() 
print(rows)
```

2.对比json数据
```
import json_tools
result = json_tools.diff(rows,rows1)
```

3.html表格转excel
```
import pandas  
with open('D:\\autovoicestudy\python\\radioTable.html','rb') as f:  
    df = pandas.read_html(f.read(),encoding='UTF-8')   
bb = pandas.ExcelWriter('out.xlsx')  
df[0].to_excel(bb)  
bb.close()
```