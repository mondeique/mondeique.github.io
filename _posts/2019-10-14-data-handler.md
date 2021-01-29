---
layout: post
title: "가방 crawling data handler"
author: "Eren"
tags: monde
---

Mondeique는 `여성 가방을 자동으로 인식하고 카테고리를 자동으로 추출하는 가방 검색 서비스` 라는 아이디어로 2019 하반기 예비창업패키지를 수행했습니다. 이런 아이디어에 기반은 AI 개발이었고 이를 위해서는 많은 데이터들이 필요했고 Amazon 에서 제공하는 open dataset 들을 활용하고 데이터 전처리 과정을 통해 학습에 용이하게 만들어야만 했습니다.
<br>

본 포스트는 이러한 방대한 data 들을 Mondeique 에서는 어떻게 crawling 을 했었고 h5py file 을 변환했는지 서술하고자 합니다.
<br>
1. Amazon h5py to jpg converter
2. web-crawling
 
<hr>
<br>
 
## Amazon h5py to jpg converter

Amazon에서는 open dataset 으로 이런 방대한 data 를 제공했습니다. 그렇지만 해당 file은 h5py 파일 형태였고 우리는 이를 jpg로 변환해야만 했습니다. code는 생각보다 아주 간단했습니다. 단순히 h5py 가 가지고 있는 key를 파악한다음 array로부터 읽어드린 뒤 local 에 저장하기만 하면 됐습니다.

{% highlight python %}
hdf = h5py.File('./data/bag_image/handbag_128.hdf5', 'r')
print(hdf.keys()) : <KeysViewHDF5 ['imgs']>

new_height = 227
new_width = 227

with h5py.File('./data/bag_image/handbag_128.hdf5', 'r') as f:
    data = f['imgs']
    for i in range(len(data)):
        im = Image.fromarray(data[i])
        new_im= im.resize((new_height, new_width), Image.ANTIALIAS)
#         im.save("./data/bag_image/hdf5_bag_128_{}.jpg".format(i)) # 128*128 로 저장하는 경우에 사용
        new_im.save("./data/bag_image/hdf5_bag_227_{}.jpg".format(i))
{% endhighlight %}

## web-crawling 

web crawling 의 경우 쇼핑몰마다 form 이 조금씩은 다르기 때문에 홈페이지 각각을 분석해 `BeautifulSoup` 을 이용해서 crawling 을 진행했었습니다. 아래는 luzzibag 이라는 쇼핑몰을 예시로 가져왔습니다.

{% highlight python %}
## 숄더백

print("\n")
print("---------------------------------------------------------------------------")
print("숄더백 사진 url 다운로드를 시작합니다")
print("---------------------------------------------------------------------------")
print("\n")

current_page = 1

img_url_list = []
shoulder_img_url_list = []
            
while current_page <= 2:
    print(str(current_page) + "번째 페이지 이미지를 다운로드 중입니다!")
    url = 'http://www.luzzibag.com/category/%EC%88%84%EB%8D%94%EB%B0%B1/27/' + '?page=' + str(current_page)
    web = urlopen(url)
    source = BeautifulSoup(web, 'html.parser')
    for a in source.find_all('div', {"class" : "prdImg_thumb" }):
        for url in a.find_all("img"):
            if url["src"].startswith('//www.luzzibag.com/web/product/tiny'):
                img_url_list.append(url["src"])
                
    current_page = current_page + 1 
    time.sleep(5)

shoulder_img_url_list = img_url_list
                

## 토트백

print("\n")
print("---------------------------------------------------------------------------")
print("토트백 사진 url 다운로드를 시작합니다")
print("---------------------------------------------------------------------------")
print("\n")

img_url_list = []
tote_img_url_list = []
            
print("1번째 페이지 이미지를 다운로드 중입니다!")
url = 'http://www.luzzibag.com/category/%ED%86%A0%ED%8A%B8%EB%B0%B1/28/'
web = urlopen(url)
source = BeautifulSoup(web, 'html.parser')
for a in source.find_all('div', {"class" : "prdImg_thumb" }):
    for url in a.find_all("img"):
        if url["src"].startswith('//www.luzzibag.com/web/product/tiny'):
            img_url_list.append(url["src"])
            
tote_img_url_list = img_url_list 

{% endhighlight %}

이렇게 만든 list를 간단하게 정제한 뒤에 csv file 로 저장합니다. 보통 csv file 로 저장하는 이유는 나중에 학습에서 DataLoader 가 훨씬 쉽게 데이터를 불러오게 하게 하기 위해서입니다.

{% highlight python %}
## 겹치는 image 발견해서 삭제하기 

total_img_url_list = shoulder_img_url_list + tote_img_url_list 

len(total_img_url_list)

total_img_url_list = list(set(total_img_url_list))

len(total_img_url_list)

## Write to csv file 

import csv

with open('Luzzi_all_bag.csv', 'w', newline='') as f:
    writer = csv.writer(f, quoting=csv.QUOTE_ALL)
    writer.writerow(total_img_url_list)
    
{% endhighlight %}

이번 포스트에서는 handbag dataset 을 어떻게 가져오고 정제했는지에 대해서 서술했습니다. 사실 포스트로도 남길 필요가 없을 정도로 간단한 라이브러리와 논리를 사용했지만, `web-crawling` 이라는 분야에 생소함을 느껴서 어려움을 겪는 사람들에게 실제 예시를 보여주는 것이 훨씬 정보를 습득하기에 용이하다고 생각했습니다. 간단한 알고리즘도 차근차근 stack을 쌓아가며 적어가는 습관이 중요하다고 생각하기에 이 글도 언젠간 가치가 있는 글이 될 것이라고 생각합니다 👍 







