---
layout: post
title: "GPU driver + CUDA + cudnn setting for AI"
author: "Eren"
tags: setting
---

Mondeique 는 극한의 기술력으로 모바일에서 피팅룸을 구현하고자 하는 비전을 가지고 있는 기업입니다. 그렇게 되기까지 우리 회사의 기술력인 AI 기술은 끊임없이 발전해야 합니다. 이에 따라서 본 포스트에서는 `AI 환경을 구축하기 위한 여러가지 setting` 에 관해서 서술하려고 합니다. 

<br>
제 개인 경험을 이야기하자면 2018년의 저는 정말 AI 초보였습니다. 처음 연세대학교 대학원 CVML Lab 에 들어갔을 당시 `Linux / ubuntu` 에 대한 기본 지식도 부족했고 이런 AI 학습을 위한 기본적인 setting 도 사실 혼자서 할 수 없었습니다. 그렇지만 직접 ubuntu 를 계속 깔아보고 CUDA 설정 하다가 버전이 맞지 않아서 포맷해보고 이런저런 ~~삽질~~ 경험을 해보며 얻었던 생생한 느낌들을 바탕으로 이번 **AI setting** 에 관한 글을 써보려고 합니다.
<br>

Windows 설정도 있지만 본 포스트에서는 Linux / Ubuntu 18.04 를 기준으로 서술하도록 하겠습니다. 순서 **1-3** 의 경우 이전 포스트인 [ubuntu 18.04 setting](https://mondeique.github.io/2019-08-16/ubuntu-18.04-setting) 를 참고해주세요.

1. Ubuntu 18.04 설치
2. python - Anaconda 3 conda env 설치
3. GPU drivers 설치
4. CUDA 설치
5. cuDNN 설치

<hr>
<br>
## CUDA 설치

CUDA 를 설치할 때 가장 중요한 것은 NVIDIA-drivers 와의 호환성입니다. 실제로 저희 Machine GPU 가 RTX 2070 super 였을 때 cuda 10.0 을 설치했었는데 호환이 되지 않는다는 사실을 알고... 시간을 날렸던 경험이 있었습니다. 일단 CUDA 설치를 한 번 해봅시다!  (본 post는 단순히 예시의 경우이고 cuda version에 따라 파일 명은 바뀔 수 있습니다)

{% highlight markdown %}
$ sudo apt-get install gcc

Download package. Linux/x86_64/Ubuntu/18.04/deb(local) https://developer.nvidia.com/cuda-10.1-download-archive

$ sudo dpkg -i cuda-repo-ubuntu1804–10–1-local-10.1.130–410.48_1.0–1_amd64.deb
$ sudo apt-key add /var/cuda-repo-10–1-local-10.1.130–410.48/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get install cuda

{% endhighlight %}

성공적으로 cuda 를 설치하셨다면 이제 `.bashrc` 에서 cuda 의 경로를 지정해주어야 합니다. 그렇지 않으면 CUDA 자체를 인식을 못하거든요! 

{% highlight markdown %}

$ gedit ~/.bashrc

export PATH=/usr/local/cuda-10.1/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}} 

{% endhighlight %}

환경변수까지 설정해주셨다면 이제 재부팅을 하고 nvidia version 을 확인해볼까요?

{% highlight markdown %}
$ sudo reboot
$ cat /proc/driver/nvidia/version
$ nvcc —version
{% endhighlight %}

![nvcc --version](/imgs/nvcc.png)

일단 CUDA 까지는 모두 설정했습니다.

<br>
## cuDNN 설치

CUDA를 설치했으니 이번엔 cuDNN 을 설치하도록 하겠습니다. cuDNN도 마찬가지로 CUDA version 과 꼭 맞는 library 를 설치해주셔야 합니다. [cuDNN 홈페이지](https://developer.nvidia.com/cudnn)에서 다운로드 받으실 수 있고 다운로드를 받으려면 회원가입을 해야한다는 사실 잊지마세요.

<br>
가장 먼저 다운로드 받은 cuDNN zip 파일을 unzip 시킵니다.
 {% highlight markdown %}
$ cd ~/Downloads/
$ sudo tar -xzvf cud-10.1-~~
{% endhighlight %}

그 다음 `sudo cp` command를 이용해서 `/usr/local/cuda`에 복사하도록 합시다. 최종적으로 아래의 command를 이용해 cuDNN이 잘 설치가 되어있는지 까지 확인하면 됩니다. 참고로 cuda 11.0 version 부터는 `cudnn.h` 파일이 아닌 `cudnn_version.h` 에서 확인할 수 있으니 이점 참고해주세요.
{% highlight markdown %}
$ cd cuda
$ sudo cp include/cudnn.h /usr/local/cuda/include
$ sudo cp lib64/libcudnn* /usr/local/cuda/lib64
$ sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
$ cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2 : 각각 7,6,2 가 뜨면 성공
{% endhighlight %}

이번 포스트에서는 NVIDIA-Driver version에 맞는 CUDA 와 cuDNN 을 설치하는 방법에 대해서 서술했었습니다. 아마 새로운 머신에 대해서 설치를 할 때 항상 따라다닐 문제이기 때문에 익숙해지면 훨씬 쉬울 것입니다. 저처럼 고생하지 말고 빠르게 setting 하고 개발하도록 합시다!!







