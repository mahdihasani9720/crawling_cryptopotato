# crawling_cryptopotato
import requests
from bs4 import BeautifulSoup
from newspaper import Article
from tqdm import tqdm
import pandas as pd 

 

# crawling in cryptopotato.com/category/
def scarab_page():
    page = 0
    productLink =[]
    scrapdata = []

    while(True):      
        page +=1
        main_url = f'https://cryptopotato.com/category/editorials/page/{page}/'
        html = requests.get(main_url).text
        soup = BeautifulSoup(html , features = 'lxml')

        # Stop while
        if( soup.find('article' , class_='post no-results not-found hentry')):
            print(' web page is end.....')
            break

        # if in tag <div class="blog-entry-readmore clr"> we can find url_pages
        links = soup.find_all('h3' , class_='media-heading')
        print(f"crawling page: {page}")
        for link in tqdm(links):
            # find url_page
            page_url = link.a['href']
            productLink.append(page_url)

            # we use Article package to extract information pages
            try:
                article = Article(page_url)
                article.download() # download pages
                article.parse()
                # append information page in scrapdata
                scrapdata.append({'url': page_url , 'title': article.title, 'summery': article.summary , 'text': article.text})
            except:
                print(f' Failed to page: {page_url}')    
    
 
    # save information pages to cryptopotato_category.csv
    df = pd.DataFrame(scrapdata)
    df.to_csv('cryptopotato_category.csv')
    

if __name__== '__main__':
    scarab_page() 
   
