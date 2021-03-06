
# 大学基本情報データをみる

[大学改革支援・学位授与機構](http://www.niad.ac.jp)の
ウェブサイト上で公開されている
[大学基本情報](http://portal.niad.ac.jp/ptrt/table.html)
データを取り込み、Let's visualize!

今回は「学生数に対する教員数」の多寡が「休学者の割合」と相関しているか、調べてみます。

## Download

[H27年度データ](http://portal.niad.ac.jp/ptrt/h27.html)
  ページのHTMLソースを確認し、公開されているExcelファイルをシェルから以下のように一括でダウンロードします。

```
curl -s http://portal.niad.ac.jp/ptrt/h27.html |
  sed -n 's/^.*=\"\(.*\.xls\).*$/http:\/\/portal\.niad\.ac\.jp\/ptrt\/\1/p' |
  xargs -n1 curl -O
```

## Library


以下Rを用います。
[R for Data Science](http://r4ds.had.co.nz)
を参考に処理をおこなっていくので、
[tidyverse](http://tidyverse.org)
と
[readxl](https://cran.r-project.org/web/packages/readxl/index.html)
を読み込みます。

```R
library(tidyverse)
library(readxl)
```

インストールされていない場合は
<code>install.packages("tidyverse")</code>
としてください。readxlはtidyverseに含まれます。


また、のちにggplot2でプロットする際に日本語が文字化けしないように、以下を記載しておきます。（いまいち理解していませんが・・・）
（参考：
[ggplot2(奥村晴彦先生)](https://oku.edu.mie-u.ac.jp/~okumura/stat/ggplot2.html)
）

```R
old = theme_set(theme_gray(base_family="HiraKakuProN-W3"))
```

## Import


<code>read_excel()</code>を使用します。
公開されているexcelファイルは一見、見出し行でセル結合をしていて扱いにくそうですが、
解析しやすいように非表示の見出し行があります。こういう気遣いは助かりますね。

<pre><code>#(7-A) 学生数
students = read_excel(path = "2015_07go_a.xls",skip = 4)
#(7-B) 教員数（本務者）
teachers = read_excel(path = "2015_07go_B.xls",skip = 3)
#(8-2) 学科別学生数のうち休学者数
absense = read_excel(path = "2015_08go_2.xls",skip = 3)
</code></pre>

## Transform


<pre><code>#NAを0に置換
students[is.na(students)] <- 0
teachers[is.na(teachers)] <- 0
absense[is.na(absense)] <- 0

sub_students <- students %>%
  filter(昼夜別=="昼間") %>%
  mutate(本科_計 = 本科_男 + 本科_女) %>%
  select(学校調査番号,大学,本科_計)
colnames(sub_students)[3] <- "学生数"

sub_teachers <- teachers %>%
  filter(D列 == "計") %>%
  select(学校調査番号,計_計)
colnames(sub_teachers)[2] <- "教員数"

sub_absense <- absense %>%
  filter(昼夜別== 1) %>%
  mutate(休学計 = 計_男 + 計_女) %>%
  select(学校調査番号,休学計) %>%
  group_by(学校調査番号) %>%
  summarise(休学者数 = sum(休学計))

students_teachers <- inner_join(sub_students,sub_teachers,"学校調査番号") %>%
  mutate("学生数/教員数" = 学生数/教員数)

students_teachers_absense <- inner_join(students_teachers,sub_absense,"学校調査番号") %>%
  mutate("休学者数/学生数" = 休学者数/学生数) %>%
  filter(学生数 > 500)</code></pre>


最後、学生数 > 500 でフィルターをかけています。
学生数が少なすぎると、休学者率(休学者数/学生数)の変動が大きくなりすぎる、という理由ですが、
500という数字に根拠はありません。こういう時、どう判断すれば良いんでしょうか。


## Visualize


<pre><code>c <- cor.test(students_teachers_absense$`学生数/教員数`,students_teachers_absense$`休学者数/学生数`)
label1 <- paste("Pearson.R=",signif(c$estimate))
label2 <- paste("p-value=",signif(c$p.value))

ggplot(data=students_teachers_absense,mapping = aes(x=学生数/教員数, y = 休学者数/学生数,family = "HiraKakuProN-W3")) +
  geom_point() +
  geom_label(data = filter(students_teachers_absense,学生数/教員数 > 30 | 休学者数/学生数 > 0.05)
             ,mapping = aes(label = 大学), nudge_x = 1, nudge_y = 0.005 ,size=3) +
  geom_text(aes(label = label1), data = data.frame(), x = 25, y= 0.095, size = 3) +
  geom_text(aes(label = label2), data = data.frame(), x = 25, y= 0.090, size = 3)
</code></pre>


![scatterplot](niad.png)


2つの外国語大学が高い休学者率を示していますが、これは外大という性格上、
留学を志向する学生が多いためだと考えられます。（休学中の私費留学）
