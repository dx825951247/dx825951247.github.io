---
title: Oracle创建自增长字段函数
date: 2017-10-17 17:19:54
categories: Oracle
---
一.创建序列号
<!--more-->
``` xml
create sequence SEQ_VEN_CODE
minvalue 1
maxvalue 999999999999
start with 74
increment by 1
nocache
cycle;
```
二.创建自增长函数
``` xml
CREATE OR REPLACE FUNCTION FUN_VEN_CODE(VEN_TYPE VARCHAR2) RETURN VARCHAR2 IS
  VEN_CODE VARCHAR2(30);
BEGIN
  IF VEN_TYPE = 'LOGISTICS' THEN
    SELECT 'L' || LTRIM(TO_CHAR(SYSDATE, 'yyyymmdd')) ||
           LPAD(SEQ_VEN_CODE.NEXTVAL, 5, '0')
      INTO VEN_CODE
      FROM DUAL;
  ELSIF VEN_TYPE = 'GOODS' THEN
    SELECT 'G' || LTRIM(TO_CHAR(SYSDATE, 'yyyymmdd')) ||
           LPAD(SEQ_VEN_CODE.NEXTVAL, 5, '0')
      INTO VEN_CODE
      FROM DUAL;
  END IF;
  RETURN TRIM(VEN_CODE);
EXCEPTION
  WHEN OTHERS THEN
    RETURN '0';
END;
```
三.查询

`select FUN_VEN_CODE('LOGISTICS') from dual`

结果：L2017101700072

`select FUN_VEN_CODE('GOODS') from dual`

结果：G2017101700073 






