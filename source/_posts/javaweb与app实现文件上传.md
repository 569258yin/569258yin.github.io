---
title: javaweb与app实现文件上传
date: 2017-03-13 20:54:27
categories: Java
tags: 
    --Java
    --app
---
### 前言·
最近在做毕设，然后app需要发送图文消息，就需要在app端实现图片上传，在web端实现文件接收，中间遇到点小意外，记在这里。

### 1.开发所需第三方jar包
    app端：HttpURLConnection(jdk自带)
    web端: commons-fileupload
    maven路径：
```
    <dependency>
			<groupId>commons-fileupload</groupId>
			<artifactId>commons-fileupload</artifactId>
			<version>1.3.2</version>
    </dependency>
    
```

### 2.JavaWeb端代码
    服务器端主要是为了接收客户端的请求，从请求解析出文件，可支持多文件上传，并创建了文件夹和随机名字，以保证生成的文件路径唯一，这里路径有两个，一个是保存的本地真实路径，用于
    写文件，第二个是为了生成url，便于客户端访问，并以json的方式进行返回。
#### Controller代码
```

    import java.io.File;
    import java.io.FileOutputStream;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.OutputStream;
    import java.io.UnsupportedEncodingException;
    import java.util.ArrayList;
    import java.util.List;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import org.apache.commons.fileupload.FileItem;
    import org.apache.commons.fileupload.FileUploadException;
    import org.apache.commons.fileupload.ProgressListener;
    import org.apache.commons.fileupload.disk.DiskFileItemFactory;
    import org.apache.commons.fileupload.servlet.ServletFileUpload;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import com.aygxy.fmaket.bean.ImagePath;
    import com.aygxy.fmaket.debug.DebugLog;
    import com.aygxy.fmaket.param.GlobalParams;
    import com.aygxy.fmaket.util.FileUtil;
    import com.aygxy.fmaket.util.GsonUtil;

    @Controller
    @RequestMapping("file")
    public class FileAction {

        private final static String ENCODING = "utf-8";

        static{

        }

        @RequestMapping("imgUpload.action")
        public void imageUpload(HttpServletRequest request,HttpServletResponse response){
            //创建工厂
            DiskFileItemFactory factory = new DiskFileItemFactory();
            //设置缓冲区的大小
            factory.setSizeThreshold(1024*1024*5);
            String result = "";
            //本地服务器真实路径
            String imgPath = request.getSession().getServletContext().getRealPath(File.separator+"extimg");
            //上传时生成的临时文件保存目录
            String tempPath = request.getSession().getServletContext().getRealPath(File.separator+"temp");
            //设置上传文件临时文件的保存路径
            File tmpFile = new File(tempPath);
            if (!tmpFile.exists()) {
                //创建临时目录
                tmpFile.mkdir();
            }
            factory.setRepository(tmpFile);
            InputStream is = null;
            OutputStream os = null;
            try {
                //创建一个文件上传解析器
                ServletFileUpload upload = new ServletFileUpload(factory);
                upload.setProgressListener(new ProgressListener() {
                    @Override
                    public void update(long pBytesRead, long pContentLength, int arg2) {
                        DebugLog.logger.info("文件大小为：" + pContentLength + ",当前已处理：" + pBytesRead);
                    }
                });
                //设置编码格式 
                upload.setHeaderEncoding(ENCODING);
                //判断是否是表单提交
                if(!ServletFileUpload.isMultipartContent(request)){
                    DebugLog.logger.info("不正确的文件格式");
                    result = GsonUtil.objectToString(new ImagePath("-1", "不正确的文件格式"));
                    response.getOutputStream().write(result.getBytes(ENCODING));
                    return;
                }
                //设置图片最大限制
                upload.setFileSizeMax(1024*1024);
                //使用解析器对上传的文件进行解析
                List<FileItem> list = upload.parseRequest(request);
                DebugLog.logger.debug("解析出的文件列表为"+list.toString());
                if(list.size() > 0 ){
                    List<String> imageLists = new ArrayList<String>();
                    for (FileItem fileItem : list) {
                        //如果封装的是普通的数据
                        if (fileItem.isFormField() && fileItem.getSize() > 0) {
                            String requestName = fileItem.getFieldName();
                            //解决普通输入项的中文乱码问题
                            String requestValue = fileItem.getString(ENCODING);
                            DebugLog.logger.info("文件上传携带参数==>"+requestName+":"+requestValue);
                        }else{	//封装的是上传的文件
                            //得到上传的文件名
                            String fileName = fileItem.getName();
                            DebugLog.logger.debug("上传文件的文件名："+fileName);
                            if (fileName == null || fileName.trim().equals("")) {
                                continue;
                            }
                            //注意：不同的浏览器提交的文件名是不一样的，有些浏览器提交上来的文件名是带有路径的，
                            //如：  c:\a\b\1.txt，而有些只是单纯的文件名，如：1.txt
                            //处理获取到的上传文件的文件名的路径部分，只保留文件名
                            fileName = fileName.substring(fileName.lastIndexOf("\\")+1);
                            //得到上传文件的扩展名
                            String fileExtName = fileName.substring(fileName.lastIndexOf(".")+1);
                            DebugLog.logger.debug("上传的文件的扩展名是："+fileExtName);
                            boolean allowCount = false;
                            for(int i = 0;i<GlobalParams.IMAGE_ALLOW_END.length;i++){
                                if(GlobalParams.IMAGE_ALLOW_END[i].equals(fileExtName)){
                                    allowCount = true;
                                    break;
                                }
                            }
                            if(!allowCount){
                                DebugLog.logger.info("文件后缀不正确");
                                result = GsonUtil.objectToString(new ImagePath("-2", "文件格式不正确"));
                                response.getOutputStream().write(result.getBytes(ENCODING));
                                return;
                            }
                            //获取item上传的输入流
                            //获取唯一名，随机生成
                            String saveFileName= FileUtil.makeFileName(fileExtName);
                            //根据文件名记性hash生成文件夹
                            String filePath = FileUtil.makePath(saveFileName, "");
                            String realFilePath = imgPath + filePath;
                            //用于url生成
                            String otherFilePath = request.getContextPath() + File.separator + "extimg" + filePath;
                            //创建文件夹
                            File filePathFile = new File(realFilePath);
                            if (!filePathFile.exists()) {
                                filePathFile.mkdirs();
                            }
                            //创建文件
                            File file = new File(filePathFile,saveFileName);
                            if (!file.exists()) {
                                file.createNewFile();
                            }
                            DebugLog.logger.debug("本地创建的文件路径："+file.getAbsolutePath());
                            is = fileItem.getInputStream();
                            os = new FileOutputStream(file);
                            //创建一个缓冲区
                            byte[] buff = new byte[2048];
                            int len = 0;
                            while((len = is.read(buff)) != -1){
                                os.write(buff, 0, len);
                            }
                            //删除处理文件的临时文件
                            fileItem.delete();
                            otherFilePath = GlobalParams.SERVER_URL + otherFilePath + File.separator + saveFileName;
                            otherFilePath =  otherFilePath.replace('\\', '/');
                            DebugLog.logger.debug("生成的图片url为:"+otherFilePath);
                            imageLists.add(otherFilePath);
                        }
                    }
                    result = GsonUtil.objectToString(new ImagePath(imageLists));
                    response.getOutputStream().write(result.getBytes(ENCODING));
                }
            } catch (FileUploadException e) {
                DebugLog.logger.error("文件上传失败", e);
            } catch (UnsupportedEncodingException e) {
                DebugLog.logger.error("不支持的编码格式", e);
            } catch (IOException e) {
                DebugLog.logger.error("文件读写失败", e);
            } catch (Exception e) {
                DebugLog.logger.error("文件上传未知异常", e);
            }finally{
                //关闭流
                if(is != null){
                    try {
                        is.close();
                    } catch (Exception e) {
                    }
                }
                if(os != null){
                    try {
                        os.close();
                    } catch (Exception e) {
                    }
                }
            }
        }
    }
    
```
#### FileUtil 代码

