
#  BeautifulSoup 爬ITHOME新聞
描述:<br>
1.每10分鐘更新一次，ithome首頁約15個新聞。<br>
2.這個爬蟲會抓取新聞圖片下來，用於判斷該新聞是否為新的新聞，缺點是使用久了會有容量的問題。

#  使用的工具

function <font style=color:blue>replace_title </font> :用於移除html元素，
beautifulsoup 抓完的值要先轉為 str 型態再使用replace替換。
```python
a=coma
a=str(a).replace('[<a href="/article/','')
```
<font style=color:blue> pymysql </font> 使用SQL指令寫入資料庫。
```python
conn= pymysql.connect(host='localhost', port=3306, user='root', passwd='',charset='UTF8')
cur=conn.cursor()
cur.execute("USE ithome")
                
cur.execute('select from where')
cur.close()                                    
conn.commit()                                  
conn.close()  
```

<font style=color:blue> try except </font> 使用try 來持續運作。
```python
try:
    xxxx
except BaseException as a:
  print (a)
```

# code
```python
# -*- coding: utf-8 -*-


from bs4 import BeautifulSoup
import urllib.request as urllib2
from urllib.request import urlretrieve
import os
from datetime import datetime
import pymysql
from time import sleep
def replace_title(coma):
    a=coma
    a=str(a).replace('[<a href="/article/','')
    a=str(a).replace('[<a href="/news/','')
    a=str(a).replace('[<a href="/people/','')
    a=str(a).replace('[<a href="/review/','')
    a=str(a).replace('" width="300"/>]','')
    a=str(a).replace('[<div class="summary"> ','')
    a=str(a).replace(' title=示意圖，與新聞事件無關。','')
    a=str(a).replace('</font>','')
    a=str(a).replace('<time>','')
    a=str(a).replace('</time>','')
    a=str(a).replace('[','')
    a=str(a).replace(']','')
    a=str(a).replace('  </div>]','')
    a=str(a).replace(' target="_blank">','\ntime:')
    a=str(a).replace('<h1>','')
    a=str(a).replace('</h1>','')
    a=str(a).replace('</a>','')
    a=str(a).replace('"','')
    
    return a

headers = {'User-Agent':"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1 (KHTML, like Gecko) Chrome/22.0.1207.1 Safari/537.1"}
html_sample = 'http://www.ithome.com.tw/'


try:
    
    x=1
    while x>0 :
        i=1
        request = urllib2.Request(html_sample,headers=headers)
        start_html1 = urllib2.urlopen(html_sample) 
        soup1 = BeautifulSoup(start_html1)
        
        while i < 15 :
            print ("search for "+ str(i) + "news ...")
            
            if str(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(i)+') > div > span > div > p:nth-of-type(3) > a')) != '[]' :
                
                it_title = str(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(i)+') > div > span > div > p:nth-of-type(3) > a'))[str(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(i)+') > div > span > div > p:nth-of-type(3) > a')).find('">')+2:]
                it_url= str(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(i)+') > div > span > div > p:nth-of-type(3) > a'))[:str(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(i)+') > div > span > div > p:nth-of-type(3) > a')).find('">'):]
                it_img=  str(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(i)+') > div > span > div > p:nth-of-type(1) > a > img'))[str(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(i)+') > div > span > div > p:nth-of-type(1) > a > img')).find('src=')+5:]
                
                if it_img.find('.jpg') > 0 :
                    it_img2= it_img[:4+it_img.find('.jpg')]
                if it_img.find('.png') > 0 :
                    it_img2= it_img[:4+it_img.find('.png')]
                
                it_com= replace_title(soup1.select('#block-views-latest-news-block-3 > div > div:nth-of-type(1) > div:nth-of-type('+str(1)+') > div > span > div > div '))[:123]+"..."
                
                
                it_title= replace_title(it_title)
                
                it_img=replace_title(it_img)
                
                if not os.path.exists('C:/kenson-python/kensontest2/static/'+replace_title(it_url)+".jpg"):
                    
                    localtime =str(datetime.now().strftime('%H%M'))
                    
                    urlretrieve(it_img2, "C:/kenson-python/kensontest2/static/"+replace_title(it_url)+".jpg")
                    
                    conn= pymysql.connect(host='localhost', port=3306, user='root', passwd='',charset='UTF8')
                    
                    cur=conn.cursor()
                    cur.execute("USE ithome")
                    
                    cur.execute("INSERT INTO newsdata(title,photo,url) values('"+replace_title(it_title)+"','"+replace_title(it_url)+".jpg','"+html_sample +(it_url).replace('[<a href="/','')+"')")
                    
                    cur.close()                                    
                    conn.commit()                                  
                    conn.close()  
                    
                    
                     
            i+=1        
        print ("10 mins later update ...")
        sleep(600)
        
except BaseException as a:
    print (a)
    
```



