# Android视频点播安全文件下载快速接入指南

本文档提供视频加密下载并播放的快速接入指南，视频管理后台基于阿里云视频点播控制台，Android端基于阿里云视频点播SDK中的[高级播放器](https://help.aliyun.com/document_detail/61910.html?spm=a2c4g.11186623.6.683.QSC6ZP)。

[Demo下载](./res/alisafeplayer-master.zip)

#### demo实现功能
将加密文件预置在apk中，demo启动时将安全文件拷贝到sd卡中，下载一段加密视频并播放。

### 加密视频下载
#### 1.获取安全文件
参考[安全文件获取](https://help.aliyun.com/document_detail/57920.html?spm=a2c4g.11186623.2.9.sHzlGa)文档
[下载安全文件帮助视频](./res/file_download_demo.mp4)
#### 2.下载视频
参考[高级播放器](https://help.aliyun.com/document_detail/61910.html?spm=a2c4g.11186623.6.683.QSC6ZP)中的下载功能

```java
//TODO 替换成真实的要下载的视频VID
mVidSts.setVid("b9f396b5674240bd8a38b9688a064968");
mDownloadManager.setDownloadInfoListener(mDownloadInfoListener);
mDownloadManager.setRefreshStsCallback(mRefreshStsCallback);
mDownloadManager.prepareDownloadMedia(mVidSts);

private AliyunDownloadInfoListener mDownloadInfoListener = new AliyunDownloadInfoListener() {
    @Override
    public void onPrepared(List<AliyunDownloadMediaInfo> list) {
        AliyunDownloadMediaInfo info = list.get(0);
        //添加要下载的info到downloadManager中：
        mDownloadManager.addDownloadMedia(info);
        //开始下载，info为prepare要下载的info。
        mDownloadManager.startDownloadMedia(info);
    }

    @Override
    public void onStart(AliyunDownloadMediaInfo aliyunDownloadMediaInfo) {
    }

    @Override
    public void onProgress(AliyunDownloadMediaInfo aliyunDownloadMediaInfo, int i) {
    }

    @Override
    public void onStop(AliyunDownloadMediaInfo aliyunDownloadMediaInfo) {
    }

    @Override
    public void onCompletion(AliyunDownloadMediaInfo aliyunDownloadMediaInfo) {
    }

    @Override
    public void onError(AliyunDownloadMediaInfo aliyunDownloadMediaInfo, int i, String s, String s1) {
    }

    @Override
    public void onWait(AliyunDownloadMediaInfo aliyunDownloadMediaInfo) {
    }

    @Override
    public void onM3u8IndexUpdate(AliyunDownloadMediaInfo aliyunDownloadMediaInfo, int i) {
    }
};
```

注意对DownloadManager的配置（安全文件的路径设置）

```java
AliyunDownloadConfig config = new AliyunDownloadConfig();
//设置加密文件路径。使用安全下载的用户必须设置（在准备下载之前设置），普通下载可以不用设置。
config.setSecretImagePath(Storage.getEncryptedFile().toString());
//设置保存路径。请确保有SD卡访问权限。
config.setDownloadDir(Storage.getVideoDir(context).toString());
//设置最大下载个数，最多允许同时开启4个下载
config.setMaxNums(4);
AliyunDownloadManager.getInstance(context).setDownloadConfig(config);
```

#### 3.加密视频播放
加密视频的播放可以通过高级播放器SDK播放本地资源的方式进行
```java
String url = info.getSavePath() // AliyunDownloadMediaInfo中获取加密视频下载路径
AliyunLocalSource.AliyunLocalSourceBuilder asb = new AliyunLocalSource.AliyunLocalSourceBuilder();
asb.setSource(url);
AliyunLocalSource mLocalSource = asb.build();
aliyunVodPlayer.prepareAsync(mLocalSource);
```
