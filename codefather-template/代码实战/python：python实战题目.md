# python实战题目

## 1.新冠疫苗
### 1.1 版本1
```python
#函数一：判断是否为闰年(1:是、0:不是)
def is_leapyear(year):
    if(year%400==0 or (year%4==0 and year%100!=0)):
        return 1
    return 0

#函数二：返回该年输入月份的天数
def month_day(year,month):
    if month==1 or month==3 or month==5 or month==5 or month==7 or month==8 or month==10 or month==12:
        return 31
    elif month==2:
        if is_leapyear(year)==1:
            return 29
        else:
            return 28
    else:
        return 30

#函数三：返回下一次打针时间的列表[年，月，日]
def add_nextday(interval,time):  #已知间隔时间求下次接种时间
    year=time[0]  #当前年份
    month=time[1]  #当前月份
    day=time[2]  #当前日期
    while(interval>0):
        left=month_day(year,month)-day  #当月剩下天数
        if interval>left:  #当月剩下天数<间隔时间
            interval-=left  #间隔时间变为间隔时间-当月剩余时间
            day=0  #day变为0
            month += 1  # 月份+1
        else:  #当月剩余天数>间隔时间
            day=day+interval  #day变为day+间隔时间
            interval=0  #间隔时间变为0
        if month>12:  #月份>12
            month=1 #月份变为1月
            year+=1 #年份+1
    return [year, month, day]  #返回列表下次接种：[年，月，日]

#函数四：判断是否到时间可以打下一针（1：可以、0：不可以）
def is_already(time,cur):   #time表示下一针日期的列表[年，月，日]，cur表示当前日期的列表[年，月，日]
    if cur[0]>time[0]:  #当前年份大于下一针年份
        return 1
    elif cur[0]==time[0]:  #cur、time同年
        if cur[1]>time[1]:  #cur>time的月份
            return 1
        elif cur[1]==time[1] and cur[2]>time[2]: #cur、time同月，cur日更大
            return 1
    return 0 #以上都不满足，不可打，返回0

#函数五：输入上一针时间time:(”year-month-day“)，当前时间cur:(”year-month-day“) 求得dict={"是否可打下一针":"下一针打针时间"}
def predict_time(count,time,cur):  #传入上一针的针数count，上一次打针时间time，当前时间cur

    time=time.split("-")  #以-为分隔符将字符串分割成列表
    cur=cur.split("-")
    for i in range(3):
        time[i]=int(time[i])  #对字符列表的每一个字符型整数转int类型
        cur[i]=int(cur[i])

    dict={}  #字典dict={“是否到下次打针时间”:"下次打针年月日"}
    if count==3:  #上一次打的第三针直接返回
        return {False:''}

    next_time=[]  #列表next_time保存下一次打针的[年,月,日]
    if count==1:  #上一次打的第1针
        next_time=add_nextday(30,time)  #返回下一次打针的的整型年月日列表
    else:
        next_time=add_nextday(180,time)  #返回下一次打针的的整型年月日列表

    flag=False  #标记不可打针
    if is_already(next_time,cur)==1:  #已经到时间
        flag=True  #标记可以打针

    next_time = str(next_time[0]) + "-" + str(next_time[1]) + "-" + str(next_time[2])   #下一针的打针年月日转字符串
    dict={flag:next_time}
    return dict


n=int(input("输入要预测打针时间的数据总数:"))
result=[]  #最终的输出列表：储存每一次输入得到的相关数据
cur=input("输入当前日期(格式：年-月-日):")
for i in range(n):
    count=int(input("输入上一次的针数:"))
    if count==3:
        predict={False:''}
        print(predict)
    elif count==0:
        predict={True:cur}
        print(predict)
    else:
        last_time=input("输入上一次打针的日期(格式：年-月-日):")
        predict=predict_time(count,last_time,cur)
        print(predict)
    result.append(predict)
print(result)

```

