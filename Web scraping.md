```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import numpy as np
from openpyxl import load_workbook


result = requests.get("https://")
src = result.content

soup = BeautifulSoup(src, 'lxml')


def scraping1(soup) :
    bodies= soup.find_all('g',class_='onclick')

    dict={}
    for body in bodies:

        #if body.attrs['class'] == 'container':# or body.attrs['id'] == 'sponsers':
        a_tags = body.find_all('a')
        for a_tag in a_tags:
            try:
                if a_tag.attrs['href'] != "":
                    if str(a_tag.attrs['href']).startswith("http"):
                        x = (str(a_tag.attrs['href']))[25:].split(".")
                        if len(x) == 2:
                            x = x[0].split("//")
                        h = a_tag.find_previous('div',class_='spons_type')
                        dict[x[1]] = [a_tag.attrs['href'], (h.text).strip()]

            except:
                pass
    return dict


def scraping2(soup):
    bodies = soup.find_all('body')

    dict = {}
    for body in bodies:

        #if 'customer-logos' in body.attrs['class']: # or body.attrs['id'] == 'services1':
        a_tags = body.find_all('div',class_='sponsor-wrapper')
        print(a_tags)
        for a_tag in a_tags:
            try:
                a_tag= a_tag.find('img',alt="sponsor")
                if a_tag.attrs['src'] != "":
                    # if str(a_tag.attrs['href']).startswith("http"):
                    x = ((str(a_tag.attrs['src'])).split("."))[-2]
                    # if len(x) == 2:
                    x = x.split("/")[-1]
                h = a_tag.find_next('p')
                dict[x] = [a_tag.attrs['src'], h.text]

            except:
                pass
    return dict

def scraping3(soup):
    bodies=soup.find_all('div',id="spons2016")
    dict={}
    for body in bodies:
        a_tags= body.find_all('a')
        for a_tag in a_tags:
            try:
                if a_tag.attrs['href'] != '#':
                    dict[(a_tag.find_next('h3')).text] = [a_tag.attrs['href'],((a_tag.find_previous('u')).contents[0]).text]
                else:
                    dict[(a_tag.find_next('h3')).text] = [(a_tag.find('img')).attrs['src'],((a_tag.find_previous('u')).contents[0]).text]

            except:
                try:
                    dict[(a_tag.find_next('h3')).text] = [a_tag.attrs['href'], (a_tag.find_previous('h2')).text]
                    pass

                except:
                    pass

    return dict

def scraping4(soup):
    import re
    bodies=soup.find_all('div', class_=re.compile(r'^sponsors'))

    for body in bodies:
        a_tags = body.find_all('img')
        for a_tag in a_tags:
            try:
                if a_tag.attrs['src'] != "":
                    # if str(a_tag.attrs['href']).startswith("http"):
                    x = ((str(a_tag.attrs['src'])).split("."))[-2]
                    # if len(x) == 2:
                    x = x.split("/")[-1]
                h = a_tag.find_next('p')
                dict[x] = [a_tag.attrs['src'], h.text]

            except:
                pass
    return dict

dict={}
#dict=scraping1(soup)
#dict=scraping2(soup)
#dict=scraping3(soup)
dict=scraping4(soup)


df=pd.DataFrame(dict)
df=df.T
df2=df.rename(columns={0:'URLS',1:'Sponsor Tags'})
print(df2)

#with pd.ExcelWriter('.xlsx',engine='openpyxl') as writer:
 #   writer.book = load_workbook('.xlsx')
  #  df2.to_excel(writer,sheet_name='2018')

#df2.to_excel('.xlsx',sheet_name='2020')

```
