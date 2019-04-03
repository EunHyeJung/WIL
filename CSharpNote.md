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
   
[참조(MicroSoft_FileStream)](https://docs.microsoft.com/ko-kr/dotnet/api/system.io.filestream?view=netframework-4.7.2)   
[참조(MicroSfot_StreamWriter)](https://docs.microsoft.com/ko-kr/dotnet/api/system.io.streamwriter?view=netframework-4.7.2)  
[참조(C#생성자,소멸자)](https://076923.github.io/posts/C-15/)   
    
