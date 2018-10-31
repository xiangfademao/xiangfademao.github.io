---
layout: post
title: "Binder ioctl命令"
date: 2018-04-27 10:34:34
comments: true
categories: 
---

## 摘抄自网络 ##   

 

命令 | 意思 
- | :-: | -: 
BINDER_VERSION | 获取Binder版本
BINDER_SET_MAX_THREADS | 设置接收线程池的线程数目  
BINDER_SET_CONTEXT_MGR | 设置Binder管理进程，对应的就是ServiceManager，handle为0 
BINDER_WRITE_READ     |注①:向Binder写入或读取数据，对应的数据结构为binder_write_read；



注释：   

 ① **BINDER_WRITE_READ:**  Binder写入或读取数据，对应的数据结构为binder_write_read；   
 参数分为两段：写部分和读部分。如果write_size不为0就先将write_buffer里的数据写入Binder；   
 如果read_size不为0再从Binder中读取数据存入read_buffer中。write_consumed和read_consumed表示操作完成时Binder驱动实际写入或读出的数据个数。   
 如果需要返回数据，那么read_size和read_buffer必须设置，BINDER_WRITE_READ命令关联的数据分为读写两个buffer，对应write_buffer和read_buffer，按照惯例，buffer头四个字节用于数据类型描述。
 这些数据类型以宏的形式定义，以BC或者BR开头：
 

 1. **(Writebuffer) BC_XXX，全称为Binder Driver Command Protocol， 是指App –>Binder Kernel命令**    
 2. **(Readbuffer) BR_XXX，全称为Binder Driver Return Protocol，是指BinderKernel –> App命令**

命令 | 意思 
- | :-: | -: 
BC_ENTER_LOOPER | 将当前线程设置为Binder主线程，它不计入接收线程池，必须设置
BC_REGISTER_LOOP | 将当前线程注册到接收线程池  
BC_EXIT_LOOP | 线程退出时，告知Binder将该线程从线程池中移除 
BR_SPAWN_LOOPER |当线程池无空闲线程并且线程池总数还未到最大值时，Binder Kernel会发送该命令告知Binder线程，线程快不够用了，请尽快开辟新线程并注册到Binder Kernel，以便能及时接收后续数据。
BC_TRANSACTION |业务事件，包含业务id以及序列化数据，App –> Kernel，所以，只有Binder Proxy才会发出该事件命令
BR_TRANSACTION|业务事件，包含业务id以及序列化数据，Kernel –> APP，所以，只有Native Binder才会收到该事件命令
BC_ACQUIRE|请求将handle所对应的binder node的强引用计数+1
BC_RELEASE|请求将handle所对应的binder node的强引用计数-1
BC_INCREFS|请求将handle所对应的binder node的弱引用计数+1
BC_DECREFS|请求将handle所对应的binder node的弱引用计数-1
BR_ACQUIRE|将关联 native binder的强引用计数+1
BR_RELEASE|将关联 native binder的强引用计数-1
BR_ATTEMPT_ACQUIRE|尝试将关联 native binder的强引用计数+1
BR_INCREFS|将关联native binder的弱引用计数+1
BR_DECREFS|将关联native binder的弱引用计数-1

