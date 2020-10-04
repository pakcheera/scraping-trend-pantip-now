# scraping-trend-pantip-now
from pandas_datareader import data as pd
import requests
from requests.auth import HTTPBasicAuth
s = requests.Session() 
Req = s.get("https://pantip.com/forum",auth=HTTPBasicAuth('pakcheerac@gamil.com','password123'))
from bs4 import BeautifulSoup
soup = BeautifulSoup(Req.text,'html.parser')
#print(soup.prettify())
Result1 = soup.find_all('div',attrs = {'class':'pt-list-item__title'})
Result1_1 = soup.find_all('div',attrs = {'class':'pt-list-item__tag'})
Result2 = soup.find_all('div',attrs = {'class':'pt-list-item__info'})
Result3 = soup.find_all('span',attrs = {'class':'pt-li_stats-comment'})
Result4 = soup.find_all('span',attrs = {'class':'pt-li_stats-vote'})
from datetime import datetime
def date(time):
    time=time.split()
    M1 = ["มกราคม","กุมภาพันธ์","มีนาคม","เมษายน","พฤษภาคม","มิถุนายน","กรกฎาคม","สิงหาคม","กันยายน","ตุลาคม","พฤศจิกายน","ธันวาคม"]
    M2=['1','2','3','4','5','6','7','8','9','10','11','12']
    for i in range(12):
        if time[1]==M1[i]:
            time[1]=M2[i]
            return datetime.strptime(time[0]+time[1]+time[2]+time[3]+time[4]+time[5],'%d%m%Yเวลา%H:%Mน.')
            data=[]
for i in range(len(Result1)):
    fr1=Result1[i]
    Topic_Url=fr1.find('a')['href']
    Topic=fr1.a.text
    Message=fr1.span.a.text
    fr2=Result2[i]
    B=fr2.find('div')['style']
    Background_image=B[21:-1]
    Date=date(fr2.find('span')['title'])
    User=fr2.h5.a.text
    fr1_1=Result1_1[i]
    fr1_1=fr1_1.find_all('a',attrs = {'class':'gtm-latest-topic gtm-topic-layout-compact gtm-topic-type-filter-all'})
    Tag=''
    for i in range(len(fr1_1)):
        tag=fr1_1[i]
        tag=tag.text
        Tag=Tag+tag+', '
    if len(Tag)==0:
        Tag='ไม่มี, '
    fr3=Result3[i]
    Comment=fr3.text[7:]
    fr4=Result4[i]
    Vote=fr4.text[7:]
    data.append((Date,Topic,Tag[:-2],Message,User,Comment,Vote,Background_image,Topic_Url
import pandas as pd
df = pd.DataFrame(data,columns=['Date','Topic','Tag','Description',"User'name",'Comment','Vote','Background_image','Topic_Url'])
df=df.sort_values('Date',ascending=True)
df=df.reset_index()
df=df.drop(['index'],axis=1)
df.to_csv('pantip.csv',index=False,encoding='utf-8-sig')
display(df)
