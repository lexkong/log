## 简介

该日志包参考 [paas-lager](https://github.com/ServiceComb/paas-lager)，做了一些便捷性上的改动，功能完全一样，只不过该日志包更便捷些。

Go开发中常用的log包有：

+ log
+ glog
+ logrus
+ ...

`log`和`glog`比较简单，无法满足生产级的程序开发。`logrus`功能很强大，但缺少rotate功能，需要自己通过外部的程序来rotate日志文件。该日志包总结了企业开发中常用的需求，将这些功能整合在一个日志包中。经过测试该日志包性能完全可以满足企业级的生产需求。

## 使用方法

在使用log包前，需要先初始化log包，初始化函数有：`InitWithConfig()`, `InitWithFile()`。一个简单的example：

```
package main

import (
	"fmt"

	"github.com/lexkong/log"
	"github.com/lexkong/log/lager"
)

func main() {
	log.InitWithFile("log.yaml")

	for i := 0; i < 1; i++ {
		log.Infof("Hi %s, system is starting up ...", "paas-bot")
		log.Info("check-info", lager.Data{
			"info": "something",
		})

		log.Debug("check-info", lager.Data{
			"info": "something",
		})

		log.Warn("failed-to-do-somthing", lager.Data{
			"info": "something",
		})

		err := fmt.Errorf("This is an error")
		log.Error("failed-to-do-somthing", err)

		log.Info("shutting-down")
	}
}
```

log.yaml文件为：

```
writers: file,stdout
logger_level: DEBUG
logger_file: logs/log.log
log_format_text: false
rollingPolicy: size # size, daily
log_rotate_date: 1
log_rotate_size: 1
log_backup_count: 7
```

## 日志参数

+ `writers`: 输出位置，有2个可选项：file,stdout。选择file会将日志记录到`logger_file`指定的日志文件中，选择stdout会将日志输出到标准输出，当然也可以两者同时选择
+ `logger_level`: 日志级别，DEBUG, INFO, WARN, ERROR, FATAL
+ `logger_file`: 日志文件
+ `log_format_text`: 日志的输出格式，json或者plaintext，`true`会输出成json格式，`false`会输出成非json格式
+ `rollingPolicy`: rotate依据，可选的有：daily, size。如果选daily则根据天进行转存，如果是size则根据大小进行转存
+ `log_rotate_date`: rotate转存时间，配合`rollingPolicy: daily`使用
+ `log_rotate_size`: rotate转存大小，配合`rollingPolicy: size`使用
+ `log_backup_count`:当日志文件达到转存标准时，log系统会将该日志文件进行压缩备份，这里指定了备份文件的最大个数。
