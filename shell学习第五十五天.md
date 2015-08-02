## shell 学习五十五天----进程记账

**linux 进程调度的实现 ---- 进程记账**

linux 进程调度的实现一共由四部分组成：

1. 时间记账 (就是记录进程已经运行了多长时间，还要运行多长时间)
2. 进程选择 (加入红黑树)
3. 调度器入口
4. 睡眠和唤醒
 
进程记账：就是记录一个进程占用处理器资源的时间长度。既然要记录，那么就需要存在在一个位置，内核说，放在 `sched_entity` 结构中吧。我们当然要听内核的....
 
至于 `sched_entity` 这个结构的源码我就不贴了，但是里面有一个 `vruntime` 成员变量，这个成员变量可看成 `virtual run time`，正确名字是虚拟实时。

这个 `vruntime` 变量就是用来记录进程已经运行的时间长短，或者说，占用处理器已经多长时间了。那么这个事件越长就越证明运行的时间越长，就越容易被其他的进程挤掉，也就是，其他进程容易抢占进来。linux 系统对于进城的调度室要看这个变量来采取行动的。那么这个虚拟实时 (时间值) 怎么计算出来的?(这个计算过程就是进程记账的过程)，内核使用 `update_curr()` 函数来实现这个过程。

**源码**：

```static void update_curr(struct cfs_rq *cfs_rq)
{
        struct <span style="color：#FF0000；">sched_entity *curr</span> = cfs_rq-\>curr；
        u64 <span style="color：#FF0000；">now</span> = rq_of(cfs_rq)-\>clock_task；
        unsigned long <span style="color：#FF0000；">delta_exec</span>；
        if (unlikely(!curr))
                return；
        /*
         * Get the amount of time the current task was running
         * since the last time we changed load (this cannot
         * overflow on 32 bits)：
         */
       <span style="color：#FF0000；"> delta_exec = (unsigned long)(now - curr->exec_start)；</span>
        if (!delta_exec)
                return；
        <span style="color：#FF0000；">__update_curr(cfs_rq，curr，delta_exec)；</span>
        curr->exec_start = now；
        if (entity_is_task(curr)) {
                struct task_struct *curtask = task_of(curr)；
                trace_sched_stat_runtime(curtask，delta_exec，curr->vruntime)；
                cpuacct_charge(curtask，delta_exec)；
                account_group_exec_runtime(curtask，delta_exec)；
        }
        account_cfs_rq_runtime(cfs_rq，delta_exec)；
}
```
 
这个 `update_curr` 函数中存在这么几个变量和函数。`curr` 是一个 `sched_entity` 结构指针，接收函数传进的参数。函数参数那个结构是什么? 搞不明白，反正就是吧其中的一个成员复制 `curr` 变量。看名字可以知道是指进程当前的状态 (当然，包括虚拟实时)。接着定义了一个无符号长整型变量 `delta_exec`，查一下 `delta` 有增量的意思，而 `exec` 是执行，那么从名字上看就是值执行的增量 (就是时间的增量)。然后，看到 `delta_exec` 被赋值为 `now` 和 `exec_start` 的差值，`exec_start` 这个可以知道这时开始，现在减开始的，那就是已经运行的时间。最后执行`__update_curr` 函数将当前的进程账本，和已经运行的时间作为参数传如，先来看一下`__update_curr()` 这个函数：

```static inline void 
__update_curr(struct cfs_rq *cfs_rq，struct <span style="color：\#FF0000；">sched_entity *curr</span>，
              unsigned long <span style="color：#FF0000；">delta_exec</span>)
{
        unsigned long delta_exec_weighted；
        schedstat_set(curr->statistics.exec_max，
                      max((u64)delta_exec，curr->statistics.exec_max))；
        curr->sum_exec_runtime += delta_exec；
        schedstat_add(cfs_rq，exec_clock，delta_exec)；
<span style="color：#ff0000；">        delta_exec_weighted = calc_delta_fair(delta_exec，curr)；</span><span style="color：#ff00；">
        </span><span style="color：#ff0000；">curr->vruntime += delta_exec_weighted；</span>
        update_min_vruntime(cfs_rq)；
\#if defined CONFIG_SMP && defined CONFIG_FAIR_GROUP_SCHED
        cfs_rq->load_unacc_exec_time += delta_exec；
\#endif
}
```

分析一下：传入的参数 `curr`，`delta_exec`，执行了 `calc_delta_fair`，从名字入手，这是计算，计算什么? 增量，还是公平的，那就是加入了权重，这样计算出了权重值。接着在 `vruntime` 上加上权重，这就是进程运行的时间，记账完成。