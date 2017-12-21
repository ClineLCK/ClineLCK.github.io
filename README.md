# ClineLCK.github.io



ClineChen







#!/bin/sh

app=eims
javaPath="/mnt/osservice/jre_1.7/bin/java"
java=java
pidfile=$app".pid"
rm -f $pidfile
touch $pidfile
echo "start $app service"
ulimit -c unlimited
ulimit -n 10240
# set java path
#export PATH=$PATH:/mnt/osservice/jre_1.7/bin/
# if exist javaPath then set java to this javaPath
if [ -n $javaPath ]; then
   echo "set javaPath: $javaPath "
   java=$javaPath
fi

nohup $java -server -Djava.ext.dirs=lib -Xms512m -Xmx512m -Xmn384m -XX:+HeapDumpOnOutOfMemoryError -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -jar $app.jar --spring.profiles.active=prod --server.port=18010 > start_log.txt  2>&1  &
sleep 1
running=`ps -p $! | grep -v "PID TTY" | wc -l`
if [ $running -gt 0 ];then
    echo $! > $pidfile
    echo "$app started..., pid=$!"
else
    echo "$app failed to start."
    exit 1
fi




#!/bin/bash

app=eims
pid=`ps x|grep $app |grep -v "get $app Pid" |grep -v grep |awk '{print $1}'`
echo "$app stop pid: $pid"
echo $pid |while read p
do
if [ "X$p" = "X" ]
    then
        echo "$app was not running."
    else
        echo "kill -9 $p"
        kill -9 $p 2>/dev/null
	echo "$app stop success"
fi
done




