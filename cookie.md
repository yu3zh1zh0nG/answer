# 一. Python操作excel
- xlrd 和xlwt
### import xlrd
- 打开Excel工作簿
    - book=xlrd.open_workbook(filename)
- 查看工作薄中所有的sheet
    - book.sheet_names()
- 选择一个工作表
    - [x] sheet=book.sheet_by_name(sheet_name)
    - sheet=book.sheet_by_index(索引序号)
    - sheet=book.sheets()[索引序号]
- 获取表格的行数和列数
    - 行数:
        - nrows=sheet.nrows
    - 列数:
        - ncol=sheet.ncols
- 获取整行和整列的值-->返回数组
    - sheet.row_values(number)
    - sheet.col_values(number)
- 读取单元格的值
    - [x] data=sheet.cell(i,j).value
    - data=sheet.row(i)[j].value
    - data=sheet.col(j)[i].value

### import xlwt
- [ ] 只能通过新建写入
- 新建Excel文件
    - book=xlwt.Workbook()
- 新建一个工作表
    - sheet=book.add_sheet('sheet_name')
- 写入数据
    - sheet.write(i,j,'数据')
- 保存文件
    - book.save('文件名')


### 解决xlwt只可以新建,不可更改现有Excel问题
1. 导入必要模块
    - import xlrd
    - from xlutils.copy import copy
2. 打开文件
    - book = xlrd.open_workbook(filename)
3. 复制文件
    - book = copy(book)
4. 选择要update的sheet
    - sheet=book.get_sheet(i)
5. 利用sheet对象对数据进行操作
    - [ ] 此时的sheet是由xlutils模块辅助的,并不是xlwt
    - sheet.write(i,j,'data')
6. 保存
    - book.save(filename)
```
from xlutils.copy import copy
import xlrd

book = xlrd.open_workbook(filename)
book = copy(book)
sheet = book.get_sheet(0)
sheet.write(1,0,'hello')
book.save(filename)
```


# 二. 桶排序-->时间复杂度O(n)
```
import random

# 1.生成要排序的列表
old_list=[]
for i in range(100):
    old_list.append(random.randint(0,100))
print('旧列表',old_list)

# 2. 找到列表中的最大最小值
max_value=old_list[0]
for i in old_list:
    if i>max_value:
        max_value=i
min_value=old_list[0]
for i in old_list:
    if i<min_value:
        min_value=i

# 3. 创建桶,并统计各个值得个数
s_list=[0 for i in range(min_value,max_value+1)]
for i in old_list:
    # i-min_value 为 桶值得索引
    s_list[i-min_value]+=1
print('桶',s_list)

# 4. 通过桶内各个值得个数,将值存入新列表
n=min_value
new_list=[]
for j in s_list:
    while j>0:
        new_list.append(n)
        j-=1
    n+=1
print('新列表',new_list)
```





# 三 . 右键启动CMD
```
按win+r
输入regedit打开注册表
HKEY_CLASSES_ROOT\Directory\Background\shell\

在shell目录右键新建一个叫runas的项
在runas目录右键新建一个叫command的项

在runas上右键，新建一个DWORD32类型叫ShowBasedOnVelocityId的项目，填入值639bc8

点到command里面，右键那个默认，填入
    cmd.exe /s /k pushd "%V"
```































