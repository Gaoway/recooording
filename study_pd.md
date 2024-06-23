Python中的列表（list）导出到Excel文件，可以使用pandas库

import pandas as pd
# 将列表转换为DataFrame
df1 = pd.DataFrame(list1, columns=["Column1"])
df2 = pd.DataFrame(list2, columns=["Column2"])
df3 = pd.DataFrame(list3, columns=["Column3"])
# 使用ExcelWriter导出到同一个Excel文件
with pd.ExcelWriter('output.xlsx') as writer:
    df1.to_excel(writer, sheet_name='Sheet1', index=False)
    df2.to_excel(writer, sheet_name='Sheet2', index=False)
    df3.to_excel(writer, sheet_name='Sheet3', index=False)

my_dict = {
    "Column1": [1, 2, 3, 4, 5],
    "Column2": ['a', 'b', 'c', 'd', 'e'],
    "Column3": [True, False, True, False, True]
}
df = pd.DataFrame(my_dict)
df.to_excel("output.xlsx", index=False)


