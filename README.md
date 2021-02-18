# 3090 tensorboard1.15配置
# docker-nvidia-tensorflow

在使用CUDA 11 之後 ，tensorflow 1.x版本便不再支援 

但Nvidia有提供可以在docker 下運行的tensorflow 1.x 版本 所以有了這次的紀錄

可以參考docker 官網提供的安裝方式安裝 docker
https://docs.docker.com/engine/install/ubuntu/

之後為了使用顯示卡需要再Nvidia網站安裝 nvidia-docker讓顯卡能在docker環境使用(這邊會安裝docker用的cuda跟cudnn 電腦不需要另外裝)
可以參考tensoflow官方給的安裝建議
https://www.tensorflow.org/install/docker?hl=zh-tw
跟nvidia官方給的教學安裝
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker

# tensorflow 1.15 安裝
安裝好docker之後便是安裝tensorflow1.15

Nvidia將這些打包好的資料都放在 Nvidia NGC 上面

Nvidia NGC https://ngc.nvidia.com

登入後選擇containers並搜尋tensorflow

複製 pull commond

docker pull nvcr.io/nvidia/tensorflow:20.12-tf1-py3 

(":" 後面為版本號，根據顯卡driver不同選不同的版本，

https://docs.nvidia.com/deeplearning/frameworks/tensorflow-wheel-release-notes/tf-wheel-rel.html#rel_20-11 可查詢需求版本)

貼在CMD上面執行image安裝

安裝完畢

sudo docker images

檢查有沒有images安裝成功


# docker環境使用
接下來便是在docker下執行程式，網路上有很多打包成一個容器的使用教學，但這次學習docker只是做一些小測試

docker下的container就像一台虛擬電腦，要執行程式必須把要執行的程式丟進去

依照 nvidia跟tensorboard的教學

CMD 輸入

sudo nvidia-docker container run --rm -it nvcr.io/nvidia/tensorflow:20.12-tf1-py3

(因為裝20.12版所以後面序號長那樣)

但是這樣執行的時候是沒有任何程式可以用的，因為沒有將檔案放進去

需要一個 -v 來將檔案映射進docker操作

nvidia-tensorflow本身啟動之後會進入workspace這個默認資料夾

所以需要將要操作的資料應設進去這個資料夾裡

於是我先在home下面創建一個testfile資料夾並把要操作的檔案丟進去

那環境啟動方式就是

sudo nvidia-docker container run --rm -it -v /home/avlab/testfile:/workspace nvcr.io/nvidia/tensorflow:20.12-tf1-py3

啟動後輸入 ls 看看當前資料夾內容

可以看到有testfile資料夾

cd testfile 後便可以如在一班情況進行操作(如pip install)

(要注意只使用官方給的imagey作為container時，任何的套件操作都會在關掉這次的工作後被刪除，下次要使用需重新安裝，故推薦去看一些製作專用container的教學)


# 一些小問題
1.
docker運行python的時候有機率發生

ImportError: libGL.so.1: cannot open shared object file: No such file or directory

使用

apt update

apt install libgl1-mesa-glx

可解決

2.
使用docker對資料夾操作的時候，會因為使用者不同而導致docker操作期間的新資料有權限的問題

可以簡單的在有權限問題的資料夾中使用

sudo chown "使用者" "檔案名稱"/ -R

例如

sudo chown zx113355 output/ -R

便可正常操作output資料夾的資料
