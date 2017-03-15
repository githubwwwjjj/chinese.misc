# Welcome to chinese.misc
# 中文文本分析方便工具R包chinese.misc的中文说明
#
# 更了更了，现在的版本是0.1.3 ！！！！
#
# by: Wu Jiang (吴江)，微信号：theblackriver
# 
# 本文地址：
# https://github.com/githubwwwjjj/chinese.misc
# 
# 重要通知：0.1.3版的chinese.misc已更新，请通过install.packages('chinese.misc')来安装。这一版解决了由于tm包近期升级带来的生成中文dtm时乱码的情况，只要你的文件能正常读出来，那么在生成dtm时就不会乱码。同时，也提高了读文件时应对乱码的能力。另外，增加了计算词语关联和话题增减趋势的函数。
# 
# ################
# 一、使用方法
# ################
#
# 在Windows下的 R >=3.3.2 中，键入
```R
install.packages('chinese.misc')
library(chinese.misc)
```
# 同时，为了完成本文所举的例子，还需加载以下包
```R
library(tm)
library(jiebaR)
```
#
# ################
# 二、本R包的特点
# ################
# 编写本R包的目的主要是帮助使用者以相对自动和便捷的方式完成中文文本分析中一系列繁琐且易出错的工作；特别是帮助对文本分析的流程不太了解的初学者。
# 特点如下：
#  （1）能够仅用一行代码生成文档-词项矩阵，并在此过程中完成自动检测编码并读取文件、中文文本分词、定制性的文本清理、去除停用词等工作。
# （2）常用的tm包在用于中文文本时会有些恼人的bug；而chinese.misc包为此进行了一些改进，因此更适合处理中文。
# （3）通过一些方法宽容用户的不正确输入，尝试纠正错误，或在报错时给出一般人能读得懂的报错信息。频频报错飘红什么的实在是太心烦了；错误少，大家心情都舒畅。
# （4）提供了若干在中文文本分析以外的任务中也能使用的函数，如类型判断、类型转化。
#
# ################
# 三、函数概览
# ################
# （一）用于自动读取中文文本、分词、生成语料或文档-词项矩阵：
#
# corp_or_dtm
# scancn
# seg_file
# dir_or_file
#
# （二）用于去除停用词或无意义词语：
#
# make_stoplist
# slim_text
#
#
# （三）用于txt/rtf和csv互转，方便那些偏好某种格式文件的童鞋：
#
# csv2txt
# txt2csv
#
#
# （四）类型判断、类型转化
#
# is_character_vector
# is_positive_integer
# as.character2
# as.numeric2
#
#
# （五）查看、统计词频、查看词语关联
# output_dtm
# sort_tf
# word_cor (0.1.3版加入)
#
# （六）其它函数
# topic_trend (0.1.3版加入)
# tf2doc
# m2doc
# match_pattern
#
# ————以下将会分别介绍这些函数————
#
# ################
# 四、函数介绍
# ################
#
# 为进行讲解，首先需要提供几段文本做例子。
# 复制并运行以下代码（不用修改），会在你的工作目录里生成名为hehe的文件夹。如果这个文件夹之前已经在你电脑中存在了，会报错，请你自己改一个别的名。
# 程序会自动在这个文件夹里写入几个文件当样例。
#
```R
a_new='hehe' #If you want to change folder name, just change this.
gwd=getwd()
f=paste(gsub('/$', '', gwd), a_new, sep='/')
if (dir.exists(f)) stop ('Folder already exists. Please change a name.')
dir.create(f)
dir.create(paste(f, 'f1', sep='/'))
dir.create(paste(f, 'f2', sep='/'))
x='以事件为选题的数据新闻最常出现在重大新闻事件的报道中。在这类事件中，数据报道可能是媒体精心制作的报道主体，也可能是媒体对事件的整个专题报道中的一个有机组成部分。可预见的重大新闻事件一般多指会议、活动、庆典或赛事，媒体可以把较为充足的时间投入到选题策划中。除了可预见的重大新闻事件以外，更多此类数据新闻的选题是突发新闻事件。近年来，越来越多的媒体将数据新闻运用于突发新闻事件的报道中，大量数据资源的整合和运用为此类新闻报道增添了更多科学性。'
write.table(x, paste(f, 'f1/d1.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='UTF-8')
x='人们对数据可视化的适用范围有着不同观点。例如，有专家认为数据可视化是可视化的一个子类目，主要处理统计图形、抽象的地理信息或概念型的空间数据。现代的主流观点将数据可视化看成传统的科学可视化和信息可视化的泛称，即处理对象可以是任意数据类型、任意数据特性，以及异构异质数据的组合。大数据时代的数据复杂性更高，如数据的流模式获取、非结构化、语义的多重性等。'
write.table(x, paste(f, 'f1/d2.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='UTF-8')
x='政治传播学是政治学与传播学的交叉学科，它是对政治传播现象的总结和政治传播规律的探索和运用，它包括政治传播的结构、功能、本质及技巧等方方面面。它的研究范围包括：政治传播行为，即政治传播的主体、客体及他们之间的相互关系体系；政治传播内容，即对政治的信息处理体系；政治传播途径，即政治符号和传播媒介体系；政治传播环境，即政治传播与相关社会现象；政治传播形态，即政治传播本体的形貌或表现体系。'
write.table(x, paste(f, 'f2/d3.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='GB18030')
 x='改进社会治理方式。坚持系统治理，加强党委领导，发挥政府主导作用，鼓励和支持社会各方面参与，实现政府治理和社会自我调节、居民自治良性互动。坚持依法治理，加强法治保障，运用法治思维和法治方式化解社会矛盾。坚持综合治理，强化道德约束，规范社会行为，调节利益关系，协调社会关系，解决社会问题。坚持源头治理，标本兼治、重在治本，以网格化管理、社会化服务为方向，健全基层综合服务管理平台，及时反映和协调人民群众各方面各层次利益诉求。'
write.table(x, paste(f, 'f2/d4.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='GB18030')
 x='所有这三种活动和它们的相应境况都与人存在的最一般状况相关：出生和死亡，诞生性和有死性。劳动不仅确保了个体生存，而且保证了类生命的延续。工作和它的产物——人造物品，为有死者生活的空虚无益和人寿的短促易逝赋予了一种持久长存的尺度。而行动，就它致力于政治体的创建和维护而言，为记忆，即为历史创造了条件。'
write.table(x, paste(f, 'd5.txt', sep='/'), row.names=FALSE, col.names=FALSE, quote=FALSE, fileEncoding='UTF-8')
```
#
# （一）收集文件名、读文件
#  
# ######      dir_or_file
#
# 现在你已经有了一个装了五个文件的文件夹f。
# 用dir(f)可以收集这个文件里的文件名
# 然而，dir只能下到文件夹的一层，怎样才能读到嵌套在子文件夹里的文件呢，这时候就要用chinese.misc包里的dir_or_file了。
# 这个函数允许你同时输入多个文件夹或文件的名称，两者混着往里放也没事；这些放进去的名字，既可以是绝对路径，也可以是相对路径，也可能是以~开头的路径。
# 无论如何，这个函数会判断文件是否存在（如果不存在则会报错），去掉重复的文件，然后把从小到大放了序的全部文件名收集起来。
# 假如你要分析的文件在不同的文件夹里，甚至有的在C盘，有的在F盘，有的在工作目录里，有的不在，有的是文件夹的名称，有的是文件的名称，那么就可以用这个函式来整理和收集你的文件名了。
```R
dir_or_file (
    ...,  #一个或多个代表文件夹/文件名
    special = "" #代表模式的正则表达式或字符
) 
```
# 其中，...最好是字符或字符向量，但如果是一个由文件名组成的矩阵、列表什么的，也OK，反正程序会尝试进行转化。
# special代表你想要的文件的模式，默认是收集所有文件，但如果你只想要以txt结果的文件，就设special='txt$'。
#
# 举个栗子
```R
all_file=dir_or_file(f, special='txt$')
all_file
#看看是不是收集到了所有文件
#如果你的文件名有重复，也没事，反正最后会去重的
all_file=dir_or_file(f, f, special='txt$')
```
#
# ######      scancn
#
# 好的，现在我们已经收集好了文件名，接下来我们要读它们了。
# 我们要用chinese.misc包中名为scancn的函数。
# 它实际上就是一个scan函数，但是它可以自动检测文件编码，减少乱码的可能性，判断文件是否有实质内容，去除一些非法字符，并最后把文件拼接成单一字符。
# 这个函数永远不会输出0长度的对象或NA。因此，如果你把它读出来的结果传递给其它函数，就是相对安全的；如果你的文本里边只有空格或标点，也会被视为无实质内容。当文本无实质内容时，程序会继续运行，不会报错，但会弹出提示。
# 当然，你用这个函数读英文文本也行，只是英文文本大多不会乱码，所以不需要特意用这个函数。
```R
scancn(
    x, #一个文件名
    enc = "auto", #编码，默认为自动
    read_2nd = TRUE #是否尝试读取两次
)
```
# 其中，enc是你的文件的编码。如果你的很多文件有不同的编码，或者你不知道编码，或者你已经被乱码搞得头大了，就不用改了，让它自动检测吧。如果你确认你的文件就是某种编码，那就直接设一个值就行了，可省下几微秒的检测时间。
# read_2nd询问的是，是否要对被视为UTF-8文件但实际上并不是且无法正常读取的文件进行二次读取。默认为TRUE，基本上不用您改。
# 不要用scancn来读.csv/.xls/.xlsx、.doc/.docx、.pdf/.caj文件，它不是用来读这些的！它可以读的包括.txt、.rtf、.R、.py等等是普通文本的文件，以及没有后缀名的文件。
#
# 写个循环，依次读取五个文件，看能不能读出来。
```R
for (each in all_file){
    message(each)
    text=scancn(each)
    print(text)
}
```
#
# 都打印在你的屏幕上了吧！实际上第一、第二个文件都是UTF-8编码，第三、四个文件是GB18030编码，而scancn会主动对编码进行检测。
#
# ######      seg_file
# 中文文本分析的一个突出特点就是需要分词。当然，分词也很简单，用jiebaR包先设个分词器，再segment( )就行了。但是，我们可以用chinese.misc包里的seg_file来做的，这个包也是调用jiebaR来分词，但是更适合懒人使用。偷懒不需要借口！！！
#
```R
seg_file(
    ..., #一个或多个文件/文件夹名
    from = "dir",  #你的输入同文件/文件夹名，还是直接就是要处理的文本
    folder = NULL,  #写入分词结果的文件夹
    mycutter = DEFAULT_cutter,  #分词器
    enc = "auto",  # 编码
    myfun1 = NULL,  #定制函数1
    myfun2 = NULL,  #定制函数2
    special = "",  #文件名需符合何种模式
    ext = "txt" #若写入分词结果，以什么为后缀
)
```
# 看着参数有点儿多是吧。没事，复制粘贴一下上述代码就行了，有用的参数就改一下，不用改或不会改的放着不管就行了。其实真正需要你自己设的也没多少。
# 其中，...是字符向量，这个必须是字符向量，而不能像dir_or_file那样可以随便放什么东西。
# from是要告诉程序你这些字符是文件或文件夹的名子呢，还是说这些字符本身就是你要分词的文本。默认为'dir'，也就是文件/文件夹名，如果输入本身就是文本，务必要改成from='v'，否则程序会把输入当文件名处理，然后显然就要报错了。
# 如果你要把分词的结果写到硬盘上，请给folder一个有效的文件夹名。这个文件夹可能已经存在，但也可能不存在，如果不存在的话，程序会尝试创建一个，而且可以创建多层文件夹，但也可能创建失败。如果你不想写到硬盘上，只要设成NULL就行了。当然，默认值就是NULL，不用麻烦您设了。
# mycutter是函数使用的分词器，请加载jiebaR包生成。如果你不设它的话，也会使用一个预先存在的默认分词器，这个分词器其实就是DEFAULT_cutter=worker(write=FALSE)。所以如果你不是想为分词器添加新词的话，这一项也不用麻烦您设了。
# enc是编码，你可以指定一个文件编码，但也可以让它默设为'auto'，即自动检测。
# myfun1的值应该是一个函数，这个函数将会对被scancn读取出来，但尚未被分词的文本进行处理。默认是NULL，也就是不做处理。
# myfun2也需指向一个函数，只是这个函数将会被用来对分词之后的文本进行处理。当然你要这个定制函数的结果是一个长度为1的、词与词之间用空格相连的字符向量；如果不是字符向量的话，程序会尝试进行转换。
# special在读取文件时（也就是当from='dir'）时，只有符合这个模式的文件才会被读
# ext指的是，如果你要把分词的结果写到硬盘上，文件要什么后缀名。这个只能设成"txt"、 "rtf"或""。否则，会报错。
#
# 看几个栗子，用来证明真心不需要您亲自设定很多参数。
#
```R
y=seg_file(all_file) #输入是文件，但最后结果不写入硬盘
seg_file(all_file, folder='hehe/seg') #输入是文件，最后写进硬盘，同时在hehe文件夹里新创新了一个文件夹。
 all_text=unlist(lapply(all_file, scancn))
y=seg_file(all_text, from='v') #all_text已是是待处理的文本向量了，而不是文件名。最后不写入。
seg_file(all_text, from='v', folder='hehe/seg2') #直接处理文本，最后写入文件夹，同时创新一个seg2文件夹。
```
#
#
# ######      corp_or_dtm
#
# 不是说好了一步出文档-词语矩阵么，为啥前边讲什么收集文本、分词之类的？好吧，现在该轮到讲这个一步生成的函数了。灯灯灯等~~
# 
```R
corp_or_dtm(
    ...,  #一个或多个文件/文件夹名，或待处理的文本
    from = "dir", #你的输入是文件/文件夹名，还是待处理的文本
    type = "corpus", 
    enc = "auto", 
    mycutter = DEFAULT_cutter, 
    stop_word = NULL, 
    stop_pattern = NULL, 
    control = DEFAULT_control1, 
    myfun1 = NULL, 
    myfun2 = NULL, 
    special = ""
) 
```
# ...必须是多个字符，不能像dir_or_file里那样可以是各种对象，因为在corp_or_dtm里不会进行强制转换。（主要是考虑到程序跑的速度！！！）
# from是要告诉程序你这些字符是文件或文件夹的名子呢，还是说这些字符本身就是你要分词的文本。默认为'dir'，也就是文件/文件夹名，如果输入本身就是文本，务必要改成from='v'，否则程序会把输入当文件名处理，然后显然就要报错了。
# type，你最后想让程序输出什么？设成c, corp, 任何以C或c开头的，都是在告诉程度你最后要的是Corpus对象；如果是以D或d开头的，就是文档-词项矩阵；如果是以T或t开头的，就是词语-文档矩阵；如果是其它的值，也会生成Corpus。默认是生成Corpus。当然，我猜您最想要的是dtm吧，还麻烦您手动改一下。
# mycutter与在seg_file中不同，现在它既可能是函数使用的分词器，也可能是NULL。请加载jiebaR包生成。如果你不设它的话，也会使用一个预先存在的默认分词器。如果你不是想为分词器添加新词的话，这一项也不用麻烦您设了。如果是NULL的话，就是不进行分词，这适用于你的文本已经分过词而不用再分词的情况。
# enc是编码，你可以指定一个文件编码，但也可以让它默认为'auto'，即自动检测。
# stop_word需设定一个字符向量作为停用词。默设为NULL，即不去除。当然，大家可能比较懒，又想去除停用词，又懒得去找停用词表，就算有也懒得加载。嗯，好的，没事，只要设stop_word='jiebar'或'auto'就行！！！这样就直接使用jiebaR的停用词了。
# stop_pattern可能光去除停用词还不够，你还想去除具有某种模式的所有词，那么可在这里设个正则表达式向量。默认是NULL，多数情况上不用管，反正大家基本上也不用这种方法去除停用词。
# control是制作DTM/TDM时用的参数，必而是一个带原素名的列表。但这个参数与tm包中DocumentTermMatrix函数所使用的control是不同的，也不会传递给它，这一点请注意，这是0.1.3版的重要变化。可接受的原素包括：wordLengths（词语长度，最小值是1，不能设成0，最大值随便，但不能写成inf）；have（对一个词出现在多少篇文章中进行限制，最小是1，最大任意。设成c(2,50)，代表至少在2篇文章中出现，至多在50篇文章中出现）；bounds（一个词在全部文本中共出现多少回，最小是1，最大随意）；dictionary（如果你只希望生成一个只包含你想要的词的DTM，请用此参数指向一个字符向量，注意，如果这个参数存在，它会覆盖掉wordLengths；而如果你所指定的词实际上一个都不存在，最后的结果会是一个以“NA”为词语的全零对象；weighting（权重计算公式，大家可以自己写算法，但我觉得基本上大家用tm包提供的就行了，分别是tm::weightTfIdf、tm::weightBin，如果只是要看词频，就不用设了，默认就行了）。
# 你可以自己设control，不过不设也行，反正会指向一个默认值DEFAULT_control1，或者'auto'，或者'auto1'，所以这个参数也不用您管了。DEFAULT_control1是自动生成的，你把control设成NULL，也仍然会指向它；不过你也可以改成DEFAULT_control2，或设成'auto2'，也是另一个可以设的值，允许最小长度是2。两者设定的词语最大长度都是25。
# myfun1的值应该是一个函数，这个函数将会对被scancn读取出来，但尚未被分词的文本进行处理。默认是NULL，也就是不做处理。
# myfun2也需指向一个函数，只是这个函数将会被用来对分词之后的文本进行处理。当然你要这个定制函数的结果是一个长度为1的、词与词之间用空格相连的字符向量；如果不是字符向量的话，程序会尝试进行转换。
# special在读取文件时（也就是当from='dir'）时，只有符合这个模式的文件才会被读
#
# 好的，还是举栗子吧。需要您动手设的参数真的很少。
# 程序运行过程中，会弹出提示告诉你机器现在做什么。
```R
dtm=corp_or_dtm(all_file, type='dtm', stop_word='jiebar')
mycorp=corp_or_dtm(all_text, from='v', stop_word=c('关系', '数据'))
dtm=corp_or_dtm(all_text, from='v', type='tdm', control='auto2')
```
#
# （二）去除停用词
# ######      make_stoplist
#
```R
make_stoplist(x='jiebar', print=TRUE)
```
#
# 你是不是觉得还得去找停用词表太麻烦，好的，没事，用make_stoplist('jiebar')或直接make_stoplist( )什么都不加，就可以直接调用jiebaR的停用词了。print是指要不要在读取后把前5个词打印在屏幕上方便你查看是否读取成功，默认是TRUE。
# 如果你想用自己的停用词文件，就输入x，即文件名，就可以读取了。理想情况是你有个TXT文件，其中每个词语占一行。但事实上，可能你的停用词表没那么规范，比如：
# ------机制，大力、加强,努力, 全面6确保7 完成/实现|实现\n
# ------全党 NA 不断\t
# ------全国\人民
# ------促进999持续
# ------1不要2更要3既要4六要5
# 那怎么办呢？没事，make_stoplist会尽量帮你整理成一个干净的停用词表。完成后，会把最靠前的几个词打印在屏幕上帮你确认读取成功。
# 如果你的文件里有重复的词也没事，因为会自动去重。
# 至于文件的编码，可自动检测。
#
# ######      slim_text
#
# 用停用词表时，会发现为了去掉更多无实质意义的词，你不得不弄一个很长的停用词表，在你想到某个词并把它加到表里之前，你就没法去除它。那怎么办呢？没事，我们通过给词语标注词性来把无意义的词去掉。
#
```R
slim_text(
	x, #字符
	mycutter = DEFAULT_cutter, 分词器
	rm_place = TRUE, #是否删去跟地点相关的，默认为是
	rm_time = TRUE,  #是否删去跟时间相关的，默认为是
	rm_eng = FALSE,  #是否删去含英文，默认为否
	rm_alpha = FALSE, #是否去除字母，默认为否，可以有FALSE, TRUE, "any"三个选项
	paste = TRUE #是否把结果拼接成一个字符，默认为是
)

# 要说明的是，有的词虽然只包含字母，但是并不会被标注为eng，所以不能通过rm_eng去掉，此时可通过rm_alpha=TRUE来去掉，且一旦如此，rm_eng的值会被无视。而如果用rm_alpha="any"，那么一个词只要包含一个英文字母，比如“A股”，就会被去掉。
#
```
# 这样就能达到大量削减词语数的目的了。
```R
 x='所有这三种活动和它们的相应境况都与人存在的最一般状况相关：出生和死亡，诞生性和有死性。劳动不仅确保了个体生存，而且保证了类生命的延续。工作和它的产物——人造物品，为有死者生活的空虚无益和人寿的短促易逝赋予了一种持久长存的尺度。而行动，就它致力于政治体的创建和维护而言，为记忆，即为历史创造了条件。'
 #先看直接分词的效果
 seg_file(x, from='v')
 #再看用词性来删词的效果
 slim_text(x)
 # 词语数减少四分之一的目的应该可以达到
```
#
# （三）文件格式互转
#
# 有些童鞋喜欢把文本全都放到一个能够用EXCEL打开的表格里；而我则喜欢让每个文本单独占一个文件，这两种格式各有利弊。所以，如果你的文本的格式不合你意，可以用以下两个函数来转换。
#
# ######      txt2csv

