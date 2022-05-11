### 配置参数解释
 | 配置块 | 名称 |名称 |
 | ----  | ---- | ---- |
 |  http  |   proxy_cache_path   |  指定缓冲区的路径
 |  |   levels  |   缓存目录级最高三层，每层1-2个字符表示，比如：1:1:2 三层
 |   |keys_zone  |   缓存块名称及内存块大小，比如：cache_item:500m表示声明一个名为cache_item大小为500m，超出大小后最早的被清除
  |   |max_size  |   缓存区硬盘的最大值，超出闲置数据将被清除
 |   |inactive  |   最长闲置时间，比如：10d ，表示一个数据被闲置10天则将被清除
 |location	|  proxy_cache| 指定缓冲区，对应keys_zone中的设定的值
 |  |proxy_cache_key | 	通过参数拼装参数key如$host$uri$is_args$agrs;
 |  |proxy_cache_valid |对不同的状态码设置缓存有效期 例：any 10m;
