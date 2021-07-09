---
title: word转pdf
date: 2021-05-22 12:14:07
tags:
  - python_pywin32模块
categories:
  - python
---

```
"""
作用：word批量转pdf
wordPath: wrod文档所在文件夹
pdfPath： paf存放文件夹
"""


import os
import datetime
from win32com.client import constants, gencache


def word_to_pdf(wordPath, pdfPath):

    if not os.path.exists(pdfPath):  # 判断存放PDF的文件夹是否存在
        os.makedirs(pdfPath)  # 若PDF文件夹不存在就创建

    i = 0
    for filename in os.listdir(wordPath):
        if os.path.splitext(filename)[1] == '.docx' or os.path.splitext(filename)[1] == '.doc':
            i += 1
            word_path = wordPath + '/' + filename
            pdf_path = pdfPath + '/' + os.path.splitext(filename)[0] + '.pdf'
            print('正在转换第 {} 个文件：\n    {}'.format(i, filename))
            try:
                word = gencache.EnsureDispatch('Word.Application')
                doc = word.Documents.Open(word_path, ReadOnly=1)
                doc.ExportAsFixedFormat(pdf_path,
                                        constants.wdExportFormatPDF,
                                        Item=constants.wdExportDocumentWithMarkup,
                                        CreateBookmarks=constants.wdExportCreateHeadingBookmarks)
            except Exception as e:
                print("转换异常，异常是：{}".format(e))
                word.Quit(constants.wdDoNotSaveChanges)
    print('本路径下一共有{}个WORD文件'.format(i))


if __name__ == '__main__':
    word_Path = r"C:\Users\dd\PycharmProjects\pythonProject1\doc"
    pdf_Path = r"C:\Users\dd\PycharmProjects\pythonProject1\pdf"
    word_to_pdf(word_Path, pdf_Path)
    print('-' * 40)
    print("转换结束")
```