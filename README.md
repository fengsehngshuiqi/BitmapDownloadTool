# BitmapDownloadTool
Download Bitmap tool is based on Picasso （基于Picasso的图片下载封装类）




#基于Piccaso的图片下载工具类封装

感谢强哥帮助修改完善~


#核心下载类BitmapDownloadHelper


1.downloadSinglePic(String url, final OnDownloadResultListener listener) 下载单张图片

2.下载多张图片downloadPic(List<String> urlList)

3.在下载多张图片时候回调接口置空


整体代码结构如下


#完整代码github链接如下：

https://github.com/fengsehng/BitmapDownloadTool



```

public class BitmapDownloadHelper {
  /**
   * 用于存储返回的结果
   */
  private static Map<String, Boolean> sResultMap = new HashMap<>();
  private static String PIC_SIZE = NewHouseConstantUtils.IMAGE_SIZE_POSTFIX.IMG_FRAME_ALBUM;

  /**
   * 批量下载图片
   * @param urlList
   */
  public static void downloadPic(List<String> urlList) {
    ToastUtil.toast(Entry.getContext(), "图片保存中");
    for (final String url : urlList) {
      downloadSinglePic(url, null);
    }
    toastResult(urlList.size());
  }

  /**
   * 下载单张图片
   * @param url
   */
  public static void downloadSinglePic(String url, final OnDownloadResultListener listener) {
      }

  /**
   * 最终的弹出结果
   * @param total
   */
  private static void toastResult(int total) {
   
  }

  /**
   * 回调下载状态接口
   */
  public interface OnDownloadResultListener{
    void onDownloadBegin();
    void onDownloadResult(boolean success);
  }
}

```


#文件处理类


1.判断指定的文件夹是否存在图片

2.图片的文件名字是经过md5处理的

3.获取系统相册的目录

代码框架如下：


```


/**
 * Created by（fengsehng） on 2017/9/19.
 * @author fengsehng
 *
 */

public class PicFileUtil {
  /**
   * 相册文件夹
   */

  public static String PATH_PHOTOGRAPH = “/fengsehng/“;


  /**
   * 保存图片
   *
   * @param bitmap
   * @param filePath
   */
  public static void saveBitmap(Bitmap bitmap, String filePath) {
    FileOutputStream bos = null;
    File file = new File(filePath);
    if (!file.getParentFile().exists()) {
      file.getParentFile().mkdirs();
    }
    try {
      bos = new FileOutputStream(file);
      bitmap.compress(Bitmap.CompressFormat.PNG, 100, bos);
    } catch (FileNotFoundException e) {
      e.printStackTrace();
    } finally {
      try {
        bos.flush();
        bos.close();
      } catch (IOException e) {
        e.printStackTrace();
      }
    }
  }


  public static void deleteDir(File directory) {
    if (directory != null){
      if (directory.isFile()) {
        directory.delete();
        return;
      }

      if (directory.isDirectory()) {
        File[] childFiles = directory.listFiles();
        if (childFiles == null || childFiles.length == 0) {
          directory.delete();
          return;
        }

        for (int i = 0; i < childFiles.length; i++) {
          deleteDir(childFiles[i]);
        }
        directory.delete();
      }
    }
  }

  /**
   * 获取保存到相册的最终文件
   * @param filePath
   * @param imageName
   * @return
   */
  public static File getDCIMFile(String filePath, String imageName) {
    if (Environment.getExternalStorageState().equals(
        Environment.MEDIA_MOUNTED)) { // 文件可用
      // 首先保存图片
      String storePath = Environment.getExternalStorageDirectory().getAbsolutePath() + File.separator + filePath;
      File appDir = new File(storePath);
      if (!appDir.exists()) {
        appDir.mkdir();
      }
      File file = new File(appDir, imageName);
      if (!file.exists()) {
        try {
          //在指定的文件夹中创建文件
          file.createNewFile();
        } catch (Exception e) {
        }
      }
      return file;
    } else {
      return null;
    }

  }



  public static File saveBitmap2(Bitmap bitmap, String fileName, File baseFile) {
    FileOutputStream bos = null;
    File imgFile = new File(baseFile, "/" + fileName);
    try {
      bos = new FileOutputStream(imgFile);
      bitmap.compress(Bitmap.CompressFormat.PNG, 100, bos);
    } catch (FileNotFoundException e) {
      e.printStackTrace();
    } finally {
      try {
        bos.flush();
        bos.close();
      } catch (IOException e) {
        e.printStackTrace();
      }
    }
    return imgFile;
  }

  public static File getBaseFile(String filePath) {
    if (Environment.getExternalStorageState().equals(
        Environment.MEDIA_MOUNTED)) { // 文件可用
      File f = new File(Environment.getExternalStorageDirectory(),
          filePath);
      if (!f.exists())
        f.mkdirs();
      return f;
    } else {
      return null;
    }
  }


  /**
   * 由指定的路径和文件名创建文件
   */
  public static File createFile(String path, String name) throws IOException {
    File folder = new File(path);
    if (!folder.exists()) {
      folder.mkdirs();
    }
    File file = new File(path + name);
    if (!file.exists()) {
      file.createNewFile();
    }
    return file;
  }

  /**
   * 把url字符串转化为对应的文件
   * @param urlList
   * @return
   */
  public static HashMap<String,String> getMd5List(List<String> urlList){
    HashMap<String,String> map = new HashMap<>();
    for(int i= 0; i < urlList.size();i++){
      map.put(Md5Util.getMd5(urlList.get(i)) + ".jpg",urlList.get(i));
    }
    return map;
  }
  /**
   * 过滤已经存在的md5文件
   * @param urlList
   * @return
   */
  public static List<String> getResultList(List<String> urlList){
    HashMap<String,String> map = new HashMap<>();
    map = getMd5List(urlList);
    List<String> fileNameList = new ArrayList<>();
    for(Map.Entry<String,String> entry:map.entrySet()){
      fileNameList.add(entry.getKey());
    }
    String storePath = Environment.getExternalStorageDirectory().getAbsolutePath() + File.separator + PicFileUtil.PATH_PHOTOGRAPH;
    File appDir = new File(storePath);
    if (!appDir.exists()) {
      appDir.mkdir();
    }
    File[] fileList = appDir.listFiles();
    Iterator<String> it = fileNameList.iterator();
    while(it.hasNext()){
      String x = it.next();
      for(File f:fileList){
        if(x.equals(f.getName())){
          map.remove(x);
        }
      }
    }
    List<String> resultUrlList = new ArrayList<>();
    for(Map.Entry<String,String> entry:map.entrySet()){
      resultUrlList.add(entry.getValue());
    }
    return resultUrlList;
  }

  /**
   * 图片是否存在
   * @param fileName
   * @return
   */
  public static boolean isFileExit(String fileName){
    String storePath = Environment.getExternalStorageDirectory().getAbsolutePath() + File.separator + PicFileUtil.PATH_PHOTOGRAPH;
    File appDir = new File(storePath);
    if (!appDir.exists()) {
      appDir.mkdir();
    }
    File[] files = appDir.listFiles();
    for (File file: files) {
      if (fileName.equals(file.getName())){
        return true;
      }
    }
    return false;
  }
}


```


#完整代码链接：

https://github.com/fengsehng/BitmapDownloadTool






