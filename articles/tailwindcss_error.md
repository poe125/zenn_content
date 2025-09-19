tailwindcssを使おうとしたら、

"'tailwind' is not recognized as an internal or external command,

operable program or batch file."というエラーが出てしまった。



(解決方法としてnpm audit fix --forceを実行したところ、npm startが使えなくなってしまったので別の方法を模索。)



tailwindcssが認識されないので、v3のものを明記してみた。



npm install -D tailwindcss@3 postcss@latest autoprefixer@latest



これによってnpx tailwindcss init -pが実行出来るようになった。

