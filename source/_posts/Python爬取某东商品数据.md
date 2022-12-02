---
title: Python爬取某东商品数据
date: 2022-12-02 13:02:04
tags:
---

## 准备工作

- 运行环境：
  
  - Python3.7
  
  - selenium3.7.0
  
  - chromedriver.exe

- 注意
  
  - 使用前下载自己浏览器对应版本的驱动
  
  - 将驱动放置在代码同一目录下，非chromedriver驱动请在代码中修改文件名

## 思路

#### 发送访问商品页请求

1. 对[https://www.jd.com](https://www.jd.com)发送访问请求

2. 输入要获取的商品

3. 跳转到搜索页面

#### 读取商品数据并保存

1. 下滑到页面底端（页面商品数据懒加载，需要滑到最后才加载完毕）

2. 使用bs4定位到商品数据的父节点 ul 标签下的所有 li 标签

3. 遍历所有 li 标签，获取每个 li 标签中的商品信息

4. 封装商品信息，追加写入文件

5. 获取到下一页标签，判断该标签是否可以点击
   
   - 可点击，点击跳转到下一页，继续执行读取商品数据并保存函数
   
   - 不可点击，返回

## 注意

1. 由于个别商品属于自营商品，没有店铺信息，需要对获取到的店铺信息进行异常处理

2. 推荐使用bs4进行元素定位，在selenium中使用bs4定位后的元素可以在该元素基础上再次定位，而xpath不行，使用xpath定位，若出现元素缺失，会出现商品数据错位的情况，而bs4的再次定位可以使得定位到的元素相互独立，即使出现元素缺失，也不会影响其他元素数据。

## 代码

```python
"""
@author:Lunau
@file:京东商城bs4.py
@time:2022/12/02
"""
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

name = input('请输入商品名称：')
# 发送请求，获取首页
def get_page():
    chrome_options = webdriver.ChromeOptions()
    chrome_options.add_experimental_option("detach", True)
    web = webdriver.Chrome(chrome_options=chrome_options, executable_path='./chromedriver.exe')
    web.maximize_window()
    # 商品名、价格、商品链接、评论数量、店铺名称。
    web.get('https://www.jd.com')
    # 搜索并跳转
    input = web.find_element_by_xpath('//*[@id="key"]')
    input.send_keys(name)
    input.send_keys(Keys.ENTER)
    return web

# 获取商品内容并保存
def get_content(web,page):
    time.sleep(3)
    # 滑动到页面底部，加载一页商品信息
    location = web.find_element_by_xpath('//div[@class="page clearfix"]')
    web.execute_script('arguments[0].scrollIntoView();',location)

    time.sleep(2)
    # 定位商品价格列表
    goods_list = web.find_elements_by_css_selector(' .gl-warp.clearfix>li')

    print('在第{}页获取到{}个商品'.format(page,len(goods_list)))

    for goods in goods_list:
        desc = goods.find_element_by_css_selector(' .p-name.p-name-type-2 a').text
        price = goods.find_element_by_css_selector(' .p-price i').text
        link = goods.find_element_by_css_selector(' .gl-i-wrap .p-img a').get_attribute('href')
        commit = goods.find_element_by_css_selector(' .p-commit>strong>a').text
        # 解决商品没有店铺
        try:
            store = goods.find_element_by_css_selector(' .p-shop a').text
        except:
            store = 'null'
        # print('商品描述：{}\n价格：{}\n链接：{}\n评论数量：{}\n店铺名称：{}\n'.format(desc,price,link,commit,store))
        with open(f'./jd_{name}_info.txt','a',encoding='utf-8') as fp:
            fp.write('商品描述：{}\n价格：{}\n链接：{}\n评论数量：{}\n店铺名称：{}\n\n'.format(desc,price,link,commit,store))

    time.sleep(2)
    print('第{}页获取到{}个商品下载完毕'.format(page, len(goods_list)))

    # 判断下一页标签是否存在，存在则翻页
    next = web.find_element_by_css_selector('#J_bottomPage > span.p-num > a.pn-next')
    judge = next.get_attribute('href')
    if (judge != None):
        # 点击下一页
        next.click()
        page += 1
        get_content(web,page)
    else:
        return ;

if __name__ == '__main__':
    page = 1
    web = get_page()
    get_content(web,page)![loading-ag-1395](assets/Python——爬取某东商品数据/2022-12-02-12-51-15-image.png)
```

![](assets/Python爬取某东商品数据/6533eb3b2a4b34ca96afd6f03f4d97c0dafbd2cc.png)
