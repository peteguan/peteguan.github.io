---
layout: post
title:  "How to test internet bandwidth with command line"
date:   2018-02-24
excerpt: "There are multiple tools we could use to test the internet bandwidth at home. What if a server?"
image: "/images/speedtest/7085753952.png"
---

In China, PC users could install applications like <i>tencent PC manager</i> to test downloading and uploading speed. However, Mac users and Linux server administrators might want to know their bandwidth in specific scenes. If you have a screen and a web browser, just click <i>GO</i> on [speedtest.org](http://www.speedtest.net/) home page could be the easiest way. If you don't, no worries, a command line tool [<i><b>speedtest-cli</b></i>](https://github.com/sivel/speedtest-cli.wiki.git) will help you.

### 3 ways to install

This tool which works with Python 2.4 ~ 3.7 could be installed on Windows, Mac OSX and Linux. What you need to do is using <i>pip</i> command,
```
pip install speedtest-cli
```
Or <i>clone</i> the whole project from <i>Github</i>,
```
git clone https://github.com/sivel/speedtest-cli.git
python speedtest-cli/setup.py install
```
If you haven't install commands <i>pip</i> or <i>git</i>, the traditional installation way is effected as well.
```
wget -O speedtest-cli https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py
chmod +x speedtest-cli
```
### start to test
Extremely easy. Input <i>speedtest-cli</i> into the terminal,
![speedtest-cli](/images/speedtest/2018-02-24_14h35_35.png){:height="80%" width="80%" .center-image}
