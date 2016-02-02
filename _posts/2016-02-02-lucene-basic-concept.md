---
layout: post
title:  "Lucene 기본개념 (번역)"
date:   2016-02-02
author: Sooyoung
categories: dev
tags: dev lucene 
cover:  "/assets/img/lucene.png"
---

## Lucene

다음은 [Lucene Tutorial.com](http://www.lucenetutorial.com/basic-concepts.html)에서 설명한 루신 기본 개념 부분을 번역한 것입니다. 미흡한 부분이 있는점 미리 양해 부탁드립니다.

### Basic Concept 기본 개념

우리의 웹사이트나 어플리케이션에 검색 기능을 쉽게 붙일 수 있는 루신은 자바로 만들어진 full-text 검색 라이브러리입니다.

루신은 content(내용)을 full-text index에 더함으로서 동작 됩니다. 루신은  당신이 그 인덱스에 쿼리를 수행할수 있게 해주고, 그 쿼리에 연관된 것들에 순위을 매겨 결과값을 리턴하거나, 문서의 마지막으루 수정된 날짜 순과 같은 특정 분야를 기준으로 정렬 해서 결과값을 리턴하기도 합니다.

당신이 루신에 추가하는 컨텐츠는 SQL,NoSQL DB, 파일시스템, 또는 웹사이트와 같이 다양한 곳으로 부터 올 수 있습니다.


### Searching and Indexing (검색과 생인)
루신은 빠른 검색 응답(fast search response)을 성취할 수 있습니다. 왜냐하면 텍스트를 직접적으로 검색하는 대신에 루신은 인덱스로 검색하기 때문입니다. 이것은 책의 맨 마지막에 색인을 이용해 관련된 키워드를 검색해 페이지를 찾는 것과 같다고 볼 수 있습니다.

인덱스의 타입을 **inverted index(도치된 색인) **라고 부릅니다. 왜냐하면 이것은 페이지 중심적인 데이타 구조 (page->word)를 키워드 중심적인 데이타 구조(word->pages)로 도치시키기 때문입니다.

### Document (문서)
루씬에서는 하나의 Document는 Searching과 Index의 단위입니다. (In Lucene, a Document is the unit of search and index.) 하나의 Index 는 하나 또는 그 이상의 Document로 구성되어 있습니다.

**Indexing(색인작업)은 여러 Documents 를 indexWriter에 추가하는 작업과 IndexSearcher를 통해서 Index로부터 Documents 를 가져오는 일을 포합합니다.**

하나의 루씬 Document는 꼭 영어로된 (English usage of the word) document일 필요가 없습니다. 예를 들어 만약 사용자들이 있는 데이터베이스 테이블에서 루신 Index를 만들고 싶다면, 각 사용자들은 루씬의 Document 로써 그 인덱스에 표기되어 있습니다.

### Fields (분야)
하나의 Document는 하나의 또는 그 이상의 Fields로 구성되어 있습니다. 하나의 Field는 단순한 namd-value (이름과 값)으로 된 쌍입니다. 예를들어 흔히 어플리케이션에서 사용되는 하나의 Field는 *title* 입니다. *title* Field의 경우, field 이름은 *title*이고, 값은 그 내용의 제목이 됩니다. (*title*이란 이름으로 된 값)

루씬에서 Indexing은 Fields로 구성되어있는 Documents를 만드는 것과, 이러한 Documents를 IndexWriter에 추가하는 것을 포함합니다

### Searching (검색)
Searching(검색작업)에는 이미 만들어진 index가 필요합니다. Searching(검색작업)은 쿼리를 만들고 (일반적으로 QueryParser를 통해서) 이 쿼리를 Hits의 리스트를 반환하는 **IndexSearcher**에게 전해주는 일을 포합합니다.

### Queries (쿼리)
루씬은 검색을 수행하는데 있어 루씬 고유의 Mini-languge를 가지고 있습니다. 더 자세한 정보를 위해서 [Lucene Query Syntax](http://www.lucenetutorial.com/lucene-query-syntax.html) 를 읽어 주세요.

Lucene Query Language 는 사용자가 검색을 위해 어떤 Field(s)를 사용할지, 어떤 Field(s)에 더 중접을 둘지, Boolean qeury(AND, OR,NOT)의 수행할수 있는 능력, 그 밖에 다른 기능들을 사용할수 있게 해줍니다.

## 5분만에 루씬 경험하기

~~~java
public class FiveMinLuceneTest {
    public static void main(String[] args) throws IOException, ParseException {
        /*
         1. Index : 간단한 예제를 위해 우리는 몇개의 String으로부터  In-memory 인덱스를 생성합니다.
         */
        StandardAnalyzer analyzer = new StandardAnalyzer();
        Directory index = new RAMDirectory();
        IndexWriterConfig config = new IndexWriterConfig(analyzer);

        IndexWriter writer = new IndexWriter(index, config);
        addDoc(writer, "Lucene in Action", "193398817");
        addDoc(writer, "Lucene for Dummies", "55320055Z");
        addDoc(writer, "Managing Gigabytes", "55063554A");
        addDoc(writer, "The Art of Computer Science", "9900333X");
        writer.close();

        /*
            2. Query : stdin으로 부터 쿼리를 읽고 parse 후 이것의 밖으로 루씬 쿼리를 build 합니다/
         */
        String queryStr = args.length > 0 ? args[0] :  "lucene";

        //"title" 값은 만약 쿼리에 field가 명시되지 않았다면 초기값으로 사용됩니다.
        Query query = new QueryParser("title", analyzer).parse(queryStr);

        /*
            3. Search : 쿼리를 사용하여 우리는 index를 검색하기 위한 Searcher를 만듭니다
            그리고 TopScoreDocCollector는 hits가 많은 순으로 탑10 을 수집하기 위해 초기화 합니다.
         */
        int hitsPerpage = 10;
        IndexReader reader = DirectoryReader.open(index);
        IndexSearcher searcher = new IndexSearcher(reader);
        TopDocs docs =searcher.search(query, hitsPerpage);
        ScoreDoc[] hits = docs.scoreDocs;


        // 4.Display : 이제 우리는 우리가 만든 searcher 로부터 결과를 얻었고 그 결과를 사용자에게 보여줍니다.
        System.out.println("Found : "+ hits.length+ " hits.");
        for (int i = 0; i < hits.length ; ++i) {
            int docId = hits[i].doc;
            Document document = searcher.doc(docId);
            System.out.println((i+1) + ".  "+ document.get("isbn") + "\t"+ document.get("title"));
        }
        // reader는 더이상 Documents에 접근할 필요가 없을때 닫혀야만 합니다.
        reader.close();

    }

    /*
        addDoc()은 실제로 Documents를 index에 무엇을 추가할것지를 수행하는 함수입니다.
        우리가 tokenized 하기 원하는 내용의 TextField의 사용과, tokenized 를 원하지 않는 id Field에 StringField의 사용에
        주목해주세요.
     */
    private static void addDoc(IndexWriter writer, String title, String isbn) throws IOException {
        Document document = new Document();

        document.add(new TextField("title", title, Field.Store.YES));
        document.add(new StringField("isbn", isbn, Field.Store.YES));
        writer.addDocument(document);

    }

}
~~~