```

    public class FileUtil {
        /**
         * 根据文件后缀生成一个随机的文件名
         * @param fileExtName
         * @return
         */
        public static String makeFileName(String fileExtName){  
            //为防止文件覆盖的现象发生，要为上传文件产生一个唯一的文件名
            return UUID.randomUUID().toString() + "." + fileExtName;
        }

        /**
         * 为防止一个目录下面出现太多文件，要使用hash算法打散存储
         *
         * @param filename 文件名，要根据文件名生成存储目录
         * @param savePath 文件存储路径
         * @return 新的存储目录
         */ 
        public static String makePath(String filename,String savePath){
            //得到文件名的hashCode的值，得到的就是filename这个字符串对象在内存中的地址
            int hashcode = filename.hashCode();
            int dir1 = hashcode&0xf;  //0--15
            int dir2 = (hashcode&0xf0)>>4;  //0-15
            int dir3 = (int) (100000 + Math.random()*999999);
            //构造新的保存目录
            String dir = savePath + File.separator + dir1 + File.separator + dir2 + File.separator + dir3;  //upload\2\3  upload\3\5
    //		//File既可以代表文件也可以代表目录
    //		File file = new File(dir);
    //		//如果目录不存在
    //		if(!file.exists()){
    //			//创建目录
    //			file.mkdirs();
    //		}
            return dir;
        }

        /**
         * 得到要删除图片的真实路径
         * @param request
         * @param tempPath
         * @return
         */
        public static String getLocalImageUrl(String tempPath){
            String url = "";
            String[] strs = tempPath.split("/");
            for (int i = 0; i < strs.length; i++) {
                System.out.println(strs[i]);
            }
    //		url = GlobalParams.path+File.separator+strs[5]+File.separator+strs[6];
            System.out.println(url);		
            return url;
        }


        public static void main(String[] args) {
            System.out.println(makePath("1.jpg", "Path"));
        }
    }
    
```

