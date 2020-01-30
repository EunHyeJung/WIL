`Elastic Search 학습 내용 정리`   
   
[참조 : ElasticSearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/analysis.html)    
  
## Text Analysis  
  
검색할 때 사용되는 역색인(inverted index)에는 token 또는 term들이 담겨져 있음.  
텍스트 분석은 텍스트(text)를 token 또는 term으로 변환하는 과정임.   
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
    
     
