# hello
## 第一步：爬取数据（发送请求并得到响应）
import requests
import time
def gettime():
    return int(round(time.time() * 1000))
if __name__ == '__main__':
    url = 'http://data.stats.gov.cn/easyquery.htm'
    headers = {}
    headers['User-Agent'] = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_3)' \
                            ' AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36'
    keyvalue = {}
    keyvalue['m'] = 'QueryData'
    keyvalue['dbcode'] = 'hgyd'
    keyvalue['rowcode'] = 'zb'
    keyvalue['colcode'] = 'sj'
    keyvalue['wds'] = '[]'
    keyvalue['dfwds'] = '[{"wdcode":"zb","valuecode":"A030102"}]'
    keyvalue['k1'] = str(gettime())
    # 发出请求，使用get方法，这里使用我们自定义的头部和参数
    r = requests.get(url, headers=headers, params=keyvalue)
    # 建立一个Session
    s = requests.session()
    # 在Session基础上进行一次请求
    r = s.get(url, params=keyvalue, headers=headers)

print(r.status_code)
#print(r.text)
print(r.headers)
print(url)
print()

## 第二步：数据处理（解析数据）
import json
data = json.loads(r.text)
#print(data)
data_one = data['returndata']['datanodes']
print(data_one)
print(data_one[0])
month=[]
oil=[]
for dic in data_one:
    if 'A03010201_sj' in dic['code']:
        month.append(dic['code'][-6:])
print(month)
print(len(month))
for dic in data_one:
    oil.append(dic['data']['strdata'])
print(oil)
print(len(oil))
oil_list=[]
for i in range(0,len(oil),13):
    oil_list.append(oil[i:i+13])
print(oil_list)
print(len(oil_list))

## 第三步：数据保存（保存为csv格式）
import csv

def oil_csv():
    with open('oil.csv','w',newline='')as f:
        writer=csv.writer(f)
        writer.writerow(month)
        for j in range(len(oil_list)):
            writer.writerow(oil_list[j])
oil_csv()

## 第三步：数据保存（保存为excle格式）
import xlwt
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('原油产量')
for row in range(len(month)):
    worksheet.write(0,row,month[row])
for i in range(len(oil_list)):
    for j in range(len(month)):
        worksheet.write(i+1,j,oil_list[i][j])
workbook.save('oil.xls')


## 第四步：图形绘制
print(oil_list)
oil_dangqi=[]
oil_leiji=[]
oil_tongbizeng=[]
oil_leijizeng=[]
oil_dangqi.append(oil_list[0])
oil_leiji.append(oil_list[1])
oil_tongbizeng.append(oil_list[2])
oil_leijizeng.append(oil_list[3])
print(oil_dangqi[0])
print(oil_leiji[0])
print(oil_tongbizeng[0])
print(oil_leijizeng[0])

import matplotlib.pyplot as plt
import matplotlib
input_values = month #图形输入值
output_values = oil_dangqi[0] #图形输出值
plt.plot(input_values,output_values,linewidth=5,label='dangqizhi')#plot根据列表绘制出有意义的图形，linewidth是图形线宽，可省略
plt.plot(input_values,oil_leiji[0], linewidth=5,color='cyan', label='leijizhi')
plt.plot(input_values, oil_tongbizeng[0], 'b', label='tongbizeng')#'b'指：color='blue'
plt.plot(input_values, oil_leijizeng[0], 'r', label='leijizeng')
plt.legend()  #显示上面的label
plt.title("oil",fontsize = 24) #设置图标标题
plt.xlabel("month",fontsize = 14) #设置坐标轴标签
plt.ylabel("the annual oil yield",fontsize = 14)
plt.tick_params(axis='both',labelsize = 8) #设置刻度标记的大小
plt.show()#打开matplotlib查看器，并显示绘制图形


plt.bar(input_values, output_values,label='dangqizhi')
plt.legend()
plt.xlabel('salary-group')
plt.ylabel('salary')
plt.title("oil",fontsize = 24)
plt.show()

plt.bar(input_values, oil_leiji[0],label='leijizhi')
plt.legend()
plt.xlabel('salary-group')
plt.ylabel('salary')
plt.title("oil",fontsize = 24)
plt.show()

