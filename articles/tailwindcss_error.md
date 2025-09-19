---

title: "npx tailwindcss init -pのエラー解消"

emoji: "💻"

type: "tech" 

topics: \["tailwindcss"]

published: true

---



tailwindcssを使おうとしたところ、以下のエラーが出てしまった。



'tailwind' is not recognized as an internal or external command,

operable program or batch file.





※ `npm audit fix --force` を実行すると `npm start` が使えなくなってしまったため、別の方法を模索。



tailwindcssが認識されない場合は、バージョン3を明示してインストールすると解決する。



```bash

npm install -D tailwindcss@3 postcss@latest autoprefixer@latest





これにより、npx tailwindcss init -p が正常に実行できるようになった。

