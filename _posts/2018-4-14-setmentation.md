---
layout: post
title: "Sentiment Analysis sử dụng Tf-Idf áp dụng cho ngôn ngữ tiếng việt "
description: "phân tích cảm xúc"
categories: [python,machine_learning]
tags: [python,machine learning]
redirect_from:
  - /2018/04/14/
---
Text mining ( lấy thông tin từ text) là một lĩnh vựng rộng và áp dụng trong nhiều lĩnh vực khác nhau. Một số ứng dụng có thể kể đến là :
sentiment analysis, document classification, topic classification, text summarization, machine translation. Trong bài hôm nay ta sẽ tìm
hiểu về sentiment analysis.Phân tích cảm xúc(sentiment analysis) được hiểu đơn giản là đánh giá 1 câu nói, tweet là tích cực (pos) hay tiêu cưc(neg). Chẳng hạn lấy một ví dụ, bạn mở một cửa hàng bán đồ ăn mà muốn biết trên mạng xã hội người ta nói gì về quán ăn của bạn.
Bạn bắt đầu vào face, instagram hay tweeter để thu thập các commnent liên quan đến quán ăn của bạn. Bạn bắt đầu đoc thì có người khen người chê, vấn đề xảy ra là bây giờ số comment nó tăng lên 1000 hay 10000 bạn có đủ sức đọc các comment đó hay không.Bạn bắt đầu nghĩ ra sẽ build một model làm việc đó cho bạn. Ta bắt tay vào công việc.
* Thuật toán sử dung : mình sẽ sử dụng logistic regression kết hợp với kỹ thuật tf-idf
* Library : pyvi(một thư viện xử lý tiếng việt), sklearn
* Các bước thực hiện :
## 1, Chuẩn bị dữ liệu
## 2, Tiền xử lý dữ liệu
## 3, Build model
## 4, Funny một tí
# 1, Chuẩn bị dữ liệu
Dữ liệu text có ở khắp mọi nơi từ facebook đến các website đâu đâu cũng có.Mình sẽ lấy dữ liệu từ trang tripnow.vn một trang web con của foody.vn chuyên về đánh giá các cửa hàng. Để đơn giản mình chỉ lấy comment ở các cửa hàng ở TP.hcm và trên mỗi comment đó có star thang đo từ 1-10 mình sẽ lấy nó làm căn cứ đánh giá comment là pos or neg.
* Bắt đầu chiến dịch cà web nào (crawler). Mình sẽ sử dụng selenium để cà dữ liệu text.
~~~ ruby
#load thu vien
import numpy as np
import selenium
from selenium import webdriver
import time
from selenium.webdriver.common.keys import Keys
#open list name
with open("name.txt") as f:
    names = f.read()
list_name = names.split("\n")
#crawler data
driver = webdriver.Chrome()
path = "https://www.tripnow.vn"
texts = []
scores = []
for name in range(len(list_name)):
    path_link = path + list_name[name] + "/binh-luan"
    driver.get(path_link)
    actions = webdriver.ActionChains(driver)
    count = 0
    while (count<30):
        load_more = driver.find_element_by_xpath("//div/*[@ng-click='LoadMore()']")
        actions.move_to_element(load_more).perform()
        load_more.click()
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        count += 1   
        time.sleep(3)
    text =  driver.find_elements_by_xpath("//div/span[@ng-bind-html='Model.Description']")
    score = driver.find_elements_by_xpath("//li/div/div/div/span[@class='ng-binding']")

    for tx,sc in zip(text,score):
        comment = tx.text
        scoring = sc.text
        texts.append(comment)
        scores.append(scoring)
 ~~~
  ### Giải thích code một tí
  * Để tránh code dài mình lưu các tên cửa hàng ở file `name.txt`
  * Mỗi cửa hàng có rất nhiều comment (có thể vài trăm) nhưng mình chỉ lấy 30 comment ở mỗi cửa hàng vì máy mình tương đối yếu load
  nhiều máy chạy không nổi.
  * Comment được lấy từ `//div/span[@ng-bind-html='Model.Description` và lưu vào biến `texts`
  * Score được lấy từ `//li/div/div/div/span[@class='ng-binding'` và lưu vào biến `scores'
  * Mình cho nó chạy tầm 2 tiếng thu được tầm 6000 comment và được lưu dưới dạng text
  
  ![text](/assets/images/text.jpg)
    