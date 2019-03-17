# Lab1
# 张宇航 516030910273 huashan_hang@sjtu.edu.cn
*  目标： 实现单边 RDT protocal
    * on top of a link medium that can lose, reorder, and corrupt(乱码) packets
# 基础知识
### Go-Back-N
* 当接收方检测出失序的信息后，要求发送方重发最后一个正确接受的信息帧之后的所有未被确认的帧
* 特点：（GBN）复杂度低，但是不必要的帧会再重发，所以大幅度范围内使用的话效率是不高的

## Selective Repeat
* 发信侧不用等待收信侧的应答，持续的发送多个帧，假如发现已发送的帧中有错误发生，那么发信侧将只重新发送那个发生错误的帧。
* 特点：相对于GBN 复杂度高，但是不需要发送没必要的帧，所以效率高。
* 例：如果序列号有K bits，那么这个ARQ的协议大小为：2^(k-1)。

## Stop-and-Wait protocol
* 数据报文发送完成之后，发送方等待接收方的状态报告，如果状态报告报文发送成功，发送后续的数据报文，否则重传该报文。
* 发送窗口和接收窗口大小均为1
* 该方法所需要的缓冲存储空间最小，缺点是信道效率很低
## 16bit checksum
1.  先将需要计算checksum数据中的checksum设为0； 
2. 计算checksum的数据按2byte划分开来，每2byte组成一个16bit的值，如果最后有单个byte的数据，补一个byte的0组成2byte； 
3. 将所有的16bit值累加到一个32bit的值中； 
4. 将32bit值的高16bit与低16bit相加到一个新的32bit值中，若新的32bit值大于0Xffff, 
再将新值的高16bit与低16bit相加； 
5. 将上一步计算所得的16bit值按位取反，即得到checksum值，存入数据的checksum字段即可。
# Inplementation
* basic design: go-back-n
* optimization:
    two-level-buffer(massage buffer and packet window) in sender and one-level-buffer(packet window) in receiver
* packet layout:
    ```
    /* 7-byte header for packets of msg
     * |<-  2 byte  ->|<-  4 byte  ->|<-  1 byte  ->|<-             the rest            ->|
     * |<- checksum ->|<-seq number->| payload size |<-             payload             ->| 
     */
    ```
    * attention: when save massage to massage buffer, it will add 4 bytes size of massage after the header and before the metadata, which means that the header of the first packet of a message is 11 bytes.
* key procedure:
    * premise: receiver receive packet in order
    1. receiver get a packet and get the seq_num
    2. if seq_num < the expected one : ignore it and resend the biggest received seq_num 
    3. if seq_num > the expected one : save it to packet window to wait for use(**don't receive it**), won't send ack
    4. if seq_num = the expected one : receive the packet and find next packet in window until can't find it, then send the biggest received seq_num
    * other opimization : 
        * sender will dynamicly adjust the seq_num to be sended by the newest ack
        * receiver does not care whether the acks are successfully or sent and only save the same correct packet only once