## 定时提醒喝水



写了一个定时提醒喝水的脚本，每10分钟提醒一下



```
repeat
    display notification "🤡🤡🤡" with title "冬猫该喝水啦~" sound name "Morse"
    delay 600
end repeat
```

![image.png](https://i.loli.net/2021/02/03/oFV8T1mAReXqulj.png)



## 每日定时提醒



```
set datte to current date
set hower to get hours of datte
set minits to get minutes of datte
set tiime to hower & minits
set wun to item 1 of tiime
set twoo to item 2 of tiime
set tiime to wun & ":" & twoo as string

if tiime contains "15:49" then
    display alert "提醒~~~"
end if
```



## 参考文档



https://segmentfault.com/a/1190000011273388



## 其他



JavaScript化



https://cloud.tencent.com/developer/article/1009747

