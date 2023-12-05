---
title: 利用Java导入Excel到数据库
date: 2023-12-5 14:43:39
tags: Java
---

### 使用stream api进行分页
```java
List<User> list = new ArrayList<>();
List<User> subList = list.stream().skip((pageNo-1)*pageSize).limit(pageSize).collect(Collectors.toList());
```
pageNo 为当前页数，pageSize为每页大小
### 配合JPA使用样例
```java
public Object summaryList(IkgPage<BidMarginSummaryDTO> page) {  
        if (page == null || page.getPageSize() == -1) {  
            //不分页全量查询  
            if(null == page){  
                page = new IkgPage<BidMarginSummaryDTO>();  
            }  
            page.setPageNumber(1);  
            page.setPageSize(Integer.MAX_VALUE);  
        }  
//        Map<String, Object> param = page.getParams();  
//        if (param != null) {  
//  
//        }  
        List<BidMarginSummaryDTO> summaryDTOList = new ArrayList<>();  
        List<Map<String, Object>> maps = bidmarginrecoveryRepo.getSummary();  
        for (Map<String, Object> map : maps) {  
            BidMarginSummaryDTO dto = new BidMarginSummaryDTO();  
            dto.setProjectId((String) map.get("projectId"));  
            dto.setCmpName((String) map.get("collectionUnit"));  
            dto.setNextDate((String) map.get("nextDate"));  
            double margin = (double) map.get("margin");  
            double currentAmount = (double) map.get("currentAmount");  
            double actualAmount = (double) map.get("actualAmount");  
            // 保证金  
            dto.setApplyAmount(margin);  
            // 保证金申请的实交金额  
            dto.setActualAmount(actualAmount);  
            // 已回收金额  
            dto.setRecoveryAmount(currentAmount);  
            // 未回收金额  
            dto.setNotRecoveryAmount(margin-currentAmount);  
            summaryDTOList.add(dto);  
        }  
        int total = summaryDTOList.size();  
        int pageSize = page.getPageSize();  
        int pageNo = page.getPageNumber();  
//        int pageSum = (total -1) / pageSize +1;  
        page.setTotal(total);  
        page.setRows(summaryDTOList.stream().skip((long) (pageNo - 1) *pageSize).limit(pageSize).collect(Collectors.toList()));  
        return page;  
    }
```
[参考链接](https://blog.csdn.net/liuxiao723846/article/details/116210660)
