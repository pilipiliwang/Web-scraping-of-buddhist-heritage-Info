# Web scraping of buddhist heritage Info
 web scraping of ‘https://www.chinabuddhism.com.cn/tp/’ website information. There are 5071 heritage information of Buddhist photos in the website. 
### website information
- Here are the website and scraped information excel.The infomation excel is in this repo called **'little_roundy_info.xlsx'**.

![website](https://raw.githubusercontent.com/pilipiliwang/Web-scraping-of-buddhist-heritage-Info/main/web%26info.png)

- This repo can also download images, but because of the size of data is big, it's better searching for the info you want then download.Here are the codes, you have to get **korean_imgurl** first, I use korean images for test, so the name is korean_imgurl.

      # 存imgurl图片到本地文件夹
      for i in range(0,len(korean_name)):
          path = '/content/drive/MyDrive/little_roundy/little_roundy_imgs/'
          imgpath = path + 'Num'+str(korean_num[i])+korean_name[i] +'.jpg'
          print(imgpath)
          imgurl = korean_imgurl[i]
          r = requests.get(imgurl,stream=True)
          with open(imgpath, 'wb') as f:
              for chunk in r.iter_content():
                  f.write(chunk)

### cautions of coding
- change headers before running, the headers here is 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.67 Safari/537.36' .

      def url_open(url):
          req = urllib.request.Request(url)
          req.add_header("User-Agent","Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.67 Safari/537.36")
          response = urllib.request.urlopen(req)
          html = response.read()
          return html
          
 - The first index page is diffierent from others, so we need to check first.
 
          # 遍历index（pageurl）页面
         pageurl_list = []

         def PageUrl(num): #左闭右开
             for i in range(1,num):
                 if i == 1:#第一个网页
                     pageurl = 'https://www.chinabuddhism.com.cn/tp/index.html'
                     pageurl_list.append(pageurl)
                 else:
                     pageurl = baseurl + '_' + str(i) +'.html'
                     pageurl_list.append(pageurl)
                 # print(pageurl)
             return pageurl_list
             
 - we scrape the [image weburl](https://www.chinabuddhism.com.cn/tp/2017-05-19/5202.html) from [index url](https://www.chinabuddhism.com.cn/tp/),but the weburl of images are diffiernet, so we need to check the weburl name.
 
       # 转译href为image url
       weburl_list = []
       for i in range(0,len(href_list)):
           if 'chinabuddhism' in href_list[i]:
             weburl = href_list[i]
             weburl_list.append(weburl)
           else:
             weburl = 'https://www.chinabuddhism.com.cn'+ href_list[i]
             weburl_list.append(weburl)
           i = i+1
       print(len(weburl_list))
 
 ![weburl](https://raw.githubusercontent.com/pilipiliwang/Web-scraping-of-buddhist-heritage-Info/main/weburl.png)
 
 - **When we get weburl, we need to scrape images from web.However, the position of images differ a little bit. I built two differnet ways for scraping, I tested based on korean_images, I'm not sure if there are other situations here.**
    - The first position
    
          # 抓取weburl中的imgurl,画在align="center"里面
          def getImgUrl_1(weburl):
              web = url_open(weburl).decode('gbk')
              web.encode(encoding='utf-8')
              websoup = BeautifulSoup(web,'html.parser')
              for element in websoup.find_all('div',align="center"):
                  element_img = element.findNext('img')
                  imgurl = element_img.get('src')
                  # print(imgurl)
              return imgurl
           
    - The second position    
    
          # 抓取weburl中的imgurl，画在class="content_zi"里
          def getImgUrl_2(weburl):
              web = url_open(weburl).decode('gbk')
              web.encode(encoding='utf-8')
              websoup = BeautifulSoup(web,'html.parser')
              for element in websoup.find_all('div',class_="content_zi"):
                  element_img = element.findNext('img')
                  imgurl = element_img.get('src')
                  # print(imgurl)
              return imgurl