### 1.2 版本2
```python
#函数一：判断是否为闰年(1:是、0:不是)
def judge_year(year):
    if year%4!=0 or (year%4==0 and year%400!=0):
        return 0
    return 1

#函数二：返回该年输入月份的天数
def get_day(year,month):
    month_31=[1,3,5,7,8,10,12]
    if month in month_31:   #传入的month在天数为30的列表month_30中(大月)
        return 31  #返回31
    elif month==2:   #月份为2
        if judge_year(year)==1:  #闰年
            return 29
        else:
            return 28    #平年
    else:
        return 30   #小月

#函数三：传入字符串类型的接种时间("year-month-day")返回[year,month,day]
def string_tolist(string):
    string=string.split("-")  #按-为分隔符分割成列表
    for i in range(3):
        string[i]=int(string[i])
    return string

#函数三：传入列表类型的接种时间[year,month,day]，返回("year-month-day")
def list_tostring(l):
    l=str(l[0])+'-'+str(l[1])+'-'+str(l[2])
    return l

#函数四：传入字符串形式的上一次的接种时间，返回字符串形式的下一次的接种时间
def get_nexttime(last_s,count):  #last_s:上一次接种时间，count:上一次接种针数
    wait_time=0
    if count==1:
        wait_time=30  #第一针等待30天
    elif count==2:
        wait_time=180  #第二针等待180天
    last=string_tolist(last_s)  #转成整型列表
    #将下次接中的时间初始化为当前时间
    lst=[]
    next_year=last[0]
    next_month=last[1]
    next_day=last[2]
    while(wait_time>0):
        rest=get_day(next_year,next_month)-next_day  #当前月的剩下天数
        if wait_time<=rest:
            next_day=next_day+wait_time
            break
        else:
            wait_time=wait_time-rest  #还要等的天数
            next_day=0
            next_month=next_month+1  #月份+1
        if next_month>12:  #月份超过了12即垮了年
            next_month=1  #月份变为1月
            next_year=next_year+1  #年份+1
    lst=[next_year,next_month,next_day]
    lst=list_tostring(lst)   #转为字符型
    return lst

#函数五：传入下一次接种时间next_time的字符串形式，当前日期now的字符串形式,next_time在now之前返回1(可以打)，否则返回0(不可以打)
def compare(next_time,now):
    next_time=string_tolist(next_time)
    now=string_tolist(now)
    if next_time[0]<now[0]:  #now年份大
        return 1
    elif next_time[0]>now[0]:  #now年份小
        return 0
    else:  #同年
        if next_time[1]<now[1]:  #now月份大
            return 1
        elif next_time[1]>now[1]:  #now月份小
            return 0
        else:
            if next_time[2]<now[2]:
                return 1
            else:
                return 0

#函数六：根据输入的列表,和当前时间预测
def get_list(input_list,now):
    N=len(input_list)  #数据总数(即输入的列表中的字典总数)
    out_put=[]   #最终输出的列表
    for i in range(N):
        d={}
        flag=False  #flag=False标记为不可以打
        data=input_list[i]  #data为列表中的每一个字典
        count=int(list(data.items())[0][0])  #字典的键为上一针的针数(整数)
        last=list(data.items())[0][1]  #字典的值为上一针打的时间(字符串)
        if count==0:
            d={True:now}
            out_put.append(d)
            continue
        if count==3:
            d={False:''}
            out_put.append(d)
            continue
        next_time=get_nexttime(last,count)  #获取下一次的接种时间
        if compare(next_time,now)==1:  #下一次的接种时间在今天之前
            d={True:next_time}  #可以接种
        else:
            d={False:next_time}  #不可以接种
        out_put.append(d)  #将d添加进列表
    print(out_put)


input_list=[{'0':''},{'1':'2022-12-04'},{'3':'2022-11-04'},{'1':'2022-10-01'},{'2':'2021-10-01'}]
print('输入：')
print(input_list)
now='2022-12-4'
print('输出：')
get_list(input_list,now)
```