### app端
    
    app 端利用httpConnection，并设置请求方式为post，将context-type改为multipart/form-data,一定要注意分割符的位置，我是因为这导致两种上传不成功，一是服务器端明明能接收
    到文件，但是就是从request解析的时候为空，二是批量上传的时候发现只能上传成功一张，服务器并没有解析出多个文件。
    
```

        /**
         * 上传图片
         * @param files
         * @return
         */
        public static String uploadImage(List<File> files) {
            URL url;
            DataOutputStream ds = null;
            BufferedReader bufferedReader = null;
            //为了分割
            String end = "\r\n";
            String Hyphens = "--";
            String boundary = "WUm4580jbtwfJhNp7zi1djFEO3wNNm";
            try {
                if (files != null && files.size() > 0) {
                    url = new URL(ConstantValue.URL_ROOT + ConstantValue.IMAGE_UPLOAD);
                    // 打开链接
                    URLConnection connection = url.openConnection();
                    HttpURLConnection httpURLConnection = (HttpURLConnection) connection;
                    httpURLConnection.setRequestMethod("POST");
                    httpURLConnection.setReadTimeout(30000);
                    httpURLConnection.setRequestProperty("Connection", "Keep-Alive");
                    httpURLConnection.setRequestProperty("Content-type", "multipart/form-data;boundary=" + boundary);
                    httpURLConnection.setRequestProperty("charset", "utf-8");
                    httpURLConnection.setUseCaches(false);
                    httpURLConnection.setDoInput(true);
                    httpURLConnection.setDoOutput(true);
                    ds = new DataOutputStream(httpURLConnection.getOutputStream());
                    for (int i = 0;i<files.size();i++) {
                        File file = files.get(i);
                        //注意加上分隔符此时只有一个Hyphens
                        ds.writeBytes(Hyphens + boundary + end);
                        //这里的格式不能改变，我将name换了别的名字结果服务器端不能正常接收文件
                        ds.writeBytes("Content-Disposition: form-data; "
                                + "name=\"file"+i+"\";filename=\"" + file.getName() + "\"" + end);
                        ds.writeBytes(end);
                        FileInputStream fStream = new FileInputStream(file);
                        byte[] buff = new byte[1024];
                        int length = -1;
                        while((length = fStream.read(buff)) != -1){
                            ds.write(buff,0,length);
                        }
                        ds.writeBytes(end);
                        fStream.close();
                    }
                    //结尾分隔符，注意此处为Hyphens + boundary + Hyphens+ end,有两个Hyphens，我刚开始没注意，结果就是不能批量上传文件
                    ds.writeBytes(Hyphens + boundary + Hyphens+ end);
                    ds.flush();
                    bufferedReader = new BufferedReader(
                            new InputStreamReader(httpURLConnection.getInputStream(),"UTF-8"));
                    String jsonString2 = bufferedReader.readLine();
                    Logger.d(jsonString2);
                    return jsonString2;
                }
            } catch (Exception e) {
                e.printStackTrace();
            }finally {
                if(ds != null){
                    try {
                        ds.close();
                    } catch (Exception e) {
                    }
                }
                if(bufferedReader != null){
                    try {
                        bufferedReader.close();
                    } catch (Exception e) {
                    }
                }
            }
            return null;
        }

```
### 结尾
有时候觉得自己还是不够认真，在网上搜了这方面的经验和代码，结果自己实现的时候会因为一些稍微不一样的地方导致各种不成功，就很无奈，后来就慢慢排查，细心一点就可以找到问题的关键了，
暂时只写了一种客户端的实现上传的办法，还有一种是利用httpClient进行上传文件，代码稍有不同，之后再补上。关于分隔符的问题还没搞懂，没有去研究其实现原理，可能也存在误解，但这份代码时测试的没有问题的。


