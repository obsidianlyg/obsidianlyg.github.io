---
title: 利用Java导入Excel到数据库
date: 2023-8-2 17:05:39
tags: Python
---



# EsayExcel的使用

## 引入依赖
```xml
<dependency>  
<groupId>com.alibaba</groupId>  
<artifactId>easyexcel</artifactId>  
<version>3.1.1</version>  
</dependency>
```
## 类属性中的注解
```java
# 默认下标从0开始，index可以不填，value值必填
@ExcelProperty(value = "excel名称", index = 0)
private String name;
# 忽略该属性
@ExcelIgnore  
private String sex;
```

## 代码部分
在Service中的代码如下
```java
    @Override
    public boolean importExcelSyncLineLoss(MultipartFile file, String dataDate) {
        try{
            //获取文件流
            InputStream inputStream = file.getInputStream();
            List<List<String>> heads = new ArrayList<>();
            // 内层list 有两个值，表示有两行，外层list有两个对象，表示有两列。同一行如果列名相同，将进行合并
            heads.add(Arrays.asList("类型", "类型1"));
            heads.add(Arrays.asList("类型","类型2"));
            heads.add(Arrays.asList("类型","类型3"));
            //easyexcel导入文件
            EasyExcel.read(inputStream, TPowerSyncLineLoss.class,new SyncLineLossImportListener(this, dataDate))
                    .head(heads)
                    .headRowNumber(2)
                    .sheet()
                    .doRead();
            return true;
        }catch (IOException e){
            e.printStackTrace();
            return false;
        }
    }
```
**若Excel中只有第一行有标题可以去除head与headRowNumber方法** 
SyncLineLossImportListener类
```java
public class SyncLineLossImportListener extends AnalysisEventListener<TPowerSyncLineLoss> {

    private static final Logger LOGGER = LoggerFactory.getLogger(SyncLineLossImportListener.class);
    /**
     * 每隔5条存储数据库，实际使用中可以3000条，然后清理list ，方便内存回收
     */
    private static final int BATCH_COUNT = 5;
    private List<TPowerSyncLineLoss> list = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);

    /**
     * 如果使用了spring,请使用这个构造方法。每次创建Listener的时候需要把spring管理的类传进来
     */
    private TPowerSyncLineLossService tPowerSyncLineLossService;
    private String dataDate;

    public SyncLineLossImportListener(TPowerSyncLineLossService tPowerSyncLineLossService) {
        this.tPowerSyncLineLossService = tPowerSyncLineLossService;
    }
    public SyncLineLossImportListener(TPowerSyncLineLossService tPowerSyncLineLossService, String dataDate) {
        this.tPowerSyncLineLossService = tPowerSyncLineLossService;
        this.dataDate = dataDate;
    }

    @Override
    public void invoke(TPowerSyncLineLoss syncLineLoss, AnalysisContext analysisContext) {
        LOGGER.info("解析到一条数据:{}", JSON.toJSONString(syncLineLoss));
        list.add(syncLineLoss);
        // 达到BATCH_COUNT了，需要去存储一次数据库，防止数据几万条数据在内存，容易OOM
        if (list.size() >= BATCH_COUNT) {
            saveData();
            // 存储完成清理 list
            list = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
        }
    }

    /**
     * 所有数据解析完成了 都会来调用
     *
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        // 这里也要保存数据，确保最后遗留的数据也存储到数据库
        saveData();
        LOGGER.info("所有数据解析完成！");
    }

    /**
     * 加上存储数据库
     */
    private void saveData() {
        LOGGER.info("{}条数据，开始存储数据库！", list.size());
        list.forEach(tmp->{tmp.setDataDate(dataDate);});
        tPowerSyncLineLossService.saveBatch(list);
        LOGGER.info("存储数据库成功！");
    }

}

```
