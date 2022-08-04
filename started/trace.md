链路追踪生成`Trace信息`，在发生请求后，这里会优先展示链路信息。

>[danger] 如果采样率为百分百时，发现前几次的请求不会被抓取到，这是因为客户端会获取相关应用ID等信息并生成缓存；

按天查询单个接口的`调用时间`、`耗时时长`及`完整的Trace信息`，快速定位耗时较长的调用。

![](images/watermark,type_d3F5LW1pY3JvaGVp,size_20,text_6K-G5rKD56eR5oqA54mI5p2D5omA5pyJ,color_FFFFFF,shadow_50,t_80,g_se,x_10,y_10-20190806145222698.png)

![](images/watermark,type_d3F5LW1pY3JvaGVp,size_20,text_6K-G5rKD56eR5oqA54mI5p2D5omA5pyJ,color_FFFFFF,shadow_50,t_80,g_se,x_10,y_10-20190806145238165.png)

可配合调试工具进行链路调试

![](images/20190806-145409.png)

## 调用栈

在 [Agent列表](agent-list.md) 中开启 PHP调用栈，会增加一个调用栈列表，会展示该条 trace 中的一些调用信息