

# github pagesことはじめ

## 参考

[https://pages.github.com](https://pages.github.com)

## 手順

githubで"username.github.io"という名前で新しいリポジトリを作成する

```sh
git clone https://github.com/yonezzzet/yonezzzet.github.io.git
cd yonezzzet.github.io/
vim index.html
```

index.htmlを適当に作成し、保存。

```sh
git add index.html
git commit -m "initial commit"
git push -u origin master
```

http://username.github.io/ がURLになる。

