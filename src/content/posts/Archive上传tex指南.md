---

title: Archive上传tex指南

published: 2024-10-24

description: 'rt'

image: ''

tags: [tutorial]

category: 'reasearch'

draft: false 

---

1. 通过submit下载tex压缩包
2. 在logs and output files → other ~ → Download output.bbl
3. 将output.bbl的内容放到main.tex的末尾，删去之前的参考文献
4. 将所有子目录上提到跟目录，对应修改引用
5. main中插入 \pdfoutput=1