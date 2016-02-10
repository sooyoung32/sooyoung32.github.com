---
layout: post
title:  "스크래피 튜토리얼 (Scrapy tutorial) 번역"
date:   2016-02-07
author: Sooyoung
categories: dev
tags: dev scrapy
cover:  "/assets/img/scrapy.jpg"
---


## Scrapy tutorial
> [scrapy tutorial](http://doc.scrapy.org/en/1.0/intro/tutorial.html) 번역입니다. 번역이 미흡한점 미리 양해 드립니다.


### Scrapy Tutorial
이번 튜토리얼 에서는 우리는 당신의 시스템에 스크래피가 이미 설치되어 있음을 가정합니다. 만약 설치되지 않았다면 [이 설치 가이드](http://doc.scrapy.org/en/1.0/intro/install.html#intro-install)를 따라주세요.


우리는 [Open directory project(dmoz)](http://www.dmoz.org/)를 우리의 스크랩할 예시 도메인으로 사용할 것입니다.

이번 튜토리얼에서는 우리는 다음과 같은 스텝으로 진행할 것입니다.

1. 새로운 스크래피 프로젝트를 생성한다.
2. 우리가 추출할 아이템을 정의한다
3. 사이트를 크롤링할 스파이더를 작성하고 아이템을 추출한다.
4. 추출된 아이템을 저장할 아이템 파이프라인을 작성한다.

스크래피는 파이썬으로 작성되어 있습니다. 만약 당신이 파이썬이 처음이시라면 당신은 아마도 스크래피 이상의 것을 알기 위해 이 언어가 어떤가에 대해 알고 싶어할 수 도 있습니다. 만약 당신이 다른 언어에 익숙해 파이썬을 빠르게 알고 싶어 하신다면 우리는 [Learn Pthon the Hard Way](http://learnpythonthehardway.org/book/) 추천합니다. 만약 당신이 프로그래밍이 처음이고 파이썬을 배우고 싶다면 [프로그래머가 아닌 분들을 위한 파이썬 배우기](https://wiki.python.org/moin/BeginnersGuide/NonProgrammers)를 추천합니다.

### Creating a project

스크래핑을 시작하기 전에 당신은 스크래피 프로젝트를 설정해야한 할 것입니다. 당신이 코드를 실행하고 저장할 디렉토리에서 다음과 같은 명령을 작성하고 엔터를 누르세요

> scrapy startproject tutorial

하셨다면 해당 디렉토리에 다음과같은 컨텐츠가 생성될 것입니다.
```
tutorial/
    scrapy.cfg            # deploy configuration file

    tutorial/             # project's Python module, you'll import your code from here
        __init__.py

        items.py          # project items file

        pipelines.py      # project pipelines file

        settings.py       # project settings file

        spiders/          # a directory where you'll later put your spiders
            __init__.py
            ...
```

### Defining our Item
아이템은 스크랩된 데이터들을 담고 있는 컨테이너입니다. 아이템은 단순한 [Python dicts](https://wikidocs.net/16) 처럼 동작됩니다. 당신이 스크래피를 사용할때 일반적인 Python dict를 사용하면서, 아이템은 선언되지 않은 필드를 두지 않기위해 추가적인 보호막을 제공합니다(?) .(While you can use plain Python dicts with Scrapy, Items provide additional protection against populating undeclared fields, preventing typos.) 아이템을 편리하게 두기위한 도우미로써 ItemLoaders라는 메카니즘과 함께 사용되기도 합니다..

아이템은 scrapy.Item 클래스를 만들면서 선언되고, 이 클래스에 대한 속성은 scrapy.Field 객체로써 정의됩니다.  이는 ORM과 비슷합니다. (ORMs을 모른다고 걱정하시마세요 당신은 이것이 쉬운것을 알게 될것입니다.)

우리는 domz.org 에서 가져온 싸이트의 데이터를 지니는데 세용할 아이템을 모델링하면서 시작합니다. 우리는 해당 싸이트의 설명, url 그리고 이름을 캡쳐하기 원하기 때문에 이러한 세개의 속성을 각각 필드로 정의합니다. 이를 하기위해 우리는 tutorial 디렉토리에있는 items.py를 수정합니다. 우리의 아이템 클래스는 다음과 같습니다.

```python
import scrapy

class DmozItem(scrapy.Item):
    title = scrapy.Field()
    link = scrapy.Field()
    desc = scrapy.Field()
```

이는 처음에는 복잡해 보일수 있지만, 스크래피에서 아이템 클래스를 정의하는것은 당신이 다른 편리한 컴포넌트들과 도우미(components and helpers)를 사용할수 있게 합니다.


### Our first Spider
스파이더는 우리가 작성한 클래스들이고 스크래피는 도메인으로부터 스크램 정보를 사용합니다.

스파이더는 다운로드할 URL의 리스트와 어떻게 링크들을 따라갈지, 그리고 아이템들을 가져올(extract) 페이지의 내용들을 어떻게 파싱할지를 정의합니다. 

스파이더를 만들기 위해 scrapy.Spider를 서브클래스로 두어야 하다 다음과 같은 속성들을 정의해야한다

* name : 스파이더의 이름을 정의합니다. 이름은 다른 스파이더들과 같은 이름을 설정할수 없다. 유니크해야합니다.
* start_urls : 스파이더가 어디서부터 크롤링을 시작할지를 정의한 URL. 다운로드될 첫 페이지들이 여기 리스트 안에 있을것입니다. 그 다음의 URL들은 시작 URL에 담겨있는 데이타로부터 연소고적으로 생성될것입니다.
* parse() : 스파이더의 메서드입니다. 이 메서드는 각 시작 URL의 다운로드된 Response 객체를 가지고 호출될 것입니다. 이러한 response는 첫번째와 그리고 유일한 인자로써 parse()에 전달됩니다.
parse()는 받아온 response 데이터를 파싱하고 스크랩된 데이타터와 다음에 올 URL들과 스크램된 데이터를 가져올 (추출할, extract) 책임이 있습니다.
또한 parse() 메서드는 그 resonse를 처리하고, Request 객체로서 다음에 올 URL과 스램된 데이터를 리턴할 책임이있습니다.

다음은 우리의 첫번째 스파이더 코드 입니다. 이를 dmoz_spider.py의 이름으로 tutorial/spiders 디렉토리에 저장해주세요.

```python
import scrapy

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        filename = response.url.split("/")[-2] + '.html'
        with open(filename, 'wb') as f:
            f.write(response.body)
```

### Crwling
우리의 스파이더가 동작하기위해 프로젝트의 가장 상위 디렉토리에서 다음을 실행시켜주세요

> scrapy crawl dmoz

이 명령은 우리가 방금 추가한 dmoz란 이름의 스파이더를 실행시킵니다. 이는 dmoz.org 도메인을 향해 몇개의 requests를 보낼것입니다. 당시은 다음과 비슷한 아웃풋을 보게 될 것입니다.

```
2014-01-23 18:13:07-0400 [scrapy] INFO: Scrapy started (bot: tutorial)
2014-01-23 18:13:07-0400 [scrapy] INFO: Optional features available: ...
2014-01-23 18:13:07-0400 [scrapy] INFO: Overridden settings: {}
2014-01-23 18:13:07-0400 [scrapy] INFO: Enabled extensions: ...
2014-01-23 18:13:07-0400 [scrapy] INFO: Enabled downloader middlewares: ...
2014-01-23 18:13:07-0400 [scrapy] INFO: Enabled spider middlewares: ...
2014-01-23 18:13:07-0400 [scrapy] INFO: Enabled item pipelines: ...
2014-01-23 18:13:07-0400 [scrapy] INFO: Spider opened
2014-01-23 18:13:08-0400 [scrapy] DEBUG: Crawled (200) <GET http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/> (referer: None)
2014-01-23 18:13:09-0400 [scrapy] DEBUG: Crawled (200) <GET http://www.dmoz.org/Computers/Programming/Languages/Python/Books/> (referer: None)
2014-01-23 18:13:09-0400 [scrapy] INFO: Closing spider (finished)
```

###### 주목하세요
> 로그의 마지막에 당신은 start_urls에 정의된 각 URL을 볼수 있습니다. 이러한 URLs은 시작하는것이기 때문에 이러한 URLs은 참조하는것이 없습니다. 이것이 로그의 마지막에 (referer:None) 이라고 보여지게 됩니다.

이제, 현재 디렉토리엥 있는 파일을 확인하세요. 당신은 아마 두개의 새로운 파일이 생긴것을 알아차렸을 것입니다. 그 파일은 Book.html, Resources.html  인데 이는 우리의 parse() 메서드의 지침으로써 각각 URLs에 대한 내용입니다.

### What just happend under the hood?
###### 스크래피 엔진에 무신일이 생긴걸까?
스크래피는 스파이더의 start_urls 속성안에 있는 각 URL의 scrapy.Request 객체를 생성하고 (scrapy.Request 객체들의) 콜백함수로써 그 스파이더의 parse() 메서드를 각 요청들에게 할당합니다.

이러한 요청들은 parse()메서드를 통해서 스케줄링 되어서 실행되고, scrapy.http.Response 객체가 반환되며 그리고 나서 스파이더에게 다시 돌아갑니다.

### Extracting Items
#### Introduction to Selector
웹페이지로부터 데이타를 가져오는 몇몇의 방법이 있습니다. 스크래피는 *[Scrapy Selector](http://doc.scrapy.org/en/1.0/topics/selectors.html#topics-selectors)* 라고 불리는 *[CSS](https://www.w3.org/TR/selectors/)* 표현 또는 *[XPath](https://www.w3.org/TR/xpath/)* 를 기반으로한 방법을 사용합니다. 다른 추출 방법이나 셀렉터에 관한 더 많은 정보는 *[Selectors documentation](http://doc.scrapy.org/en/1.0/topics/selectors.html#topics-selectors)*를 보세요.

여기 XPath 표현과 그것에 대한 의미들에 대한 예제가 있습니다.
* /html/head/title : HTML 문서의 head 안에 있는 title 요소를 선택합니다.
* /html/head/title/text() : 앞에서 말한 title 요소 안에 있는 text를 선택합니다.
* //td : 모든 td 요소를 선택합니다.
* //div[@class="mine"] : class="mine"에 담겨있는 모든 div 요소를 선택합니다.

이는 XPath로 할수 있는 단순한 몇개의 예제일 뿐이지만 XPath로 할수 있는것들은 정말 강력합니다. XPath에대해 더 배우기 위해 우리는 *[예제로부터 배우는 XPath](http://zvon.org/comp/r/tut-XPath_1.html)* 와 *[XPath에서 생각하는 법 강좌](http://plasmasturm.org/log/xpath101/)* 를 추천합니다.

###### 주목하세요!
> **CSS cs XPath** : 당신은 단지 CSS 셀렉터를 사용하면 웹페이지에서 데이터를 가져오는데 코드가 길어질 수 있습니다. 반면 XPath는 좀 더 강력한 방법을 제공합니다. XPath는 특정 문구를 가리키는데 있어 ['Next Page' 라는 문구를 가지고 있는 링크] 와 같이 네비게이팅하는 방법을 사용할수 있기 때문입니다. 이와 같은이유로 우리는 당신이 CSS selector를 잘 알더라도 XPath를 배워 사용하길 권장합니다.

CSS와 XPath를 가지고 일하기 위해, 스크래피는 Selector 클래스와 당신이 response로부터 온 어떤것을 선택할 필요가 있을때마다 셀렉터 스스로 예시를 드는것을 방지하기위해 편리한 shortcut을 제공합니다.

당신은 이 문서구조에서의 노드들을 상징하는 객체들의 셀렉터를 볼수 있습니다. 첫번재로 예로든 셀렉터들은 root node 또는 전체 문서와 관련이 있습니다.

셀렉터는 4개의 기본 메소드를 가지고 있습니다.
* xpath() : 셀렉터 리스트를 반환합니다. 각각은 인자로 주어진 xpath 펴현에 의해 선택된 노드를 상징합니다.
* css() : 셀렉터 리스트를 반환합니다.  각각은 인자로 주어진 css 펴현에 의해 선택된 노드를 상징합니다.
* extract() : 선택된 데이타를 가진 유니코드 string을 반환합니다.
* re() :  인지로써 주어진 정규식 표현을 적용하면서 추출된 유니코드 스트링의 리스트를 반환합니다.

### Trying Selectors in the Shell
*[원문](http://doc.scrapy.org/en/1.0/intro/tutorial.html)*을 확인해주세요.

### Extracting the data
이제 이러한 페이지로부터 특정 실제 정보를 추출해 봅시다

당신은 response.body를 콘솔에 타이핑할것이고 당신이 필요한곳에 사용할 XPath를 이해하기 위해 소스코드들을 검사할 것입니다. 그러나 raw HTML을 검사하는것은 굉징히 지루한 일입니다. 이를 더 쉽게 하기위해 당신은 브라우저의 개발자 도구를 사용할수 있습니다. 더 많은 정보는 *[Using Firebug for scraping](http://doc.scrapy.org/en/1.0/topics/firebug.html#topics-firebug)* *[Using Firefox for scraping](http://doc.scrapy.org/en/1.0/topics/firefox.html#topics-firefox)* 을 보세요.

페이지 소스를 검사한 후에 당신은 웹사이트의 정보가 ul 정보 안에 위치하고 있는것을 알수 있습니다.

그리고 우리는 해당 사이트의 리스트를 포함하고 있는 각 li 요소를 볼수 있습니다.
> response.xpath('//ul/li')
그 이후에 그 싸이트의 설명은
> response.xpath('//ul/li/text()').extract()
싸이트의 타이틀은
> response.xpath('//ul/li/a/text()').extract()
그리고 사이트의 링크는 
> response.xpath('//ul/li/a/@href').extract()

우리가 이미 전에 말했듯이, .xpath() 는 셀렉터의 리스트를 반환합니다. 그래서 우리는 다음 노드로 더 깊이 파고들기위해 그 다음의 .xpath() 호출에 집중할수 있습니다. 우리는 여기에 그러한 속성을 사용할 것입니다.

```python
for sel in response.xpath('//ul/li'):
    title = sel.xpath('a/text()').extract()
    link = sel.xpath('a/@href').extract()
    desc = sel.xpath('text()').extract()
    print title, link, desc
```

우리의 스파이더에 다음과 같은 코드를 추가해주세요

```python
import scrapy

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        for sel in response.xpath('//ul/li'):
            title = sel.xpath('a/text()').extract()
            link = sel.xpath('a/@href').extract()
            desc = sel.xpath('text()').extract()
            print title, link, desc
```

이제 dmoz.org 를 다시 크롤링을 시도해볼수 있고 당신은 로그에서 프린트된 사이트 정보를 볼수 있습니다.

### Using our item
item 객체는 일반 파이썬 딕테이션 입니다. 당신은 표준 python dict 을 사용하여 해당 필드의 값에 접근할수 있습니다.

```
>>> item = DmozItem()
>>> item['title'] = 'Example title'
>>> item['title']
'Example title'
```

그리고 지금까지 우리가 스크랩한 데이터를 반환하기위해 우리의 스파이더의 마지막 코드는 다음과 같습니다

```python
import scrapy

from tutorial.items import DmozItem

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        for sel in response.xpath('//ul/li'):
            item = DmozItem()
            item['title'] = sel.xpath('a/text()').extract()
            item['link'] = sel.xpath('a/@href').extract()
            item['desc'] = sel.xpath('text()').extract()
            yield item
```

이제 dmoz.org 를 크롤링은 DmozItem 객체에 양도합니다.


### Following links
만약 당신이 Book, Resorces 페이지만 스크랩하는것 대신에  Python directory 안에 모든 페이지를 파이썬 디렉토리 내에 스크랩하고싶다고 가정해 봅시다.

이제 당신은 페이지로부터 데이터를 추출하는 법을 알고 있습니다. 그럼 당신이 흥미있어하는 페이지의 링크를 추출하고, 이러한 링크를 따라가서 당신이 원하는 모든 데이터를 추출하는 건 어떤가요?

아래와 같이 우리의 스파이더를 조금 수정해 봅시다

```python
import scrapy

from tutorial.items import DmozItem

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/",
    ]

    def parse(self, response):
        for href in response.css("ul.directory.dir-col > li > a::attr('href')"):
            url = response.urljoin(href.extract())
            yield scrapy.Request(url, callback=self.parse_dir_contents)

    def parse_dir_contents(self, response):
        for sel in response.xpath('//ul/li'):
            item = DmozItem()
            item['title'] = sel.xpath('a/text()').extract()
            item['link'] = sel.xpath('a/@href').extract()
            item['desc'] = sel.xpath('text()').extract()
            yield item

```
이제 parse() 메소다다드는 특정 페이지로부터 흥미로운 링크만 추출하는것만이 아니라 response.urljoin 메소드를 사용한 전체 URL 경로를 만들어 궁극적으로 우리가 원하는 데이타를 스크랩할 parse_dir_content() 메서드를 콜백으로 등록하면서 추후에 보내질 새로운 요청들에게 양도합니다.

여기서 당신은 Scrapy가 links를 따라가는 방법을 볼 수 있습니다. 당신이 하나의 요청(Request)를 콜백함수에 양도할 때, Scrapy는 보내진 요청들을 스케쥴링하고 그 요청이 마칠때 실행될 콜백 메소드를 등록합니다.

이것을 사용하면 당신이 정의한 규직에 따라 다음 링크를 따르게 하고, 크롤러가 방문한 그 페이지에 따라 다양한 종류의 데이터를 추출할수 있는 복잡한 크롤러를 만들 수 있습니다.

공통의 패턴은 특정 아이템을 찾아서 다음에 팔로우할 링크를 찾고 그 링크를 향해가는 똑같은 콜백 메서드를 가지고 요청(Request)를 넘겨주는 것입니다.

```python
def parse_articles_follow_next_page(self, response):
    for article in response.xpath("//article"):
        item = ArticleItem()

        ... extract article data here

        yield item

    next_page = response.css("ul.navigation > li.next-page > a::attr('href')")
    if next_page:
        url = response.urljoin(next_page[0].extract())
        yield scrapy.Request(url, self.parse_articles_follow_next_page)
```

위에 코드는 다음 페이지를 찾을수 없을 때까지 모든 링크를 찾아 크롤링을 반복하는 코드입니다. 이는 블로그나 포럼, 그리고 페이지를 가진 다른 사이트들을 크롤링하는데 유용합니다.

다른 공통의 패턴은 *[콜백에게 추가적인 데이터를 보내는 트릭](http://doc.scrapy.org/en/1.0/topics/request-response.html#topics-request-response-ref-request-callback-arguments)* 을 사용하여 한 페이지 이상으로부터 데이타를 가진 아이템을 만드는것이다.

### Storing the scrapted data
스크랩 데이터를 저장하는 가장 단순한 방법은 *[Feed exports](http://doc.scrapy.org/en/1.0/topics/feed-exports.html#topics-feed-exports)*를 이용하는것 입니다. 다음과 같은 명령어를 이용합니다.

> scrapy crawl dmmoz -0 items.json

이것은 모든 스크랩된 아이템을 가지고 있는 items.json 파일을 생성할것 입니다.
이는 작은 프로젝트에서는 충분하지만 만약 당신이 스크랩된 아이템을 가지고 좀더 복잡한 것을 수행하려면 당신은 *[Item Pipeline](http://doc.scrapy.org/en/1.0/topics/item-pipeline.html#topics-item-pipeline)* 을 작성해야합니다. 우리의 프로젝트에는 프로젝트가 생성될때 이미 tutorial/pipelines.py가 만들어져 있습니다.
