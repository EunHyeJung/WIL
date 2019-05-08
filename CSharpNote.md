### FileStream  
   
파일에 들어있는 데이터를 바이트배열로 읽고 쓰기 위한 기능 제공  
<i> cf) Stream : 파일, 네트워크 등에서 데이터를 바이트 단위로 읽고 쓰는 클래스  </i>
   
`  
public class FileStream : System.IO.Stream
`  
   
<b> 사용형식 </b>  
`  
public FileStream(string path, FileMode mode, FileAccess access, FileShare share)
`   
   
<b> FileMode </b>  
* FileMode.Open : 기존 파일을 연다.  
* FileMode.OpenOrCreate : 파일이 없으면 만들어서 연다.  
* FileMode.Append : 추가모드로 연다. 파일이 없으면 만든다.  
* FileMode.Create : 파일을 만드는데, 같은 파일명이 있으면 기존 파일을 지우고 새로 만든다.  
* FileMode.CreateNew : 파일을 만드는데, 같은 파일명이 있으면 예외가 발생.  

<b>FileStream.Flush</b> : 스트림에 대한 모든 버퍼를 지우고 버퍼링된 모든 데이터를 내부 장치에 저장(Close하기전 사용)  
    
   
- - -  
    
### StreamWriter   
   
TextWriter를 구현하여 특정 인코딩의 스트림에 문자를 씀.  
   
`
public class StreamWriter : System.IO.TextWriter  
`   
   
<b> 메서드 </b>  
* Close() : 현재 StreamWriter 개체 및 내부 스트림을 닫음.  
* Dispose() : 해당 TextWriter 개체에서 사용되는 리소스를 모두 해제.   

<b>StreamWriter 사용 예제</b>  
```  
namespace StreamReadWrite
{
   class Program
   {
      static void Main(string[] args)
      {
         // C드라이브에 있는 폴더 목록들을 가져옴
         DirectoryInfo[] cDirs = new DirectoryInfo(@"C:\").GetDirectories();
         
         // 각 폴더명을 파일에 작성
         using (StreamWriter sw = new StreamWriter("CDrivers.txt"))
         {
            foreach (DirectoryInfo dir in cDirs)
            {
               sw.WriteLine(dir.Name);
            }
         }
         
         // 파일로부터 데이터를 읽어서 한라인씩 출력  
         string line;
         using (StreamReader sr = new StreamReader("CDriveDirs.txt"))
         {
            while ((line != sr.ReadLine()) != null)
            {
               Console.WriteLine(line);
            }
         }
      }
   }
}
```   
   
- - -   
   
### 소멸자 (Destructor)   
  
객체가 메모리에서 제거될때마다 실행됨.   
가비지 컬렉터가 객체의 소멸을 관리하기 때문에 언제 실행될지 예측할 수 없으며,  
명시적으로 소멸자를 선언하지 않아도 컴파일러가 암시적으로 기본 소멸자를 생성.  
    
- - -  
   
### 병렬 프로그래밍  (Parallel Programming)   
     
병렬처리는 큰 일거리를 분할하는 단계, 분할된 작업들을 병렬 처리하는 단계, 그리고 결과를 집계하는 단계로 진행됨.  
일거리를 분할하는 방식은 크게 Data Parallelism과 Task Parallelism으로 나뉨.  
   
* <b>Data Parallelism</b>  
  대량의 데이터를 분할해서, 다중 CPU를 사용하여 동시에 데이터를 처리.   
  다중CPU를 사용하여 다중 쓰레드들이 각각 할당된 데이터를 처리하는데, 일반적으로 쓰레드당 처리 내용은 동일.  
   
* <b>Task Parallelism</b>  
  각 쓰레드들이 나눠서 다른 작업 TAsk들을 실행하는 것.  
   
많은 양의 데이터를 처리할 떄, 다른 스레드들이 변경되는값에 대해서 영향을 주지 않는다면, 병렬처리를 하면 처리 시간을 단축할 수 있다.  
다중스레드로 병렬처리하는 경우, 작업들이 인덱스 순서대로 진행되지는 않는다.  
   
    
#### Parallel 클래스    
    
Parallel 클래스는 Paralle.For(), Parallel.ForEach() 메서드를 통해 다중 CPU에서 다중 스레드가 병렬 데이터를 분할하여 처리하는 기능 제공.     
      
<b>Parallel 사용 예제</b>   
```
using System;
using System.Treading;
using SYstem.Treading.Tasks;

// 순차 처리 (단일스레드)
for (int i = 1; i <=100; i ++)
{
   Console.WriteLine("{0} : {1}", Thread.CurrentThread.ManagedTreadId, i);
}

// 병렬 처리
Parallel.For(1, 100, (i) => {
   Console.WriteLine("{0} : {1}", Thread.CurrentThread.ManagedTreadId, i);
});
```    
   
- - -  
   
### 용량 큰 텍스트 파일 멀티스레드로 읽기  
  
```  
// Method signarture : Parallel.ForEach(IEnumerable<TSource> source, Action<TSrouce> body)
Parallel.ForEach(File.ReadLines("파일경로"), (line, _, lineNumber) =>
{
   // code
});
```  
  
cf) `File.ReadLines`는 라인별로 문자열을 하나씩 리턴하는 Inumerable<string> 객체를 리턴함.  
     
   
- - -  
    
### decimal 및 numeric
  
전체 자리수와 소수 자리수가 고정된 숫자 데이터 형식  
decimal과 numeric은 동의어   

    
 
   
     
     
- - -   
     
[참조(MicroSoft_FileStream)](https://docs.microsoft.com/ko-kr/dotnet/api/system.io.filestream?view=netframework-4.7.2)   
[참조(MicroSfot_StreamWriter)](https://docs.microsoft.com/ko-kr/dotnet/api/system.io.streamwriter?view=netframework-4.7.2)  
[참조(C#생성자,소멸자)](https://076923.github.io/posts/C-15/)   
[참조(Microsoft_데이터병렬처리)](https://docs.microsoft.com/ko-kr/dotnet/standard/parallel-programming/data-parallelism-task-parallel-library)  
[참조(C#스터디_병렬프로그래밍)](http://www.csharpstudy.com/Threads/parallel.aspx) 
[참조(StackOverFlow_ReadLargeTxtFile)](https://stackoverflow.com/questions/17188357/read-large-txt-file-multithreaded)