```R
txt2csv(
	..., #一个或多个文件夹名或文件名
	csv, #你要把形成表格的文本写到什么csv文件里
	must_txt = TRUE,  #是否要读取的必须是txt文件
	na_in_txt = NULL # 你的独立文件里的什么内容会被视为缺失值
) 
```
#
# 参数csv必须是个可创建的csv文件，即，必须以csv结尾，不要尝试xls/xlsx ！
# must_txt表示是否只读取txt文件，默认为是。如果你要读其它文件，请设为FALSE。
# na_in_txt是指，被scancn读取的哪些内容会视为无实质内容。无论如何，" "也就是一个空格，肯定会被视为缺失，但是你还可以加上其它值。比如，如果你的文本是网页，而有的网页返回的是"404 ERROR"的话，你可以把这个算成缺失。
#
# #举个例子，把几个文件写到CSV里
```R
f1_and_f2=paste(f, c('f1', 'f2'), sep='/')
txt2csv(f1_and_f2, csv=paste(f, 'iamcsv.csv', sep='/'))
```
# 成功写入后，会发现表格中第一列为完整路径名，第二列为内容。假如原文件没有实质内容，会写入NA。
#
#
# ######      csv2txt
# 
# 真的，txt难道不是打开最省事的文件么。喜欢txt的童鞋可以方便地用这个函数把写到表格里的文本转成txt。
# 这个函数的特点是会自动为每个文件的文件名添加一个唯一的序列号。比如你有1234个文件，那么由于1234是四位数，所以会给文件加上0001, 0002, 0012, 0348, 1022这样的号码，而不是1, 2, 12, 348, 1022。这样是为了保证在任何软件上都能以同样的顺序出现在你眼前。另外，加序列号也是因为，用户指定的那列字符可能含有WINDOWS不可接受的文件名符号，比如英文冒号，这会导致你指定的文件名被完全或部分修改，从而出现相同的文件名（其实就算不对文件名进行修改，那列数据仍然可能有相同的单元格），因而必须用唯一数值来区分。
#
```R
csv2txt(
	csv, #你要读的CSV文件
	folder, #你要把分开来的文本写到什么文件夹里
	which, #CSV里哪一列是文本，务必看以下说明
	header = TRUE, #CSV是否有表头
	row.names = NULL, #CSV是否有行标题
	na_in_csv = c(NA, "", " ", "?", "NA", "999"), #CSV表格里哪些值被视为缺失值 
	na_in_txt = " ", #若表格中的文本被视为缺失，在独立文本中被写成什么
	name_col = NULL, #CSV表格哪一列可用作文件名
	ext = "txt" #独立文件是什么格式
 )
```
#
# folder必须是个有效文件夹名，如果它不存在，程序会尝试创建它
# header和row.names这两个是要传递给read.csv的，这个大家都知道。不过在现在这个函数里row.names只能有两个值，要么是NULL，表示无行标题，要么是1，表示第1行是行标题，这意味着你的CSV表格必须也是这样的。
# which和name_col必须注意。假如你的CSV是在第1列有行标题的，第2列是文件名，第3列是文本，那么，实际上你的表格只有两列，因为第1列是行标题，因此name_col应该埴1（尽管它在CSV中是第2列），which应该是2（尽管它在CSV中是第3列）。如果没有行标题，那么name_col是2，which是3。
# na_in_txt是指，你的表格的单元格中的什么值会被视为缺失值。无论如何，"", " ", "?", "NA", "999"是无论你怎么设置都会被当成缺失的，但是你还可以往上加其它值，比如"0"、"no content"什么的。
# na_in_txt假如你的那个单元格是缺失，那么当写入独立文本时写入什么，默认是一个空格。
# ext是独立文本的格式，只能是"txt"、"rtf"和""（即无后缀）三个值；如果是其它的无道值，会被自动设成""。
#
#
# （四）类型判断、类型转化
#
# ######      is_character_vector
#
```R
is_character_vector(
	x, 
	len = NULL, 
	allow_all_na = TRUE
)
```
# 这个函数用来判断对象是不是一个字符向量，且具有一定长度；is.character是最常用的判断是否是字符的函数，但是即使是对一个由字符组成的矩阵，它也仍然返回TRUE，因此并不能判断对象是否是一个向量。不过最重要的是它用来判断一个值是否是有效的值。所谓有效，就是指这个一个值被传给其它函数处理时，不会发生错误。比如说，is.character(character(0))的结果是TRUE，但是character(0)对很多函数来说并不是一个有效的参数值。所以is_character_vector重在判断一个值是否是有效的。
# 对于0长度的对象，它肯定返回FALSE；
# 对于不是向量的对象，肯定返回FALSE；
# 对于只有一项且是NA的对象，无论它是NA，还是character(NA)，都是FALSE；
# 对于有很多项，但每个字符都是NA的对象，结果取决于参数allow_all_na的值，如果是TRUE，即允许，则返回TRUE，否则就是NA；
# 必须满足指定的长度。如果len=NULL，则是不限长度；假如len=c(2,10)，则表示要么是2，要么是10；如果len=c(2:10)，则表示2至10均可。
#
# 多举些例子吧：
```R
is_character_vector(character(0))
is_character_vector(NA)
is_character_vector(as.character(NA))
is_character_vector(c(NA, NA))
is_character_vector(as.character(c(NA,NA)))
is_character_vector(as.character(c(NA, NA)), allow_all_na = FALSE)
is_character_vector(as.character(c(NA, NA)), allow_all_na = TRUE)
is_character_vector(matrix(c("a", "b", "c", "d"), nr = 2))
is_character_vector(c("a", "b", "c"), len = c(1, 10))
is_character_vector(c("a", "b", "c"), len = c(1:10))
```
#
# ######      is_positive_integer
# 
# 这个函数用来判断对象是否是一个由有效正整数组成的符合一定长度的向量。什么叫”有效“呢？比如说3.0，这个数你用is.integer来判断，就是FALSE，但是其实它完全可以被当成一个正整数来使，因为就是有效的。
#
```R
is_positive_integer (x, len = NULL)
```
# 
# 如果对象长度为0，则返回FALSE，因为is.integer(integer(0))是TRUE，但是is_positive_integer(integer(0))就是FALSE；
# 如果是混杂的，比如c(1, 2.0)这样的，也是TRUE；
# 不允许有任何NA，否则判断为FALSE，这一定与is_character_integer不同；
# 必须符合由len指定的长度，默认为NULL，即不限长度。 
#
# 还是举例吧:
```R
is_positive_integer(NULL)
is_positive_integer(as.integer(NA))
is_positive_integer(integer(0))
is_positive_integer(3.0)
is_positive_integer(3.3)
is_positive_integer(-1:5)
is_positive_integer(1:5, len = c(2, 10))
is_positive_integer(1:5, len = c(2:10))
```
#
# ######   as.character2
#
```R
as.character2(...)
```
# 是的，这是个增强版的as.character，它可一次转化多个对象，但更重要的是它的转化能力比as.character更强。
# 可以转化列表，以及嵌套列表，即列表的列表，列表的列表的列表……
# 可以转化数据框；
# 可以转化在屏幕上显示为字符的因子，以及放在列表中或数据框中的因子，你在屏幕上看见的是什么样，转化出来就是什么样。
# 对于NULL，会返回character(0)，而不会返回NULL或'NULL'，这样在传给其它函数时会相对安全些。
#
# 上例子吧:
```R
as.character2(NULL, NULL)
null_list <- list(a = NULL, b = NULL, c = NULL)
as.character2(null_list)
# 列表套数据框
df <- data.frame(matrix(c(66,77,NA,99), nr = 2))
l <- list(a = 1:4, b = factor(c(10,20,NA, 30)), c = c('x', 'y', NA, 'z'), d = df)
as.character2(l)
l2 <- list(l, l, cha = c('a', 'b', 'c'))
as.character2(l2)
```
#
# ######      as.numeric2(...)
#
# 这个是as.numeric的升级版。
# 与as.numeric不同的是，它能将在屏幕上显示为数值的因子转化成你看到的样子。而且它也像as.character2一样可用来处理列表、数据框。
#
# 上例子：
```R
a <- c(55, 66, 77, 88, 66, 77, 88)
b <- factor(a)
df <- data.frame(a, b)
as.numeric2(df, a*2)
l <- list(a, a*2)
as.numeric2(l)
l2 <- list(l, l)
as.numeric2(l2)
```
# 
#（五）查看、统计词频
#
# ######      output_dtm 
# DTM对象是不能直接查看的，也不可能直接写到硬盘上，我们用一个简单的函数来转化一下。
```R
output_dtm(
    x, 
    outputfile = NULL, 
    doc_name = NULL
) 
```
#
# x是DTM或TDM对象。
# output_file默认是NULL，即生成矩阵，不写文件，或者，应该是一个CSV文件的名称，也就是在将DTM/TDM转化成矩阵后写到什么文件里。
# doc_name，当转化成矩阵或写入文件时，给每个文本加一个名字。默认是NULL，即不加，此时就是1, 2,3...。或者，你也可以放一个长度与文件数相同的字符向量。
#
# 举例：
```R
x <- c(
  "Hello, what do you want to drink?", 
  "drink a bottle of milk", 
  "drink a cup of coffee", 
  "drink some water")
dtm <- corp_or_dtm(x, from = "v", type = "dtm")
m <- output_dtm(dtm, doc_name = paste("doc", 1:4))
```
#
#
# ######      sort_tf
#
```R
sort_tf (
	x, 
	top = 10, 
	type = "dtm", 
	todf = FALSE, 
	must_exact = FALSE
)
```
# x是DTM或TDM对象；但是也可以是matrix。如果是matrix，请务必用type指明你的这个是"dtm"还是"tdm"，分别用以”D/d“开头的，或以”T/t“开头的代表就行了。
# top是显示最靠前的多少个词，默认是10。所有词被加总后会按词频从高到底排序，top用来指明要看前多少个词。不过假如你指定看前5个词，第5个词的词频是20，但还有两个词的词频也是20，因此此时函数会显示7个词。如果你真的就只要5个词，那么就把must_exact设成TRUE就行了，默认是FALSE。
# todf是指是否转化成数据框。默认是FALSE，即不输入任何结果，而是直接把词语和主词频用&连起来，打印到屏幕上。这样，你就可以复制粘贴到EXCEL之类的软件上了。如果是TRUE，则回返回由词语和词频组成的数据框。
#
# 举例：
```R
x <- c(
 "Hello, what do you want to drink?", 
  "drink a bottle of milk", 
  "drink a cup of coffee", 
  "drink some water", 
  "hello, drink a cup of coffee")
dtm <- corp_or_dtm(x, from = "v", type = "dtm")
sort_tf(dtm, top = 5)
```
#
#
# ######      word_cor
#
```R
word_cor(
	x,  #DTM/TDM，或matrix
	word,  #你要查询相关性的词，每次最多30个
	type = "dtm", #如果x是matrix，用来指明它代表dtm还是tdm，分别用以”D/d“开头的或以”T/t“开头的就行了
	method = "kendall",  #何种系数，kendall, spearman, pearson
	p = NULL, #设一个p值，只有当一个相关系数的p值小于此值时，系数才会显示，否则会是NA
	min = NULL #设一个最小的系数值，只有大于等于此值的系数才会被显示，否则是NA
	)
```
#
# 这个函数与tm包中的findAssocs计算的差不多，但是更为灵活。第一，findAssocs只能计算pearson系数。但是对于像词频这种极少呈正态分布的数据来说，更适合用等级相关系数，因此word_cor可以让你指定是用pearson，还是spearman还是kendall。默认是kendall。第二，这个函数直接把相关系数表及P值给出来，这更符合我们平时做研究风需要。第三，这个函数允许通过系数限制和P值限制显示。
#
```R
dtm=corp_or_dtm(all_file, type='dtm', stop_word='jiebar')
word_cor(dtm, word=c('新闻', '数据', '可视化'))
word_cor(dtm, word=c('新闻', '数据', '可视化'), p=0.5)
```
#
#
# （六）其它函数
#
# ######     topic_trend
#
```$
topic_trend(
	year, #每个话题所在的年份
	topic,  #每个话题的类别
	relative = FALSE, #是计算相对数据还是绝对数据
	zero = 0 #如果某一年某个话题一次都没出现，是算NA上是0，默认是0
	)
```
# 这个函数的应用场景是，你用各种算法已经确定了第个文本的话题，同时也知道这个文本出现的哪一年，于是想算不同的话题在这些年的变化趋势。这个函数本质上就是lm( )一下而已，反映的只是大体上的增减趋势。若用绝对数据，即默认的relative=FALSE，就是拿每年的数量直接计算；如果是相对数据，就是先拿这一年这个话题的数量除以全年文章总和，用这个百分比计算。year参数要求至少要有三个时间点，比如2014:2016，仅两个是不行的。
# zero的值只能是NA或0。但是如果relative为TRUE的话，那么会直接将所有NA都看成0来计算。
# 返回结果是一个列表，依次是趋势数据，历年话题的数量汇总，以及历年话题的百分比汇总（如果relative=TRUE）。其中，趋势数据有以下几项，trendIndex就是斜率啦；trendLevel就是斜率的p值；totalTrend是依据以上两个值对趋势进行的判断，如果回归系数大于0，就是rise，反之就是fall，如果p值小于0.05，则是significant rise或significant fall；maxminYear，如果是rise就看最大值出现在哪年，如果是fall就看最小值出现在哪年；detailTrend当为rise时，若最后一年是最大值，则为rise along，否则为rise and fall，当为fall时，若最后一年为最小值，则为fall along，否则为fall and rise；simpleTrend就是看最后一年与第一年的大小，大，就是rise，反之为fall，相等就是equal。
# 有两种不能计算的特殊情况会在结果中反映出来（但不会报错，所以不用担心）。一种是只有一两个时间点可用，也就是说，一个话题只在一两年中出现过，这种情况下，结果会显示为"less than 3y"以及999。另一种情况是确实每年那个话题的数量都没有变化，此时结果会显示为0、1，以及”almost same“，这些都很容易理解。更详细的说明可以看英文手册。
# 看例子
```R
year=sample(2010: 2016, 200, replace=TRUE)
topic=sample(c('art', 'law', 'philosophy', 'literature', 'history', 'sociology'), 200, replace=TRUE)
topic_trend(year, topic)
```
#
#
# ######      match_pattern
#
```R
match_pattern (pattern, where, vec_result = TRUE)
```
#
# 这个函数就是简单地提取包含某个模式的字符。
# pattern为要满足的模式。
# where为要在什么字符里提取。
# vec_result为是否将结果转化成向量输出，默认是TRUE。如果是FALSE的话，那么where有几项，就会生成一个有几项的列表，每一列代表where中的一项的匹配结果。
#
# 举例：
```R
p <- "x.*?y"
x <- c("x6yx8y", "x10yx30y", "aaaaaa", NA, "x00y")
y <- match_pattern(p, x)
y <- match_pattern(p, x, vec_result = FALSE)
```
#
# ######      tf2doc
#
# 有时你已经把一个DTM转化成矩阵了，但是某个函数却需要输入DTM对象，或者，有的软件需要你输入一段文本。这时就可以用tf2doc来还原出一个已经分好词且每个词之间用空格相连的文本了，也可能还会将它再弄成语料或DTM。这个函数其实就是封装一下rep而已。
#
```R
tf2doc (term, num)
```
# term是词语向量，但不是的话也没关系，反正程序会强行转化，所以放个什么数据框、列表、矩阵之类的进来都没问题。num是与term等长的整数向量，但是若不是向量的话，程序也会尝试自动转化。
# term中若有缺失值，"NA"这个词会被重复。但是term中不能有缺失值。
#
# 举例：
```R
# 一个是矩阵，一个是因子，都没问题
x <- matrix(c("coffee", "milk", "tea", "cola"), nrow = 2)
y <- factor(c(5:8))
```
#
# ######      m2doc
#
# 这个是把数值矩阵转化成文件。它把矩阵当成DTM（不能是TDM），把列标题当词语，表格的每行代表一个文件。如果没有列标题，会自动用term1, term2...代替。
#
```R
m2doc (m, checks = FALSE)
```
# m是数值矩阵。checks是是否检测m是正常输入，包括是否是数值，是否均是正数，是否无NA。为加快速度，可设为不检测，即FALSE，这也是默认值。
#
# 举例：
```R
s <- sample(1:5, 20, replace = TRUE)
m <- matrix(s, nrow = 5)
colnames(m) <- c("r", "text", "mining", "data")
m2doc(m)
```
#
# ============================
# End
