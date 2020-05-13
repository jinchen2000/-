# -
import psutil
from win32 import win32api
from win32 import win32console
import datetime
import time
import os

pids = []  # 常见PID
flag = 0  # 不存在异常


#################端口扫描################
def netpidport(pid: int):
    """根据pid寻找该进程对应的端口"""
    alist = []
    # 获取当前的网络连接信息
    net_con = psutil.net_connections()
    for con_info in net_con:
        if con_info.pid == pid:
            alist.append({pid: con_info.laddr.port})
    return alist


def netportpid(port: int):
    """根据端口寻找该进程对应的pid"""
    adict = {}
    # 获取当前的网络连接信息
    net_con = psutil.net_connections()
    for con_info in net_con:
        if con_info.laddr.port == port:
            adict[port] = con_info.pid
    return adict


def dealwrong(p):
    # 判断异常端
    for i in range(len(pids)):
        if p == pids[i]:
            return True
    return False


def scanport(count):
    dicts = {'port': '', 'pid': ''}
    for i in range(0, 10000):
        dicts = netportpid(i)
        if dicts:
            p = psutil.Process(dicts[i])  # 获取进程名
            print('Port Number:{:5s}   PID:{:5s}   Process Name:{}'.format(str(i), str(dicts[i]), p.name()))
            if count == 0:
                pids.append(dicts[i])
            else:
                if not dealwrong(dicts[i]):  # 存在异常进程
                    flag = 1
                    print("ERROR!!!\n EX pid:{} Process Name:{}".format(dicts[i], p.name()))


def scan():
    for i in range(2):  # 扫描次数
        print('-------Scan Times:{}---------'.format(i + 1))
        nowTime = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')  # 现在的时间
        print(nowTime)
        scanport(i)
        print('\n')


scan()
