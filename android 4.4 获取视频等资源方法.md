Android 应用中获取视频资源（支持KitKat+）
========================================
问题现象：
---------
在dzb项目中，发布活动--->添加视频或图片，有的手机上会报错，错误提示查找资源得到的cursor不对<br>
产生问题原因：
-------------
* 前提1. KitKat之后版本中无法操作外置Sdcard，详细内容参考：[外部存储权限](http://www.2cto.com/kf/201405/303835.html)<br>
* 前提2. SAF，Android4.4中引入了Storage Access Framework存储访问框架，简称（SAF）。该框架是为了符合前提1的权限控制实现的。通过各种DocumentProvider(ContentProvider的子类)来向外提供资源。详细内容参考：[android存储访问框架Storage Access Framework](http://blog.csdn.net/jianghejie123/article/details/40503607)

有了以上两个前提，就可以判断出问题的原因。<br>因为有了documentProvider,所以有了和以前不一样的URI，格式如`content://com.android.providers.media.documents/document/video%3A358817`<br>而我们现有代码处理的URI还是默认为原contentProvider的uri,格式如`content://media/external/video/media/263262`<br>用同样的方式去找资源就会出错
<br>解决方式：
---------
拿到URI以后，判断到底是属于哪种类型的，根据不同类型采取不同的方法<br>
以下是GIT上开源的一个例子，getpath()方法最终要实现的是拿到文件的路径，在其它项目中可以根据需要进行修改<br>
```JAVA
public static String getPath(final Context context, final Uri uri) {
    final boolean isKitKat = Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT;
    //第一种情况 DocumentProvider，需要符合两点1，版本要>=19;2,uri是documentUri
    if (isKitKat && DocumentsContract.isDocumentUri(context, uri)) {
        if (isExternalStorageDocument(uri)) {
            final String docId = DocumentsContract.getDocumentId(uri);
            final String[] split = docId.split(":");
            final String type = split[0];
            if ("primary".equalsIgnoreCase(type)) {
                return Environment.getExternalStorageDirectory() + "/" + split[1];
            }
        }else if (isDownloadsDocument(uri)) {
            final String id = DocumentsContract.getDocumentId(uri);
            final Uri contentUri = ContentUris.withAppendedId(Uri.parse("content://downloads/public_downloads"),Long.valueOf(id));
            return getDataColumn(context, contentUri, null, null);
        }else if (isMediaDocument(uri)) {
            final String docId = DocumentsContract.getDocumentId(uri);
            final String[] split = docId.split(":");
            final String type = split[0];
            Uri contentUri = null;
            if ("image".equals(type)) {
                contentUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
            }else if ("video".equals(type)) {
                contentUri = MediaStore.Video.Media.EXTERNAL_CONTENT_URI;
            }else if ("audio".equals(type)) {
                contentUri = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI;
            }
            final String selection = "_id=?";
            final String[] selectionArgs = new String[] { split[1] };
            return getDataColumn(context, contentUri, selection,selectionArgs);
        }
    }
    //第二种情况  MediaStore (and general)
    else if ("content".equalsIgnoreCase(uri.getScheme())) {
        return getDataColumn(context, uri, null, null);
    }
    //第三种情况 File
    else if ("file".equalsIgnoreCase(uri.getScheme())) {
        return uri.getPath();
    }
    return null;
}
```
```JAVA
public static String getDataColumn(Context context, Uri uri,String selection, String[] selectionArgs) {
    Cursor cursor = null;
    try {
        cursor = context.getContentResolver().query(uri, null,selection, selectionArgs, null);
        if (cursor != null && cursor.moveToFirst()) {
            final int column_index = cursor.getColumnIndexOrThrow("_data");
            return cursor.getString(column_index);
        }
    } finally {
        if (cursor != null)
        cursor.close();
    }
    return null;
}
```
```Java
/**
* @return Whether the Uri authority is ExternalStorageProvider.
*/
public static boolean isExternalStorageDocument(Uri uri) {
    return "com.android.externalstorage.documents".equals(uri.getAuthority());
}
/**
* @return Whether the Uri authority is DownloadsProvider.
*/
public static boolean isDownloadsDocument(Uri uri) {
    return "com.android.providers.downloads.documents".equals(uri.getAuthority());
}
/**
* @return Whether the Uri authority is MediaProvider.
*/
    public static boolean isMediaDocument(Uri uri) {
return "com.android.providers.media.documents".equals(uri.getAuthority());
}
```
> 示意图
![](https://github.com/kongling1014/Note/raw/master/note_pic.jpg) 
