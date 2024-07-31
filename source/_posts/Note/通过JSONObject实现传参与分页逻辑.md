---
title: 通过JSONObject实现传参与分页逻辑
date: 2024-07-31 15:52:59
tags: java
---

## 前言

在开发中，我们经常需要通过接口传递参数，并且需要分页。那么如何通过JSONObject实现传参与分页逻辑呢？

## 实现思路

1. 统一传参格式，用于实现分页的统一处理模板工具。
2. 实现用于JSONObject的分页工具。
3. 根据解析的参数进行分页查询。
4. sql方面的预处理问题。

## 代码实现

### 统一传参格式，用于实现分页的统一处理模板工具。

**传入参数**
```json
{
  "pageNumber": 1,
  "pageSize": 15,
  "params": {
    "name":""
  }
}
```
**返回参数**
```json
{
    "total": 4,
    "pageNumber": 1,
    "pageSize": 15,
    "params": {
        "name": ""
    },
    "rows": [{},{},{},{}]
}
```

### 实现用于JSONObject的分页工具。

```java 
import com.alibaba.fastjson.JSONObject;  
import java.util.List;  
  
/**  
 * @author obsidianlyg  
 */
 public class JsonPageUtil {  
  
    public static Integer toInteger(JSONObject params, String key) {  
        return params.getInteger(key);  
    }  
  
    public static String toString(JSONObject params, String key) {  
        String value = params.getString(key); 
        // 这里的判断主要为后面sql做准备 
        if (value == null || value.isEmpty()) {  
            value = null;  
        }        return value;  
    }  
  
    public static boolean toBoolean(JSONObject params, String key) {  
        boolean value = false;  
        try {  
            value = params.getBoolean(key);  
        } catch (Exception e) {  
            return false;  
        }        return value;  
    }  
  
    public static JSONObject toPage(JSONObject params, List<JSONObject> list, int pageNumber, int pageSize)  {  
        // 记录数据  
        JSONObject data = new JSONObject();  
        data.put("pageSize", pageSize);  
        data.put("params", params);  
  
        // 分页  
        // 记录总数  
        Integer count = list.size();  
        data.put("total", count);  
  
        // pageSize == -1 全量查找  
        if (pageSize == -1) {  
            data.put("pageNumber", pageNumber);  
            data.put("rows", list);  
            return data;  
        }  
        // 页数  
        Integer pageCount;  
        if (count % pageSize == 0) {  
            pageCount = count / pageSize;  
        } else {  
            pageCount = count / pageSize + 1;  
        }        // 开始索引  
        int fromIndex;  
        // 结束索引  
        int toIndex;  
        // 防止下标出现负数  
        pageNumber = Math.max(pageNumber - 1, 0);  
        // 更新返回值  
        data.put("pageNumber", pageNumber + 1);  
  
        if (!pageCount.equals(pageNumber+1)) {  
            fromIndex = pageNumber * pageSize;  
            toIndex = fromIndex + pageSize;  
            if(toIndex > count){  
                fromIndex = (pageNumber-1) * pageSize;  
                toIndex = count;  
            }  
        } else {  
            fromIndex = pageNumber * pageSize;  
            toIndex = count;  
        }  
        // list不能为空  
        if (list.isEmpty()) {  
            data.put("rows", null);  
            return data;  
        }  
  
        List<JSONObject> pageList = list.subList(fromIndex, toIndex);  
        data.put("rows", pageList);  
        return data;  
    }  
}
```

### 根据解析的参数进行分页查询。

```java
@Override  
public JSONObject pageList(JSONObject page) {  
    // 获取分页参数  
    Integer pageNumber = JsonPageUtil.toInteger(page, "pageNumber");  
    Integer pageSize = JsonPageUtil.toInteger(page, "pageSize");  
  
    // 获取过滤参数  
    JSONObject json = page.getJSONObject("params");  
    String name = JsonPageUtil.toString(json, "name");  
  
    List<JSONObject> list = contractpaymentRepo.pageList(name);  
  
    // 分页  
    return JsonPageUtil.toPage(json, list, pageNumber, pageSize);  
}
```
### sql方面的预处理问题
由于有些参数不一定是必填的，所以在sql语句中需要做预处理，比如name字段，如果传了name参数，则进行查询，如果没有传name参数，则不进行查询，直接查询所有数据。
当然这只是一个偷懒的写法，如果参数很多的话，还是建议使用mybatis的动态sql，这样会更清晰一些。

```sql
SELECT * FROM contractpayment WHERE
name = ifnull(#{name}, name)
```