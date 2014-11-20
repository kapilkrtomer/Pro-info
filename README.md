Pro-info
========






import requests
from bs4 import BeautifulSoup
from lxml import html
import phan_proxy
import time
import req_proxy
import sys
import re
import ast


special_char = ['-','! ', ' " ', '# ', '$ ', '%', '& ', "'", '(', ')', '* ', '+', ', ',  '. ', '/ ', ':', '; ', '<', '=', '> ', '? ', '@ ', '[', '\\ ', '] ', '^ ', '_ ', '` ', '{ ', '| ', '} ', '~ ', '\xc2\xb4 ', '#! ', '/* ', '*/ ', '&amp;', '']

special_char = map(str.strip, special_char)



def my_strip(x):
    try:
       # x = str(x).replace("\n", "").replace("\t", "").replace("\r","").replace(",","").strip()
        x = str(x)
    except:
        #x = str(x.encode("ascii", "ignore")).replace("\n", "").replace("\t", "").replace("\r","").replace(",","").strip()
        x = str(x.encode("ascii", "ignore"))
    return x

def main(line,f1):
#def main(link):
    line =ast.literal_eval(line)
    link = line[-1].strip()
    #print line
   # sys.exit()
    driver = phan_proxy.main(link)
    page = driver.page_source

   
    driver.delete_all_cookies()
    driver.quit()
    #page = req_proxy.main(link)
    #f= open("myhtml.html","w+")
    #f.write(str(page)+"\n")
    soup = BeautifulSoup(page,"html.parser")

    #pid = soup.find("div",attrs={"class":"proImg"}).img.get("pagespeed_url_hash")
    pid = soup.find("span",attrs={"class":"skuLi"}).get_text().split("#")[-1].strip()
    #print pid

    pimage =soup.find("div",attrs={"class":"proImg"}).img.get("data-zoom-image")
    #print pimage

    sort_image_link = pimage.split("/")[-1].strip()
    #ptitle =soup.find("meta",attrs={"name":"keywords"}).get("content").strip().replace(",","").strip()
    ptitle = soup.find("div",attrs={"class":"content_inner"}).h1.get_text().replace(",","").strip()
    #print ptitle

    product_title_zovon = "-".join("".join([c for c in ptitle if c not in special_char]).split()).lower()
    #try:
    #    tlink = soup.find("div",attrs={"class":"breadcrumbs"}).find_all("a")[1].get("href")
  
   # except:
    #    tlink =soup.find("div",attrs={"class":"breadcrumbs"}).find_all("a")[0].get("href")

    #print tlink


    #cat2 = soup.find("div",attrs={"class":"breadcrumbs"}).find_all("a")[1:]

    #print cat2

    #try:
     #   cat1 = cat2[-2].get_text().strip()
      #  scat = cat2[-1].get_text().strip()
       # tlink = cat2[-1].get("href")

   # except:
    #    cat1 = cat2[-1].get_text().strip()
     #   tlink = cat2[-1].get("href")
      #  scat = cat1


    #print cat1
    #print scat

    if(len(line)==5):
        cat1 = line[0].lower()
        scat = line[1].lower()
        sscat = line[2].lower()
        tlink = line[3]
        ss_link = tlink

    else:
        cat1 = line[0].lower()
        scat = line[1].lower()
        sscat = line[1].lower()
        tlink = line[1]
        ss_link = tlink


    sp ="Rs."+ str(soup.find("div",attrs={"class":"proPrice"}).span.get_text().replace(",",""))
    #print sp

    try:
        mrp = "Rs."+str(soup.find("div",attrs={"class":"oldPrice"}).find("span",attrs={"itemprop":"price"}).get_text().replace(",",""))

    except:
        mrp = sp
    #print mrp
  
    size =[]
    try:
        size1 = soup.find("div",attrs={"class":"availSize clearfix"}).find_all("li")
        for s1 in size1[0:-1]:
            s2 = s1.get_text()
            size.append(s2)
        size =map(my_strip,size)
   
    except:
        size =" "

    #print size

    colour =[]
 
    try:
        colour1 = soup.find("ul",attrs={"class":"product_colors detail_sections clearfix"}).find_all("li")
        for c1 in colour1:
            c2 = c1.a.get("title")
            colour.append(c2)
   
        colour =map(my_strip,colour)

    except:
        colour = " "

    #print colour

    target = "women"

    plink = link

    brand = soup.find("div",attrs={"class":"brand_default_img"}).h3.get_text().strip()
    #print brand

    seller = "zivame"

    mtitle = soup.find("title").get_text().replace(",","").strip()

    mdesc = soup.find("meta",attrs={"name":"description"}).get("content").replace(",","").strip()

    try:
        desc = soup.find("div",attrs={"class":"proDetCnt"}).get_text()

    except:
        desc = " "
   
    spe =" "

    status1 = soup.find("div",attrs={"class":"qtyfrom detail_sections clearfix"})
    if (status1 == None):
        status ="IA"

    else:
        status = "A"
    #print status

    desc , spe = tuple(map(my_strip, [desc, spe]))

    desc = "  ".join(desc.split())
    spe = "  ".join(spe.split())


    now = time.strftime("%c")

    info = [pid, ptitle,product_title_zovon, tlink, sp, cat1, scat,sscat,ss_link, brand, pimage,sort_image_link, mrp, colour,
            target, plink,seller ,mtitle, mdesc, size, desc, spe, now, status]

    info2 = map(my_strip,info)
    #print info2
    f1.write(str(info2)+"\n")







if __name__ =="__main__":
    f = open("mycsv.csv","w+")
    line ="['Panty', 'Coverage', 'Low Waist', 'http://www.zivame.com/buy-lingerie-online/low-waist-brief.html', 'http://www.zivame.com/buy-lingerie-online/low-waist-brief/enamor-floral-brief.html']"

 #   line =['Bras', 'Everyday', 'T-shirt', 'http://www.zivame.com/buy-bra-online/t-shirt-bra.html', 'http://www.zivame.com/buy-bra-online/t-shirt-bra/avirate-basic-comfy-molded-underwired-bra.html']

 #   line = ['Bras', 'Collection', 'TeenageNew', 'http://www.zivame.com/teens/teen-bras.html', 'http://www.zivame.com/teens/teen-bras/bwitch-full-coverage-beginner-bra.html']
    #link ="http://www.zivame.com/brands-1/enamor-bra/enamor-cotton-floral-everyday-non-padded-bra.html"
    #link ="http://www.zivame.com/buy-bra-online/t-shirt-bra/biara-everyday-lightly-padded-non-wired-bra.html" 
    #link = "http://www.zivame.com/moonbasa-mesh-panelled-suave-sleeveless-top.html"
    main(line,f)
