# Redis 02

## 1、Linux环境配置

1. 下载压缩包

   ```
   wget http://download.redis.io/releases/redis-6.0.5.tar.gz
   ```

2. 解压文件夹

   ```
   tar xzf redis-6.0.5.tar.gz
   ```

3. 安装

   ```
   make install
   ```

4. 使用

   ```
   redis-server
   redis-cli
   ```

5. 更改端口号

   ```
   redis-server --port 6380
   redis-cli -p 6380
   ```

## 2、持久化

- 概念：利用永久性存储介质将数据进行保存，在特定的时间将保存的数据进行恢复的工作机制称为持久化。
- 作用：防止数据的意外丢失，确保数据安全性。
- 保存形式：
  1. 将当前数据状态进行保存，快照形式，存储数据结果，存储格式简单，关注点在数据
  2. 将数据的操作过程进行保存，日志形式，存储操作过程，存储格式复杂，关注点在数据的操作

![image-20200626145853598](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626145853598.png)

### 2.1 RDB 启动方式

- 命令

  ```
  save
  ```

- 作用：手动执行一次保存操作



![image-20200626150332329](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626150332329.png)

![image-20200626150726672](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626150726672.png)

- 命令（建议使用）

  ```
  bgsave
  ```

- 作用：手动启动后台保存操作，但不是立即执行

![image-20200626151158781](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626151158781.png)

![image-20200626151428657](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626151428657.png)

![image-20200626153417423](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626153417423.png)

![image-20200626154229588](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626154229588.png)

![image-20200626154327073](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626154327073.png)

![image-20200626154411713](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200626154411713.png)

### 2.2 AOF