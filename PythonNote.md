## Python Note
    
- - -  
    
### 리스트내포 (List Comprehension)   
    
cf) 파이썬의 Comprehension은 이미 정의된 시퀀스를 이용해서 짧고 간결하게 새로운 시퀀스(list, set, dictionary와 같은)를 만드는 것을 제공.  
<b>형식</b>   
`  
[출력표현식 for 요소 in 입력시퀀스 [if조건식]] 
`  
  
ex1) 리스트 a = [1,2,3,4]에 짝수만 3을 곱하여 출력  
```python  
a = [ 1,2,3,4 ]
result = [num * 3 for num in a if num % 2 == 0]
print(result)
```  
   
- - -   
   
