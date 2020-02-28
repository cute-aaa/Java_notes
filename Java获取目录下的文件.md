# Java获取目录下的文件



```java
import java.io.File;    
  
/**  
 * @author yinxm  
 * @version 1.0 2005/06/17  
 *   
 * This class can take file's path and file's name;  
 * you must give the path where you want to take the file.  
 */  
public class TakeFilePathAndName {    
  
    public static void main(String[] args) {   
        // This is the path where the file's name you want to take.   
        String path = "C://Documents and Settings//yinxm//デスクトップ//TestFile";   
        getFile(path);   
    }   
       
    private static void getFile(String path){   
        // get file list where the path has   
        File file = new File(path);   
        // get the folder list   
        File[] array = file.listFiles();   
          
        for(int i=0;i<array.length;i++){   
            if(array[i].isFile()){   
                // only take file name   
                System.out.println("^^^^^" + array[i].getName());   
                // take file path and name   
                System.out.println("#####" + array[i]);   
                // take file path and name   
                System.out.println("*****" + array[i].getPath());   
            }else if(array[i].isDirectory()){   
                getFile(array[i].getPath());   
            }   
        }   
    }   
}   
```

