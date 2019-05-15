# Lab 2: QoS Implementation with DPDK

## Parameter Deduction

### srTCM
* SINGLE RATE TRAFFIC CONTRACT
    * defined for a sub*rate connection over a physical link with maximum transmission rate of AR (Access Rate)
* main parameters:
    * Committed Information Rate (CIR)
    * Committed Burst Size (CBS)
    * Excessive Burst Size (EBS)
```
    fid     cir           cbs      ebs
    0    160000000000    80000    80000    
    1    80000000000     40000    40000    
    2    40000000000     20000    20000    
    3    20000000000     10000    10000    
```

* limit the flow 0 to 160000 bytes in every time period(1000000 ns) so bandwidth is about 1.28 Gbps.
* thr cbs == ebs means that we disperse bytes of IP packets evenly to two token buckets(green and yellow), and the exceed will be marked as red
* refill every token bucket everytime after a time period
* The 4 flows share total bandwidth in proportion of 8:4:2:1

### WRED
* Parameters
    * red_cfg	[in,out] config pointer to a RED configuration parameter structure
    * wq_log2	[in] log2 of the filter weight, valid range is: RTE_RED_WQ_LOG2_MIN <= wq_log2 <= * RTE_RED_WQ_LOG2_MAX
    * min_th	[in] queue minimum threshold in number of packets
    * max_th	[in] queue maximum threshold in number of packets
    * maxp_inv	[in] inverse maximum mark probability
```
    color    wq_log2    min_th    max_th    maxp_inv   
    GREEN       9        1022      1023      10    
    YELLOW      9        1022      1023      10    
    RED         9         0         1        10    
```
* enqueue as many green/yellow packets as possible
* drop all red packets.
* set `wq_log2` and `maxp_inv` to the value taht recommended in DPDK document.

## Used DPDK APIs

* `rte_panic()`: terminate program when fatal error happens and print error message

* `rte_meter_srtcm_config()`: srTCM configuration per metered traffic flow
* `rte_meter_srtcm_color_blind_check()`: srTCM color blind traffic metering

* `rte_red_config_init()`: Configures a single RED configuration parameter structure.
* `rte_red_rt_data_init()`: Initialises run-time data. 
* `rte_red_mark_queue_empty()`: Updates queue average in condition when queue is empty. 
* `rte_red_enqueue()`: Decides if new packet should be enqeued or dropped. Updates run time data based on new queue size value. Based on new queue average and RED configuration parameters gives verdict whether to enqueue or drop the packet.


