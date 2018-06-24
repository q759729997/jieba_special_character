# jieba分词可以自定义词表和词库。但是目前版本尚不支持特殊字符（如空格等）。

## 参考github上的网友们的解答，总结修改方法如下：

- 修改目录（我的为windows系统，使用miniconda，路径供参考，具体则需要根据自己实际情况进行修改）：
~~~
文件路径 D:\ProgramData\Miniconda3\envs\python36\Lib\site-packages\jieba
~~~
- 修改内容

  - 修改jieba根目录 包括init(参考：jieba__init__.py)和词表(参考：jieba_dict.txt)
  - 修改posseg目录 包括init(参考：posseg__init__.py)
  - 参考网站 https://github.com/fxsjy/jieba/issues/423
~~~
打开默认词典（根目录）或自定义词典，把所有用来间隔词频和词性的空格间隔符改成@@
（选用@@是因为一般关键词里遇到这个分隔符的几率比较小吧）

继续，打开jieba根目录下init.py

搜索
re_han_default = re.compile("([\u4E00-\u9FD5a-zA-Z0-9+#&\._]+)", re.U)
改成
re_han_default = re.compile("(.+)", re.U)
搜索
re_userdict = re.compile('^(.+?)( [0-9]+)?( [a-z]+)?$', re.U)
改成
re_userdict = re.compile('^(.+?)(\u0040\u0040[0-9]+)?(\u0040\u0040[a-z]+)?$', re.U)
搜索
word, freq = line.split(' ')[:2]
改成
word, freq = line.split('\u0040\u0040')[:2]
补充：若用的全模式继续改。
搜索
re_han_cut_all = re.compile("([\u4E00-\u9FD5]+)", re.U)
改成
re_han_cut_all = re.compile("(.+)", re.U)
~~~

## 测试代码
~~~
import jieba
import jieba.posseg as pseg

jieba.add_word('奥迪Q7',tag='car_type')
jieba.add_word('A3 e-tron',tag='car_type')
jieba.add_word('奥迪R8',tag='car_type')

line = '奥迪Q7 e-tron是奥迪系列的车。A3 e-tron也是车。奥迪R8是另一个车。'
line_txt_list = []
words = pseg.cut(line)
for word, flag in words:
    line_txt_list.append('%s %s' % (word, flag))
print('|||'.join(line_txt_list))
~~~
##测试结果
~~~
奥迪Q7 car_type|||  x|||e eng|||- x|||tron eng|||是 v|||奥迪 nz|||系列 q|||的 uj|||车 n|||。 x|||A3 e-tron car_type|||也 d|||是 v|||车 n|||。 x|||奥迪R8 car_type|||是 v|||另 r|||一个 m|||车 n|||。 x
~~~





