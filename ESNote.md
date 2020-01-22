`Elastic Search 학습 내용 정리`   
   
[참조 : ElasticSearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/analysis.html)    
  
## Text Analysis  
  
검색할 때 사용되는 역색인(inverted index)에는 token 또는 term들이 담겨져 있음.  
텍스트 분석은 텍스트(texT)를 token 또는 term으로 변환하는 과정임.   
분석은 분석기로 수행되는데, 분석기는 내장도니 분석기를 쓸 수 도 있고, 색인마다 정의되는 커스텀 분석기를 사용할 수도 있음.  
  
    
### Index time analysis   
   
예를 들어, 다음의 문장을 보면,
   
```"The Quick brown foxes jumped over the lazy dog!"```  
    
색인시 내장된 english 분석기는 처음 문장을 별개의 토큰으로 변환한다.   
그리고 각 토큰을 소문자로 변환하고, 빈번히 쓰이는 불용어를 제거한다. (the와 같은)  
그리고 term을 최소화된 단어로 만든다 (foxes -> fox, jumped -> jump, lazy -> lazi)   
최종적으로 다음의 term들이 역색인에 더해질 것이다.   
   
```[ quick, brown, fox, jump, over, lazi, dog]```   
   
   
### Specifying an index time analyzer   
   
엘라스틱서치는 다음 파라미터를 순서대로 체크하면서 어떤 분석기를 사용할지 색인할 때 결정한다.   
   
1. The analyzer mapping parameter of the field   
2. The default analyzer parameter in the index settings   
   
만약 어떤 파라미터도 명시되지 않으면, *standard* 분석기가 사용된다.   
   
    
### Specify the index-time analyzer for a field    
   
매핑에서 각 텍스트 필드 각각의 분석기를 명시할 수 있다.   
   
```  
PUT my_index
{
  "mapping" : {
      "properties" : {
          "title" : {
              "type" : "text",
              "analyzer" : "standrad"  
          }
      }
   }
}
```    
    
   
### Specify a default index-time analyzer     
   
색인을 생성할 때, `default` 분석 세팅을 사용하여 디폴트 분석기를 설정할수 있다.    
    
```   
PUT my_index
{
  "setting" : {
      "analysis" : {
          "analyzer" : {
            "default" : {
                "type" :" "whitespace"  
            }
          }
      }
  }
}
```   
   
디폴트 분석기를 설정해놓으면 같은 분석기를 사용하는 여러개의 텍스트필드를 매핑할 떄 유용하다.   
또한, 인덱스 시간과 검색 시간 분석을 위한 일반적인 폴백 분석기로도 사용된다.  
    
       
- - -   
   
## Text analysis overview   
   
텍스트 분석은 엘라스틱서치가 전문검색이 가능하도록 한다.   
검색은 정확한 매칭보다는 관련있는 모든 결과들을 반환해준다.  
  
만약 `Quick fox jumps`를 검색하면, `A quick brown fox jumps over the lazy dog`을 포함한 문서를 결과로 원할수도 있고,  
또한 `fast fox`나 `foxes leap`와 같은 연관된 단어를 가지고 있는 문서를 결과로 원할수도 있다.   
   
  
### Tokenization    
  
분석은 토큰화를 통해 전문건색이 가능하게끔 한다 .  
텍스트를 작은 덩어리로 쪼개는데 이 작은 덩어리를 토큰이라고 한다.  
대부분 이러한 토큰들은 개별적인 단어들이다.  
  
만약 구문 `the quick brown fox jumps`를 단일 문자열로서 색인하고 `quick fox`로 검색하면 아무 결과도 얻지 못할 것이다.  
하지만 만약에 구문을 토큰나이징하고, 각 단어를 개별적으로 색인한다면, 쿼리스트링 안에 있는 term들은 개별적으로 검색이 될 것이다.  
즉, `quick fox`, `fox brown` 또는 다른 단어들은 검색결과로 매칭될 수 있다.  
     
       
### Normalization    
  
토큰화는 개별적인 term 매칭들을 가능하게 하지만, 각 토큰들은 여전히 문자그대로 매칭된다.   
이것이 의미하는 것은 다음과 같다.  
  
* `Quick` 검색은 `quick`과 매칭되지 않는다.  
* 우리는 `fox`와 `foxes`를 같은 단어로 보지만, foxes를 검색하면 fox가 검색되지 않으며 반대도 마찬가지이다.  
* jumps를 검색하면 leaps와 매치되지 않는다.  
  이 두단어들은 어근(root word)을 공유하지는 않지만 동의어이며 비슷한 의미를 가진다.  
  
이러한 문제들을 해결하기 위해, 텍스트 분석은 이러한 토큰들은 표준 형식으로 정규화한다.   
검색어와 정확히 일치하지는 않지만, 관련성 높은 토큰을 일치시킬 수 있다.   
    
     
- - -  
    
    
## Configure text analysis   
    
모든 텍스트 분석시에 엘라스틱서치는 기본적으로 `standard analyzer`를  사용한다.  
표준 분석기는 대부분의 자연 언어 및 사용 사례를 지원한다.  
만약 현재 표준 분석기를 사용하기 위한다면, 별도의 환경설정은 필요없다.   
   
   
### Configuring built-in analyzers   
    
내장분석기는 별도의 환경설정없이 바로 사용가능하다.  
하지만 일부 동작을 변경하기 위한 옵션들을 지원한다.  
예를 들어서 표준 분석기(standard analysis)는 불용어 목록을 지원하도록 환경설정될 수도 있다.  
   
```  
PUT my_index
{
   "settings" : {
      "analysis" : {
         "analyzer" {
               "std_english" : {    // 1
                     "type" : "standard",
                     "stopwords" : "_english_"
               }
          }
      }
   }, 
   "mappings" : {
      "properties" : {
            "my_text" : {
               "type" : "text",
               "analyzer" : "standard",   // 2
               "fields" : {
                  "english" : {
                     "type" : "text",
                     "analyzer" : "std_english"    // 3
                  }
               }
            }
      }
   }
}

POST my_index/_analyze
{
   "field" : "my_text", // 2
   "text" : "The old brown cow"
}

POST my_index/_analyze
{
   "field" : "my_text.english", // 2
   "text" : "The old brown cow"
}
```   
  
1. `std_english` 분석기가 표준분석기를 기반으로 정의되었다.  
    하지만 사전 정의된 영어 단어 목록을 제거하도록 환경설정 ㅏㅎ였따.  
2. `my_text` 필드는 어떤 설정없이 표준 분석기를 다이렉트로 사용한다.    
    이 필드에서 어떤 불용어도 제거되지 않을 것이다.  
    결과는 `[ the, old, brown, cow ]`가 될 것이다.  
3. `my_text.english` 필드는 `std_engish` 분석기를 사용한다.  
    그래서 영어 불용어는 제거될 것이다.  
    결과는 `[ old, brown, cow ]`가 될 것이다.   
   
  
       
     

