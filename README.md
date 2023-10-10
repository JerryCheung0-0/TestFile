# TestFile


```
package com.demo.account.service;

import org.junit.Test;

import java.io.File;
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

/**
 * Created by on 2023/10/10 10:17
 *
 * @Maintainance: jerryzshiyan@163.com
 * @author: Jerry
 * @Project: nacos-service-consumer
 */
public class TestFile {

    @Test
    public void testSplit() throws IOException {
        //源文件地址
        File sourceFile = new File("/Users/jerry/Desktop/PPT/ToastMaster-Speech.zip");
        //块文件目录
        String chunkFileFolder = "/Users/jerry/Desktop/PPT/split/";
        //块文件大小
        int chunkFileSize = 10 * 1024 * 1024;
        //块文件数量
        int chunkFileNum = (int) Math.ceil(sourceFile.length() * 1.0 / chunkFileSize);
        //
        RandomAccessFile readFile = new RandomAccessFile(sourceFile, "r");
        byte[] bytes = new byte[1024];
        for(int i = 0; i < chunkFileNum; i++) {
            File chunkFile = new File(chunkFileFolder + i);
            int len = -1;
            //创建块文件
            RandomAccessFile writeFile = new RandomAccessFile(chunkFile, "rw");
            while((len = readFile.read(bytes)) != -1) {
                writeFile.write(bytes, 0 ,len);
                //如果块文件大小达到1M，就读下一块
                if(chunkFile.length() >= chunkFileSize) {
                    break;
                }
            }
            writeFile.close();
        }
        readFile.close();
    }

    @Test
    public void merge() throws IOException {
        //块文件目录
        String chunkFileFolderPath = "/Users/jerry/Desktop/PPT/split";
        //获取块文件对象
        File chunkFileFolder = new File(chunkFileFolderPath);
        //块文件列表
        File[] files = chunkFileFolder.listFiles();
        //将文件排序
        List<File> filesList = Arrays.asList(files);
        Collections.sort(filesList, (o1, o2) -> {
            if(Integer.parseInt(o1.getName()) > Integer.parseInt(o2.getName())){
                return 1;
            }
            return -1;
        });
        //合并后的文件
        File mergerFile = new File("/Users/jerry/Desktop/PPT/ToastMaster-Speech-copy.zip");
        //创建新文件
        boolean newFile = mergerFile.createNewFile();
        //创建写对象
        RandomAccessFile writeFile = new RandomAccessFile(mergerFile, "rw");

        byte[] b = new byte[1024];
        for(File chunkFile : filesList){
            int len = -1;
            //创建读块文件的对象
            RandomAccessFile readFile = new RandomAccessFile(chunkFile, "r");
            while((len = readFile.read(b)) != -1){
                writeFile.write(b, 0, len);
            }
            readFile.close();
        }
        writeFile.close();
    }
}
```
