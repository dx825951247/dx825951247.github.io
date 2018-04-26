---
title: JavaCSV读写csv文件
date: 2018-04-23 19:04:33
tags: [通用]
---
添加javacsv jar包
<dependency>
　　<groupId>net.sourceforge.javacsv</groupId>
　　<artifactId>javacsv</artifactId>
　　<version>2.0</version>
</dependency>
<!--more-->
``` java
public static void createCSV(String filePath,
			List<TBusiLogBackup> tBusiLogBackupList) throws Exception {
　　File file = new File(filePath);
　　FileOutputStream fos = null;
　　boolean existFile = true;
　　if (!file.exists()) {
　　　　file.createNewFile();
　　　　fos = new FileOutputStream(file);
　　　　existFile = false;
　　} else {
　　　　//写入文件末尾处
　　　　fos = new FileOutputStream(file, true);
　　}
    //使用输出流创建一个CsvWriter对象来写入数据。
　　CsvWriter csvWriter = new CsvWriter(fos, ',', Charset.forName("UTF-8"));

　　if (!existFile) {
　　　　//文件头
　　　　String[] headers = { "LOG_ID", "SRC_SYS", "SRC_MOD", "BUSI_TYPE",
　　　　　　　　"OPT_TYPE", "REC_PK", "UPDATE_ID", "CONTENT" };
　　　　csvWriter.writeRecord(headers);
　　}

　　for (TBusiLogBackup item : tBusiLogBackupList) {
　　　　csvWriter.write(String.valueOf(item.getLogId()));
　　　　csvWriter.write(item.getSrcSys());
　　　　csvWriter.write(item.getSrcMod());
　　　　csvWriter.write(item.getBusiType());
　　　　csvWriter.write(item.getOptType());
　　　　csvWriter.write(item.getRecPk());
　　　　csvWriter.write(item.getUpdateId() + "");
　　　　csvWriter.write(item.getContent());
        //通过发送记录分隔符来结束当前记录。
　　　　csvWriter.endRecord();
　　}
　　csvWriter.close();
　　fos.close();
}
```