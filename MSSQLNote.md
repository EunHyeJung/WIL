- - -  
    
### MERGE문  
  
MERGE문 사용시 조건에 따라 INSERT, UPDATE, DELETE 등을 한 문장으로 간단히 실행가능.   
MERGE문은 먼저 TARGET과 SOURCE를 지정. 
TARGET : DML을 실제 수행하는 대상  
SOURCE : TARGET과 비교하여 새로 추가하거나 갱신할 데이터를 가진 테이블   
       
```sql  
MERGE Customer AS c
USING (SELECT Name, Email From CustUpdQueue) AS cq
ON c.Name = cq.Name
WHEN MATCHED THEN
     UPDATE SET c.Email = cq.Email
WHEN NOT MATCHED THEN
     INSERT (Name, Email) VALUES(cq.Name, cq.Email);
```   
    
MERGE문 다음에 TARGET이 되는 테이블을 지정하고,  
USING 다음에 소스 지정.
소스는 테이블이나 임시테이블, 리터럴값, 변수가 될 수 있음.     
ON 키워드 다음에는 타겟과 소스가 매치되는 조건식 지정.  
위의 예의 경우 이름이 매치되는 경우 Email을 업데이트 하고, 이름이 없는 경우 새 레코드를 INSERT 함.  
      
- - -  
     
### 임시테이블(#Table)    
    
테이블의 이름은 `#`으로 시작.   
사용자가 연결이 끊겼을 때 임시 테이블이 삭제되지 않는 경우, SQL Server는 자동으로 임시 테이블을 삭제.  
인덱스를 작성할 수 있으며, FK(외래키)를 제외한 나머지 제약 지정 가능.  
ALTER TABLE 가능.  
INSERT INTO, BULK INSERT문과 함께 사용 가능.     
대용량에서 쿼리 비용 유리하며, 주로 INSERT되는 데이터가 100건 이상인 경우 사용.  
   
     
### 테이블 변수(@Table)   
   
테이블 이름은 `@`으로 시작.  
명시적으로 삭제하지 않을 경우 배치 처리 기간동안 존재   
소용량에서 쿼리 비용 유리하며, INSERT되는 데이터가 100건 이하, 1~2개 컬럼시 적합  
가능하면 PK 또는 UNIQUE 컬럼을 지정해서 데이터가 정렬된 형태로 저장하여 사용.   
      
      
- - -   
    
    
### NULLIF(expr1, expr2)   
  
지정된 두 식이 같으면 Null을 반환하고, 다르면 앞의 expr1을 반환함.   
ex) 특정 데이터가 빈값인지, 아닌지 확인하는 경우  
`NULLIF(data, '') IS NULL `   
   
      

       
- - -  
   
[참조(MicroSoft_Decimal)](https://docs.microsoft.com/ko-kr/sql/t-sql/data-types/decimal-and-numeric-transact-sql?view=sql-server-2017)
[참조(C#스터디 MERGE문)](http://www.sqlprogram.com/TIPS/tip-merge.aspx)    
[참조(임시테이블,테이블변수)](https://blog.naver.com/islove8587/220608680181)   
       
