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
   
    
### 색인시 필드에 대한 analyzer 명시  
   
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
    
   
### 색인할 때 디폴트 분석기 지정  
   
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
    
       

