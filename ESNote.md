`Elastic Search 학습 내용 정리`   
   
[참조 : ElasticSearch Reference(Mapping)](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/mapping.html#mapping-type)    

## Mapping   
    
맵핑은 엘라스틱 서치에서 사용될 필드의 타입(자료형)을 사전에 정의해주는것으로 보면 된다.  
관계형 데이터베이스의 스키마와 유사한 의미이고, 사전에 매핑에 대한 정의가 없다면 색인되는 데이터에 따라 필드 데이터 타입에 대한 매핑정보가 자동으로 설정된다.  
한번 매핑 타입이 정의되면 이후엔 수정이 될 수 없기떄문에 사전에 적절한 매핑 타입을 정의하는 것이 좋다.  
   
매핑을 사용하면 다음을 정의할 수 있다.  
    
* 전문 필드(full text field)로 취급되어야 하는 문자열 필드들  
* 숫자, 날짜, 지리적 위치를 포함하는 필드들  
* 날짜 값 포맷  
* 동적으로 추가된 필드 매핑을 컨트롤 하는 커스텀 규칙들  
   
   
### Mapping Type   
   
각 색인은 하나의 매핑 타입을 가진다.  
  
한매핑 타입은 **Meta-fields**와 **Fields** 혹은 properties를 가진다.    
  
* Meta-fields  
  메타 필드는 문서의 메타데이터가 처리되는 방식을 정의하는데 사용된다.  
  메타필드의 예로는 `_index`, `_type`, `_id` 및 `_source` 필드가 있다.  
* Fields or properties  
  한 매핑 타입은 필드 혹은 문서에 관련된 적절한 프로퍼티(properties)의 목록을 포함한다.   
       
        
### Field datatypes  
  
각 필드에는 다음과 같은 데이터 타입이 있다.  
   
* test, keyword, date, long, double, boolean 또는 ip와 같은 간단한 타입  
* 객체 또는 중첩과 같은 JSON의 계층적 특성을 지원하는 타입  
* geo_point, geo_shape 또는 completion과 같은 전문화된 타입   
      
같은 필드를 다른 목적으로, 다른 방식으로 색인하는것은 종종 유용하다.  
예를 들어 문자열 필드는 전문 검색을 위한 텍스트 필드와 정렬 또는 집계하기 위한 키워드 필드로 색인될 수 있다.  
또는 문자열 필드를 표준 분석기, 영어 분석기, 프랑스 분석기로 색인할수도 있다.  
이것이 바로 다중 필드(Multi-field)의 목적이다.      
대부분 데이터 타입은 fields 매개변수를 통해 다중 필드를 지원한다.  
     
       
### Dynamic mapping    
  
필드와 매핑 타입은 사전에 정의될 필요는 없다.  
dynamic mapping 덕분에 새로운 필드이름들은 도큐먼트를 색인하면서 자동적으로 덧붙여질 수 있다.  
최상위(top-level) 필드 매핑 타입과 내부 개체 및 중첩 필드에 새 필드를 추가하는 것은 가능하다.  
   
   
### Create an index with an explicit mapping   
   
정확한 매핑 타입을 가진 새로운 색인을 만들때 `create index` API를 사용하면 된다.  
   
```   
PUT /my-idnex
{
      "mappings" : {
           "properties" : {
               "age" : { "type" : "integer" },  // 정수 필드 age 생성  
               "email" : { "type" : "keyword" }, // 키워드 필드 email 생성  
               "name" : { "type" : "text" } // 텍스트 필드 name 생성  
           }           
      }
}
```   
  
### Add a field to an existing mapping  
    
새로운 필드를 기존 색인에 덧붙이기 위해서는 `put mapping` API를 사용하면 된다.  
다음 예는 키워드 필드 employee-id를 index 매핑 파라미터 값 false로 기존 색인에 추가하는 예제이다.  
이것은 employee-id 필드가 저장되지만 색인되거나 검색이 가능하지 않다는 것을 의미한다.   
   
```  
PUT /my-index/_mapping
{
   "properties" : {
         "employee-id" : {
               "type" : "keyword",
               "index" : false  
         }
   }
}
```  
    
#### Update the mapping of filed   
  
지원되는 매핑매개변수를 제외하고, 기존필드의 매핑 또는 필드 타입을 변경할 수 없다.  
기존 필드를 변경하면, 색인된 데이터가 무효화될 수도 있다.  
만약 필드 매핑을 변경하기 원하면, 올바른 매핑으로 새로운 색인을 생성하고 데이터를 재색인해야 한다.  
필드명을 변경하면 이전 필드명으로 이미 색인된 데이터 이용이 불가능해질 수 있다.  
대신 alias 필드를 추가해서 대체 필드명을 생성해야 한다.  
    
    
### View the mapping of an index  
   
기존 색인의 매핑 정보를 보려면 `get mapping` API를 사용하면 된다.  
    
```  
GET /my-index/mapping   
```   
   
### View the mapping of specific fields  
  
특정필드의매핑 정보를 확인하고 싶으면 `get field mapping` API를 사용하면 된다.  
이건 필드수가 매우 많을떄 사용하면 유용함  
  
다음 요청은 employee-id 필드의 매핑 정보를 알려준다.  
  
```  
GET /my-index/_mapping/field/employee-id
``` 
   
   
- - -  
   
[참조 : ElasticSearch Reference(Removal of mapping types)](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/removal-of-types.html)    
   
   
## Removal of mapping types  
   
   

엘라스틱 7.0.0 이후로 생성된 색인들은 더이상 `_defulat` 매핑을 허용하지 않는다.  
6.x 버전에 생성된 색인들은 엘라스틱 6.x에 기능하던 동작대로 동작될 것이다.  
Types은 7.0에 있는 API들에서 더이상 사용될 수 없다. (deprecated)  
이에 따라 관련된 API들에 변경된 부분이 생겼다.  
(index creation, put mapping, get mapping, put template, get template, get field mapping)   
   
   
### What are mapping types ?  
  
처음 엘라스틱 서치가 릴리즈된 이후로, 각 도큐먼트는 하나의 색인에 저장되고 하나의 매핑 타입을 할당받았다.  
각 매핑 타입은 자체 필드들을 가질 수 있다.  
그래서 user 타입은 full_name 필드, uase_name 필드 그리고 email 필드를 가질 수 있고,  
tweet 타입은 content 필드, tweeted_at 필드들을 가질 수 있다.  
  
각도큐먼트는 `_type` 메타 필드를 가지고 있으며, `_type` 메타필드는 타입 이름을 포함한다.  
그리고 검색들은 URL에서 타입명을 명시함으로써 하나 또는 하나 이상의 타입으로 제한할 수 있다.  
   
```
Get twitter/use,tweet/_search
{
   "query" : {
      "match" : {
         "use_name" : "kimchy"
      }
   }
}
```   
   
`_type` 필드는 도뮤먼트의 `_uid`필드를 생성하기 위해 `_id`와 결합된다.  
그래서 같은 `_id`를 가지는 다른 타입의 도큐먼트들은 단일 인덱스에서 존재할 수 있다.  
매핑 타입은 또한 도큐먼트들 사이에서 parent-child 관계를 만들기 위해 사용된다.  
그래서 `question` 타입의 도뮤먼트는 `answer`타입의 도큐먼트의 부모가 될 수 있다.  
  
      
### Why are mapping types being removed ?  
   
첬째로, 우리는 "index"가 SQL의 "database"와 유사하다고 말한다.  
그리고 "type"은 "table"과 유사하다고 말한다.  

이것은 잘못된 가정을 만든다. SQL 데이터베이스에서 테이블은 서로 독립적이다.  
한 테이블에서 컬럼들은 다른 테이블의 컬럼들과 관련이 없다.  
하지만 이러한 내용이 매핑 타입의 필드들에 대해서는 다르게 적용된다.  
엘라스틱 서치 인덱스에서, 다른 매핑 타입에 있는 같은 이름을 가지는 필드들은 내부적으로 같은 루신 필드로 지원된다.  
즉, 위의 예에서 `user` 타입에서 `use_name` 필드는 `tweet` 타입에서 `user_name` 필드와 동일한 필드로 저장된다.  
그리고 두 `use_name`필드는 두 타입 모두에서 동일한 매핑을 가져야만 한다.   
    
이것은 혼란을 만들 수 있다.   
This can lead to frustration when, for example, you want deleted to be a date field in one type and a boolean field in another type in the same index.    
  
On top of that, storing different entities that have few or no fields in common in the same index leads to sparse data and interferes with Lucene’s ability to compress documents efficiently.  
(이해가 잘안됨..ㅠㅠ)  
   

### Alternatives to mapping types   
    
#### Index per document type   
  
첫번째 대안은 도큐먼트 타입별로 색인을 만드는 것이다.  
단일 twitter 인덱스에 tweet들과 user들을 저장하는 대신에, tweets 색인에 tweet 데이터들을 저장하고, user 색인에 user 정보를 저장한다.  
색인들은 서로 완전히 독립적이고 색인들 사이에 필드 타입으로 인한 충돌은 없을 것이다.  
    
이 접근에는 두가지 이점이 있다.  
* 데이터가 더 밀집될 가능성이 높아져, 루신에서 사용되는 압축기술에 이점이 있을 것이다.  
* 모든 문서가 단일 엔터티를 표현하기 떄문에, 전문검색에서 검색은 더 정확해질 것이다.  
   
각 색인은 포함할 도큐먼트 수에 맞게 크기를 조정할 수 있다.   
user의 경우 기본 샤드 수를 줄이고, tweet의 경우 기본 샤드 수를 더 많이 사용할 수 있다.  
    

  
- - -  
   
[참조 : ElasticSearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/analysis.html)    
  
   
## Text Analysis  
  
검색할 때 사용되는 역색인(inverted index)에는 token 또는 term들이 담겨져 있음.  
텍스트 분석은 텍스트(text)를 token 또는 term으로 변환하는 과정임.   
분석은 분석기로 수행되는데, 분석기는 내장(built-in) 분석기를 쓸 수 도 있고, 색인마다 정의되는 커스텀 분석기를 사용할 수도 있음.  
  
    
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
   
  
       
- - -   
    
  
## Create a custom analyzer   
   
빌트인 분석기로 요구사항 충족이 안되면, 문자 필터(character filter), 토크나이저(tokenizer), 토큰 필터(token filteR)를 조합하여 커스텀 분석기를 만들 수 있다.  
    
     
### Configuration       
 
커스텀 분석기는 다음 파라미터를 받아들인다.   
  
**tokenizer** : 빌트인 혹은 커스텀 토크나이저 (필수값)   
**char_filter** : 빌트인 혹은 커스텀 문자필터 (선택값)   
**filter** : 빌트인 혹은 커스텀 토큰 필터 (선택값)   
**position_increment_gap** : 텍스트 배열을 인덱싱할떄, 엘라스틱서치는 한 값의 마지막 term과 다음 값의 첫번째 term 사이에 가짜 "갭"을 삽입한다.  
                             디폴트값은 100   
      
      
### Exmaple Configuration   
   
[Custom Conifguration 예제](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/analysis-custom-analyzer.html)  
    
    
- - -   
   
   
## Built-in analyzer reference   
      
   
* Standard Analyzer  
  유니코드 텍스트 분할 알고리즘 (Unicode Text Segmentation)으로 정의된대로 텍스트를 term으로 분리.  
  대부분 구두점, 소문자 term, 불용어 제거 지원  
     
* Simple Analyzer  
  문자가 아닌 글자가 나오면 텍스트를 term으로 분리, 모든 term 소문자로 변환   
    
* Whitespace Analyzer   
  공백문자를 만날떄마다 텍스트를 term으로 분리, term들을 소문자로 변환하지 않음   
   
* Stop Analyzer   
  심플분석기와 비슷하지만 불용어 제거 지원    
     
* Keyword Analyzer   
  keyword analyzer is a “noop” analyzer that accepts whatever text it is given and outputs the exact same text as a single term.   
     
* Pattern Analyzer   
  패턴 분석기는 텍스트를 term으로 나눌떄 정규표현식 사용, 소문자 변환과 불용어 제거 지원.  
  
* Language Analyzers  
  엘라스틱서치는 영어와 프랑스어 같은 많은 언어분석기 지원   
     
* Fingerprint Analyzer  
  
  
- - -   
   
   
## Mapping   
   
NEST를 이용하여 엘라스틱서치와 상호작용하기 위해서, POCO 타입과 엘라스틱 역색인에 저장된 JSON 도큐먼트와 필드들간 매핑을 컨트롤 할 수 있어야 한다.   
엘라스틱서치에서 명시적으로 도큐먼트들을 매핑하는것은 매우 중요한 부분이다. 
엘라스틱 서치가 첫번째 주어진 도큐먼트에 근거하여 색인에서 주어진 타입을 유추하여 매핑을 할 수도 있지만, 추론된 매핑은 때때로 검색할 때 완벽한 결과를 제공하지 못할 수도 있다.  
명시적으로 매핑을 제어하기 위해, 색인을 만들 때 명시적 타입 매핑을 명시하거나, 첫번째 문서가 색인되기 전에 기존 색인에 더할 수 있다.  
(명시적 매핑 없이 문서를 색인하게 되면, 엘라스틱 서치는 매핑을 유추하게 된다!)  
  
NEST에는 매핑 컨트롤 하는 다음의 몇가지 방법이 있다.  
  
* Auto mapping (inferred from POCO property types)  
* Attribute mapping  
* Fluent mapping  
* through the Visitor Pattern  
* Parent/Child Relationship  
        
   
### Auto mapping   
   
색인을 생성하거나 PUT Mapping API를 사용하여 매핑을 만들 때, NEST는 매핑하는 CLR POCO 속성 유형에서 올바른 엘라스틱서치 필드 데이터 유형을 자동으로 유추 할 수있는 자동 매핑 기능을 제공한다.    
    
예를 들어, 다음과 같이 직원들 정보를 담고 있는 `Company`와 `Employee` 두 개의 POCO가 있다고 보자.   
   
```  
public abstract class Document
{
   public JoinField Join { get; set; }
}

public class Company : Document
{
   public string Name { get; set; }
   public List<Employee> Employees { get; set; }
}

public class Employee : Document
{
    public string LastName { get; set; }
    public int Salary { get; set; }
    public DateTime Birthday { get; set; }
    public bool IsManager { get; set; }
    public List<Employee> Employees { get; set; }
    public TimeSpan Hours { get; set; }   
}
```  
  
자동 매핑(Auto mapping)은 POCO에 대한 모든 속성에 수동매핑을 정의하지 않도록 해준다.  
이 케이스에서 우리는 한 인덱스에 2개의 서브 클래서들을 색인하려고 한다.  
기본 클래스에 대해 Map을 호출한 다음, 구현하려는 각 유형애 대하여 AutoMap을 호출하면 된다.  
    
```  
var createIndexResponse = _client.Indices.Create("myindex", c => c
         .Map<Document>(m => m
               .AutoMap<Company> () //1
               .AutoMap(typeof(Employee)) //2
       )
);
```  
  
1. generic 메소드를 이용하여 Company를 자동매핑한다.  
2. non-generic 메소드를 이용하여 Employee를 자동매핑한다.  
  
이 작업은 다음의 JSON request를 만들어낼 것이다.  
   
``` 
{
  "mappings": {
    "properties": {
      "birthday": {
        "type": "date"
      },
      "employees": {
        "properties": {
          "birthday": {
            "type": "date"
          },
          "employees": {
            "properties": {},
            "type": "object"
          },
          "hours": {
            "type": "long"
          },
          "isManager": {
            "type": "boolean"
          },
          "join": {
            "properties": {},
            "type": "object"
          },
          "lastName": {
            "fields": {
              "keyword": {
                "ignore_above": 256,
                "type": "keyword"
              }
            },
            "type": "text"
          },
          "salary": {
            "type": "integer"
          }
        },
        "type": "object"
      },
      "hours": {
        "type": "long"
      },
      "isManager": {
        "type": "boolean"
      },
      "join": {
        "properties": {},
        "type": "object"
      },
      "lastName": {
        "fields": {
          "keyword": {
            "ignore_above": 256,
            "type": "keyword"
          }
        },
        "type": "text"
      },
      "name": {
        "fields": {
          "keyword": {
            "ignore_above": 256,
            "type": "keyword"
          }
        },
        "type": "text"
      },
      "salary": {
        "type": "integer"
      }
    }
  }
}
```  
  
NEST가 우리가 만든 POCO 프로퍼티의 CLR 타입에 근거해서, 엘라스틱 서치 타입을 추론한다는 것을 주목해야 한다.  
예를 들어,  
* Birthday는 date타입으로 매핑된다.
* Hours는 long 타입으로 매핑된다.  
* IsManager는 boolean으로 매핑된다.  
* Salary는 integer로 매핑된다.  
* Employees는 object로 매핑된다.  
 그리고 나머지 문자열 속성은 각각 키워드 데이터 유형 하위필드가 있는 다중 필드 텍스트 타입이 된다.  
     
[Inferred .NET type mapping](https://www.elastic.co/guide/en/elasticsearch/client/net-api/current/auto-map.html)   
    
   
- - -   
   
[NEST - High level client](https://www.elastic.co/guide/en/elasticsearch/client/net-api/current/writing-queries.html)   
   
     
### Search - Combining queries  
    
```  
var searchResult = _client.Search<Project>(s => s
      .Query(q => q
            .Bool(b => b
                  .Must(mu => mu
                       .Match(m => m   // 1
                           .Field(f => f.Student.FirstName)
                           .Query("Hailey")  
                      ), mu -> mu
                      .Match(m => m    // 2
                           .Field(f => f.Student.LastName)
                           .Query("Jung")
                      )
                 )
                 .Filter(fi => fi
                     .DateRange(r => r
                           .Field(f => f.StartedOn)
                           .GreaterThanEquals(new DateTime(2019, 01, 01))
                           .LessThan(new DateTime(2020, 01, 01))     // 3
                        )
                   )
               )
         )
);                                       
```   
   
1. 학생(Student)의 이름(First Name)에 "Hailey"가 포함되어 있는 도큐먼트를 찾는다.    
2. 그리고(and) 학생의 성에 "Jung"이 포함된 도큐먼트를 찾는다.  
3. 기간 검색 조건   
   
검색 쿼리 결과로 나온 도큐먼트는 3쿼리를 모두 충족해야한다.    
위코드는 다음의JSON 쿼리를 생성한다.   
       
```  
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "leadDeveloper.firstName": {
              "query": "Russ"
            }
          }
        },
        {
          "match": {
            "leadDeveloper.lastName": {
              "query": "Cam"
            }
          }
        }
      ],
      "filter": [
        {
          "range": {
            "startedOn": {
              "lt": "2020-01-01T00:00:00",
              "gte": "2019-01-01T00:00:00"
            }
          }
        }
      ]
    }
  }
}
```   
   
bool 쿼리는 매우 일반적이기 때문에, NEST는 연산자를 쿼리에 오버로드 해서 bool 쿼리를 훨씬 간결하게 만든다.  
다음과 같이 쿼리를 훨씬 간결하게 만들 수 있다.  
    
```   
searchResposne = _client.Search<Projects>(s => s
      .Query(q => q
         .Match(m => m
               .Field(f => f.Student.FirstName)
               .Query("Hailey")
         ) && q      // 1
         .Match(m => m
               .Field(f => f.Student.LastName)
               .Query("Jung")
         ) && +q  // 2
         .DateRange(r => r
               .Field(f => f.StartedOn)
               .GreaterThanOrEquals(new DateTime(2020, 01, 01))
               .LessThan(new DateTime(2019, 01, 01))
         )
      )
);
```  
   
1. && 연산자 사용하여 쿼리들을 결합   
2. + 연산자 사용해서 bool 쿼리 필터절에 쿼리를 래핑하고, && 연산자 사용하여 결합   
       
      
### Search Response    
    
검색 쿼리로부터  `SearchResposne<T>`을 반환받는다.  
`T`는 검색 메소드 호출에서 정의된 제너릭 파라미터 타입이다.   
응답에는 별로 많은 속성(properties)이 있지는 않지만, 주로 많이 사용하는 속성은 `.Document`이다.  
    
    
### Matching Document   
   
검색쿼리에서 받은 응답값에서 도큐먼트는 다음과 같이 얻을 수 있다.   
   
```   
var searchResponse = client.Search<Project>(s => s
      .Query(q => q
            .MatchAll()
      )
);

var projects = searchResponse.Documents;
```   
     
`.Document` 는 각 히트마다 `_source`를 얻기 위한 편리한 속기이다.   
   
```  
var srouces = searchResposne.HitsMetadata.Hits.Select(h => h.Source);  
```  
   
검색어에 하이라이팅하는 방법도 있음.  
   
```  
var highlights = searchResponse.HitsMetadata.Hits.Select(h => h
      .Highlight
);
```   
  
    
- - -   
    
[참조 : ElasticSearch Reference(format)](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html)    
     
## format   
   
JSON 도큐먼트에서, 날짜데이터(date)는 문자열로 나타내진다.  
엘라스틱서치는 사전에 구성된 포맷셋을 사용하고, 이러한 문자열들을 UTC에서 milliseconds-since-the-epoch을 나타내는 long 값으로 파싱한다.  
   
빌트인 포멧 외에, `yyyy/MM/dd`와 같이 친숙한 커스텀 포맷을 명시할 수 있따.   
  
```   
PUT my_index
{
   "mappings" : {
      "properties" : {
         "date" : {
            "type" : "date",
            "format" : "yyyy-MM-dd"
         }
      }
   }
}
```   
   
date 값을 지원하는 많은 API들은 `now-1m/d`와 같은 데이터 매칭 표현식을 지원한다.  
      
cf) 엘라스틱서치에서 날짜 타입은 [ISO8601](https://ko.wikipedia.org/wiki/ISO_8601) 형식에 따라 입력된다.  
    일반적으로 다음과 같이 입력되면 자동으로 날짜 타입으로 인식된다.  
   
     2020-02-07   
     2019-02-07T15:19:40   
     2019-02-07T15:19:40+09:00  
     2019-02-07T15:19:40.428Z 
     
   위와 같은 ISO8601 형식이 아니라 "2020/02/07 16:32:30"와 같이 입력되면 text,keyword로 저장된다.  
   "2020/02/07 16:32:30" 같은 형식으로 날짜를 저장하려면 format옵셕을 사용해서 형태를 지정해야 한다.  
   
    
- - -   
    
[참조 : ElasticSearch Reference(Source filtering)](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html)    
   
### Source Filtering  
   
검색결과때 리턴받은 `_source` 필드를 컨트롤 할 수 있다.  
만약 `stored_fields` 파라미터를 사용하지 않거나 `_source` 필드가 비활성화되어있다면, 디폴트로는 `_source` 필드의 내용 전부가 검색 결과로 반환된다.  
`_source` 파라미터 값을 붙여서 `_source`값을 검색 대상에서 제외시킬 수 있으며, 또한 특정 필드를 검색결과에서 포함, 제외시키는 것도 가능하다.  
   
``` 
GET /_search
{
   "_source" : false,
   "query" : {
      "termm" : { "user" : "kimchy" }
   }
}
```       
    
```   
GET /_search
{
   "_source" : {
      "includes" : [ "ob1.*", "obj2.*" ],
      "excludes" : [ "*.description" ]
   },
   "query" : {
         "term" : { "user" : "kimchy" }
   }
}
```     
   
- - -   
     
      
[참조 : ElasticSearch Reference(fielddata)](https://www.elastic.co/guide/en/elasticsearch/reference/current/fielddata.html)    
      
       
## feilddata   
  
  
대부분의 필드들은 디폴트로 색인되고, 검색가능하게 된다.  
스크립트에서 정렬, 집계 그리고 필드값에 액세스하려면 검색과 다른 접근 패턴이 필요하다.  
   
검색은 "이 term을 포함하는 문서는 무엇인가?" 질문에 대한 답을 필요로 한다.  
반면에 정렬과 집계는 약간 다르다. "이 문서에서 이 필드의 값을 무엇인가?"가 관심대상이므로 역인덱스 정보가 아닌 document를 key로 필드정보를 담은 데이터 구조가 필요하다. 이 데이터 구조가 바로 fielddata이다.  
      
대부분의 필드들은 데이터 액세스 패턴에 대해 색인 시간 디스크상의 doc_value를 사용할 수 있지만,  
`text`필드는 doc_values를 지원하지 않는다.  
   
대신에, 텍스트 필드는 `feielddata`라고 불리는 쿼리시간(query-time) in-memory 데이터 구조를 사용한다.  
이 데이터구조는 필드가 집계, 정렬 또는 스크립트에 처음 사용될 때 필요에 따라 구축된다.  
이것은 term과 document 관계를 뒤집고(inverting), 결과를 JVM힙의 메모리에 저장하면서 디스크로부터 각각의 세그먼트에 대해 전체 역색인을 읽음으로써 구축된다.  
     
     
### Fielddata is disabled on `text` fields by defualt  
     
필드 데이터는 많은 양의 힙 공간을 차지한다. 특히, 높은 cardinality 텍스트 필드를 로드할때 더더욱 그렇다.   
일단 필드 데이터가 힙에 로드되면, 세그먼트 생애주기동안 유지된다.   
또한, 필드 데이터를 로딩하는 것은 사용자가 지연을 유발할 수 있는 매우 비싼 프로세스이다.  
이러한 이유떄문에 필드데이터는 디폴트로는 불가능하다.   
  
만약 정렬, 집계 또는 텍스트 필드에 대한 스크립트로부터 값에 접근하기 위한다면, 이 excpetion을 보게 될 것이다.   
```   
Fielddata is disabled on text fields by default.  Set `fielddata=true` on
[`your_field_name`] in order to load  fielddata in memory by uninverting the
inverted index. Note that this can however use significant memory.
```    
    
### Before enabling fielddata   
   
fielddata를 설정하기 전에 왜 텍스트 필드를 집계, 정렬 등에 사용하는지를 고려해야 한다.   
New York과 같은 단어가 new 또는 york을 검색할때 발견되게 하게 위해 텍스트 필드는 색인 전에 분석된다.  
이 필드에 대한 term aggregation은 New York이라는 단일 버킷을 원할 때, new 버킷과 york 버킷을 반환할 것이다.  
    
대신에, 전문 검색에 대해서는 반드시 텍스트 필드를 가져야 한다.  
그리고 그 텍스트 필드는 분석되지 않은 집계를 가능하게 하는 `doc_values`를 가지는 `keyword`필드를 가져야 한다.  
     
```   
PUT my_index
{
   "mappings" : {
      "properties" : {
         "my_field" : {    // 1
            "type" : "text",
            "fields" : {
               "keyword" : {     // 2
                  "type" : "keyword"
               }
            }
         }
      }
   }
}
```    
   
1. 검색에 사용되는 필드 : `my_field`   
2. `my_field.keyword` 필드는 집계, 정렬 또는 스크립트에서 사용됨.   
   
    
### Enablign fielddata on `text` fields  
   
기존의 `text`필드에 대해 PUT mapping API를 사용함으로써 필드데이터를 사용할 수 있다.  
    
```  
PUT my_idnex/_mapping
{
   "properties" : {
      "my_field" : {
            "type" : "text",
            "fielddata" : true
      }
   }
}
```  
- - -    
   
## 스크립트 이용해서 동적으로 필드 추가   
  ES는 스크립트 이용해서 사용자가 특정 로직 삽입하는것 가능  -> 스크립팅(Scripting)이라 함  
  스크립팅 활용해서 검색했을 때 특정 필드를 선택적으로 반환하거나 필드 값 수정 등 광범위한 작업 가능.  
  ES에서 동적스크립핑 (API 호출시 코드내 스크립트 직접 정의) 사용하려면 elasticsearch.yml 파일에 다음과 같은 설정 추가 필요  
  `script.disable_dynamic : false`     
  ES에서 쓰이는 스크립팅 전용 언어를 페인리스(Painless)라고 함.  
  
     
     
- - -    
   
## ETC    
  
* 엘라스틱서치는 실제로 저장된 Document의 원문을 검색하는것이 아니라 `inverted index`에서 `term(token)`중에 쿼리에서 질의한 검색 키워드를 찾는다.    
* Query VS Filter  [출저](http://guruble.com/elasticsearch-query-vs-filter/)  
  Query 스코어 값에 영향을 미치고, Filter는 스코어값에 영향을 미치지 않음.  
  Query는 캐싱되지 않고, Filter는 캐싱되므로 Filter 가 성능면에서 유리  
  RDBS를 이요할 때 사용했던 WHERE절 구문은 엘라스틱서치의 필터에 해당   
  Filter는 RDBMS의 WHERE절과 전체결과에서 원하는 결과를 추려내는 작업이라는 점에서 개념이 유사하고, 성능면에서 유리함.  
  엘라스틱서치에서 말하는 Query는 SQL Query의 개념과 차이점이 있음.  
  검색이 조건을 만족하넌 레코드를 찾는 개념을 넘어서서 가장 적합하고 유사성(스코어)가 높은 결과를 찾으라는 개념이 포함됨.   
* 엘라스틱서치에서 데이터를 매핑할떄 keyword type과 text type이 있는데,  
  키워드 타입(keyword type)은 정확한 매칭(exact)에서 사용하고, 텍스트 타입(text type)의 경우 analyzed 매칭에 사용한다.  
  텍스트 타입의 경우 형태소 분석을 통해 필드를 여러 term으로 나누어 역인덱싱의 과정을 거치게 되지만, 키워드 카입은 그 자체로 역인덱스 된다.  
     
   
