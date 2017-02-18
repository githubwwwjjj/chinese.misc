# chinese.misc
# 中文文本分析方便工具R包chinese.misc的中文说明
# 
# 本使用说明目前尚未完成，预计将于2017年4月以前完成。
# This Chinese manual has not been finished. It will be finished before Apr 2017.
# 
# ################
# 一、使用方法
# ################
# # 在Windows下的 R >=3.3.2 中，键入
# install.packages('chinese.misc')
# librarry(chinese.misc)
#
# ################
# 二、本R包的特点
# ################
# 编写本R包的目的主要是帮助使用者以相对自动和便捷的方式完成中文文本分析中一系列繁琐且易出错的工作；特别是帮助对文本分析的流程不太了解的初学者。
# 特点如下：
#  1、能够仅用一行代码生成文档-词项矩阵，并在此过程中完成自动检测编码并读取文件、中文文本分词、定制性的文本清理、去除停用词等工作。
# 2、通过一些方法宽容用户的不正确输入，尝试纠正错误，或在报错时给出一般人能读得懂的报错信息。错误少，大家心情舒畅，初学者的沮丧情绪也会少些。
# 3、提供了若干在中文文本分析以外的任务中也能使用的函数，如类型判断、类型转化。
#
# ################
# 三、函数概览
# ################
# （一）用于自动读取中文文本、分词、生成语料或文档-词项矩阵：
# corp_or_dtm
# scancn
# seg_file
# dir_or_file
#
# （二）用于去除停用词或无意义词语：
# make_stoplist
# slim_text
#
# （三）用于txt/rtf和csv互转，方便那些偏好某种格式文件的童鞋：
# csv2txt
# txt2csv
#
# （四）类型判断、类型转化
# is_character_vector
# is_positive_integer
# as.character2
# as.numeric2
#
# （五）查看、统计词频
# output_dtm
# sort_tf
#
# （六）其它函数
# tf2doc
# m2doc
# match_pattern
#
# ————以下我将会分别介绍这些函数————
























