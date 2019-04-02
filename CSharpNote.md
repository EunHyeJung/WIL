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


[참조(MicroSoft_FileStream)](https://docs.microsoft.com/ko-kr/dotnet/api/system.io.filestream?view=netframework-4.7.2)   
  
