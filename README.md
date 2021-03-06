# Flat-Lattice-Transformer
论文代码  ACL 2020 paper: FLAT: Chinese NER Using Flat-Lattice Transformer

Models and results can be found at our ACL 2020 paper [FLAT: Chinese NER Using Flat-Lattice Transformer](https://arxiv.org/pdf/2004.11795.pdf).


# 运行环境:

```
Python: 3.7.3
PyTorch: 1.2.0
FastNLP: 0.5.0
Numpy: 1.16.4
fitlog: 0.9.13
注意FastNLP 0.5.0有一个小bug, 225-228行，使用cpu的时候，会报错，简单注释掉，或改一下
lib/python3.7/site-packages/fastNLP/core/utils.py
    # else:
    #     if not torch.cuda.is_available() and (
    #             device != 'cpu' or (isinstance(device, torch.device) and device.type != 'cpu')):
    #         raise ValueError("There is no usable gpu. set `device` as `cpu` or `None`.")
       改成这个，或注释掉
        if not torch.cuda.is_available() and ((isinstance(device, str) and device!='cpu') or
         (isinstance(device, torch.device) and device.type != 'cpu')):
            raise ValueError("There is no usable gpu. set `device` as `cpu` or `None`.")
```

你可以在 [这里](https://fastnlp.readthedocs.io/zh/latest/) 深入了解 FastNLP 这个库.

#目录结构
```buildoutcfg
V0 $ tree
.
├── add_lattice.py
├── cache      #生成的数据集的缓存，可以删除
├── data
│   ├── WeiboNER                #下载数据集放这里
│   │   ├── dev.conll
│   │   ├── test.conll
│   │   └── train.conll
│   ├── ctb.50d.vec                             #一些词嵌入，暂时放着，在paths.py中指定修改位置
│   ├── gigaword_chn.all.a2b.bi.ite50.vec
│   ├── gigaword_chn.all.a2b.uni.ite50.vec
│   └── sgns.merge.word
├── flat_main.py
├── log             #存放生成的日志
├── models.py
├── modules.py
├── output
│   └── yangjie_word_char_mix.txt      #存储混合后的词向量，运行preprocess.py后生成
└── utils_.py

```

# 微博数据集下载
http://download.fastnlp.top/dataset/weibo_NER.zip

# MSRA数据集
http://download.fastnlp.top/dataset/MSRA_NER.zip

# 数据集格式说明,BIO标注法,参照V0/data/WeiboNER/test.conll
```buildoutcfg
一	O                       #一 表示这个字，O表示的是这是一个负样本
节	O
课	O
的	O
时	O
间	O
真	O
心	O
感	O
动	O
了	O
李	B-PER.NAM       #李是"李开复"的第一个字，， B-PER.NAM 是人名的类型，开头
开	I-PER.NAM
复	I-PER.NAM
感	O
动	O                   

# 每条数据用空格分隔
xxxxx
xxxxx
xxxxx
```

如何运行？
====
1. 请下载预训练的embedding

      从[Google Drive](https://drive.google.com/file/d/1_Zlf0OAZKVdydk7loUpkzD2KPEotUE8u/view?usp=sharing) 或 [Baidu Pan](https://pan.baidu.com/s/1pLO6T9D) 下载字和 Bigram 的 embedding (gigaword_chn.all.a2b.{'uni' or 'bi'}.ite50.vec) 

      从[Google Drive](https://drive.google.com/file/d/1K_lG3FlXTgOOf8aQ4brR9g3R40qi1Chv/view?usp=sharing) 或 [Baidu Pan](https://pan.baidu.com/s/1pLO6T9D) 下载词的 embedding (ctb.50d.vec)(yj)
      
      从[Baidu Pan](https://pan.baidu.com/s/1luy-GlTdqqvJ3j-A4FcIOw) 下载词的embedding (sgns.merge.bigram.bz2)(ls)

```buildoutcfg
embedding 文件说明
joint4.all.b10c1.2h.iter17.mbichar
joint4.all.b10c1.2h.iter17.pmodel       
joint4.all.b10c1.2h.iter17.mchar
sgns.merge.word.bz2   --> 解压出
gigaword_chn.all.a2b.uni.ite50.vec       # unigram 的单词embedding
gigaword_chn.all.a2b.bi.ite50.vec        # bigram 的单词embedding
ctb.50d.vec                              # 50维度的embedding
ctb.bilstm.joint4.model                   # bilstm 模型
```

2. 修改 `paths.py` 来添加预训练的 embedding 和你的数据集
3. 运行下面的代码进行预处理

V0和V1的区别是：V0没有用bert，V1使用了bert
```
# 合并2个词向量字典，合并成一个词向量字典
python preprocess.py (add '--clip_msra' if you need to train FLAT on MSRA NER dataset)

# 选择一个你要运行的版本
cd V0 (without Bert) / V1 (with Bert)

# 运行
python flat_main.py --dataset <dataset_name> (ontonotes, msra, weibo or resume)
# 使用CPU运行
python flat_main.py  --dataset weibo --device cpu
```

如果你想方便地记录和观察实验结果, 你可以使用fitlog:
```
pip install fitlog
fitlog init V0
cd V0
fitlog log logs
```
然后把flat_main.py里的 use_fitlog 设置为 True 就行
你可以在 [这里](https://fitlog.readthedocs.io/zh/latest/) 深入了解 Fitlog 这个工具


引用: 
========
[bibtex](https://www.aclweb.org/anthology/2020.acl-main.611.bib)

