<img id='top_image' src='img/ic_home_black_48dp_2x.png' style='width: 144px; height: 144px; position:absolute; top:30%; left:30%;'></img>
<div style='font-size: 80px; position:absolute; top:37%; left: 46%;'>
λ...
</div>
<div style='font-size:40px; position: absolute; bottom: 5%; left: 5%;'>
2016.11.26 @Kansai.kt
</div>
---
## Who?
### Name: Masui Masanori

### Twitter: [@masanori_msl](https://twitter.com/masanori_msl)
<img src="img/pumpkin.png" alt="twitter" style="width: 128px; height: 128px; position: absolute; top:20%; right: 19%;"></img>

### Blog: [vaguely](http://mslgt.hatenablog.com/)

### GitHub: https://github.com/masanori840816

### App: [SearchWakayamaToilet](https://play.google.com/store/apps/details?id=jp.searchwakayamatoilet)
---
## 今回の概要
### Kotlinのラムダ式について調べた試したことを、<br>ゆるゆるふんわりとお話します。
---
## ラムダ式(Lambda expression)
### (定義は色々あるとは思いますが)
### 無名関数を簡便に書く方法、とします。
---
## 関数リテラル
### ラムダ式や無名関数(匿名関数)であり、<br>予め定義せず直接処理を書いて変数への代入、<br>引数として渡すといったことができます。
---
## 例
### 無名関数

```xml
val arrayList = arrayListOf(1, 5, 2)
Collections.sort(arrayList, fun (x: Int, y: Int): Int{
        return y - x
})
```
---
## 例
### ラムダ式
```xml
val arrayList = arrayListOf(1, 5, 2)
Collections.sort(arrayList) { x, y -> y - x }
```

#### 下記のように第二引数である関数も括弧()の中に書くことは可能ですが、<br>通常は最後の引数が関数の場合括弧()の外に出します。

```xml
val arrayList = arrayListOf(1, 5, 2)
Collections.sort(arrayList, { x, y -> y - x })
```
### シンプルに書けて良いですね(・∀・)
### ...ただし全ての関数をラムダ式で書けるわけではありません。
---
## SAM(Single Abstract Method) type
### sortの中身はこんな内容です。

```xml 
val arrayList = arrayListOf(1, 5, 2)
Collections.sort(arrayList, object: Comparator<Int>{
    override fun compare(x: Int, y: Int): Int{
        return y - x
    }
})
```
### Comparatorは、サブクラスで実装が必要な関数が一つだけという特徴があります。
### そのため引数の型や数、戻り値の型などが推測でき、省略することが可能となります。
---
## SAM(Single Abstract Method) type
### ラムダ式に変換できるのはSAM typeのみで、<br>実装する関数が複数ある場合は通常通り書く必要があります。
---

## 実行速度
### ラムダ式で書いた場合とそうでない場合とで、<br>実行速度に差は生まれるのでしょうか。
### ラムダ式で書いた場合

 ```xml 
@Throws(IOException::class)
override fun start(primaryStage: Stage) {
    val start = System.currentTimeMillis()

    // 偶数だけArrayListに追加する.
    val result = (0..1000000).filter { it % 2 == 0 }
    
    val end = System.currentTimeMillis()
    val diff = end - start
    println(diff)
}
 ```
---
## 実行速度
### ラムダ式を使わない場合
 ```xml 
@Throws(IOException::class)
override fun start(primaryStage: Stage) {
    val start = System.currentTimeMillis()
    
    // 偶数だけArrayListに追加する.
    val result = ArrayList<Int>()
    for(i in 0..1000000){
        if(i % 2 == 0){
            result.add(i)
        }
    }
    
    val end = System.currentTimeMillis()
    val diff = end - start
    println(diff)
}
 ```
---
## 結果
<table style='absolute; top: 30%; font-size: 25px;' border='1'>
<tr>
<th></th>
<th>ラムダ式で書いた場合</th>
<th>ラムダ式を使わない場合</th>
</tr>
<tr>
<th>一回目</th>
<th>54 ms</th>
<th>30 ms</th>
</tr>
<tr>
<th>二回目</th>
<th>59 ms</th>
<th>30 ms</th>
</tr>
<tr>
<th>三回目</th>
<th>59 ms</th>
<th>38 ms</th>
</tr>
<tr>
<th>四回目</th>
<th>91 ms</th>
<th>29 ms</th>
</tr>
<tr>
<th>五回目</th>
<th>77 ms</th>
<th>33 ms</th>
</tr>
</table>
---
## Decompile
### IntelliJ IDEAではTools > Kotlin > Show Kotlin Bytecodeでバイトコードが、<br>Kotlin Bytecode上部のDecompileを押すとデコンパイルされたコードが見られます。

---
## Decompile
### ラムダ式で書いた場合

 ```xml 
public void start(@NotNull Stage primaryStage) throws IOException {
    Intrinsics.checkParameterIsNotNull(primaryStage, "primaryStage");
    long start = System.currentTimeMillis();
    Iterable end = (Iterable)(new IntRange(0, 1000000));
    Collection diff = (Collection)(new ArrayList());
    Iterator var8 = end.iterator();
    while(var8.hasNext()) {
        Object element$iv$iv = var8.next();
        int it = ((Number)element$iv$iv).intValue();
        if(it % 2 == 0) {
        diff.add(element$iv$iv);
        }
    }
    List result = (List)diff;
    long end1 = System.currentTimeMillis();
    long diff1 = end1 - start;
    System.out.println(diff1);
}
 ```
---
## Decompile
### ラムダ式を使わない場合
```xml 
public void start(@NotNull Stage primaryStage) throws IOException {
    Intrinsics.checkParameterIsNotNull(primaryStage, "primaryStage");
    long start = System.currentTimeMillis();
    ArrayList result = new ArrayList();
    int end = 0;
    int var6 = 1000000;
    if(end <= var6) {
        while(true) {
        if(end % 2 == 0) {
            result.add(Integer.valueOf(end));
        }
        if(end == var6) { break; }
        ++end;
        }
    }
    long var9 = System.currentTimeMillis();
    long diff = var9 - start;
    System.out.println(diff);
}
```
---
## kotlin1.0.5
### 先日リリースされたKotlin1.0.5からは、IntelliJ IDEAの機能で下記を自動でラムダ式に変換できるようになりました。

### 変換前
```xml
val list = arrayOf("one", "two")
val result = arrayListOf<String>()
for(s in list){
    if(s.isNotEmpty()){
        result.add(s)
    }
}
```
---
## kotlin1.0.5
### 変換後

```xml
val stringList = arrayOf("one", "two")
val result = stringList.filter{it.isNotEmpty()}
```

#### ※本来なら下記のように引数名を指定しますが、<br>引数が一つだけの場合は暗黙的にitとして扱うことができます。
```xml
val stringList = arrayOf("one", "two")
val result = stringList.filter{s -> s.isNotEmpty()}
```
---
## kotlin1.0.5
### v1.0.5-release-IJ2016.3-2では、<br>さらに下記のように書くことができるようになりました。

```xml
val stringList = arrayOf("one", "two")
val result = stringList.filter(String::isNotEmpty)
```

### ※ :(コロン)を2つ並べた表記はbound callable referenceといい、<br>ここではStringのisNotEmpty関数に対する参照を取得しています。
---
## 高階関数(Higher-order function)
### Array.filterなどの関数は、引数に関数をとります。
### このような関数を高階関数と呼びます。

```xml
val result = stringList.filter(String::isNotEmpty)
```
<ul style='font-size: 30px;'>
<li>filter</li>
<li>map</li>
<li>flatMap</li>
<li>max</li>
<li>sort</li>
<li>etc.</li>
</ul>
---
## 例外
### ラムダ式そのものに対する特別な例外処理は無いようです。
### ただし、Javaと比較して大きな違いが一つあります。
### Kotlinにはチェック例外(検査例外)がないことです。
---
## 例外
### Javaでは例外処理が行われているかをコンパイル時にチェックする機能があり、<br>適切にtry - catchなどが行われていない場合はコンパイルエラーとなります。
### ただし、ラムダ式の外でtry - catchを行ってもラムダ式内のエラーはキャッチできないようです。
### (コンパイルエラーになります)
---
## 例外
### そのためこのようなコードになります。

```xml
ArrayList<String> stringList = new ArrayList<>();
stringList.add("1");
stringList.add("2");
try(BufferedWriter writer = Files.newBufferedWriter(Paths.get("test.txt"))){
    stringList.forEach(s -> {
        try {
            writer.write(s + '\n');
        } catch (IOException ex) {
            System.out.println("例外が発生しました");
            /// ラムダ式の外で例外がキャッチできるように非チェック例外を投げる.         
            throw new UncheckedIOException("例外が発生しました");
        }
    });
    writer.flush();
    writer.close();
}catch (IOException | RuntimeException e){
    System.out.println(e.getMessage());
}
```
---
## 例外
### Kotlinではチェック例外が無いため、下記のように書くことができます。
```xml
val stringList = arrayOf("1", "2")
try{
    val writer = Files.newBufferedWriter(Paths.get("test.txt"))
    stringList.forEach {
        writer.write(it + '\n')
    }
    writer.flush()
    writer.close()
}catch (e: Exception){
    println("例外が発生しました")
}
```
---
## 例外
### ※今回はチェック例外の不便なところを挙げましたが、<br>チェック例外そのものは(当然)良いところもあるため<br>適切に使っていきましょう(๑•̀ㅂ•́)و✧
---
## SAM typeを作る
### Comparator - compareのように予め用意された関数だけでなく、SAM typeを自作することも可能で、<br>関数を一つ持つinterfaceを作るだけです。
---
## SAM typeを作る
### Java
```xml
public interface JavaSamTest {
    String test(String arg);
}
 ```
 
### これをKotlinで変数に代入すると下記のように書くことができます。
 
 ```xml
val javaSamTest = JavaSamTest { "SAMだよ～" }
 ```
---
## SAM typeを作る
### Kotlin
```xml
interface KotlinSamTest {
    fun test(arg: String): String
}
 ```

### これをKotlinで変数に代入すると下記のように書くことができます。
  
 ```xml
 val kotlinSamTest = object :KotlinSamTest{
     override fun test(arg: String): String = "SAMｶﾅ-?"
 }
 ```
 
### …おや?
---
## SAM typeを作る
### Kotlinでinterfaceを書いた場合、<br>それがSAMに相当する内容であってもSAM typeとしては扱われないようです。
---
## Kotlin1.1
### 次期バージョンのKotlin1.1での変更は?
---
## Kotlin1.1
### 先に挙げたbound callable references、<br>Destructuring in Lambdasなどは影響がありそうです。
---
## Destructuring in Lambdas
### ラムダ式の中でDataクラスなどを分解して使うことができます。
 ```xml
 myMap.forEach {
     (k, v) ->
     println(“$k => $v”)
 }
 ```
---
## まとめ
<div style='font-size: 70px; position:absolute; top: 40%;'>
Kotlinはいいぞ
</div>
---
## 参考
<ul style='font-size: 30px;'>
<li><a href="http://kotlinlang.org/docs/reference/">Reference - Kotlin Programming Language</a></li>
<li><a href="http://www.ric.co.jp/book/contents/book_1039.html">Kotlinスタートブック──新しいAndroidプログラミング── - リックテレコム</a></li>
<li><a href="http://gihyo.jp/magazine/wdpress/archive/2016/vol94">WEB+DB PRESS Vol.94 - 技術評論社</a></li>
<li><a href="http://try.kotlinlang.org/#/Kotlin%20Koans/">Kotlin Koans - Try Kotlin</a></li></ul>
<h3>SAM type</h3>
<ul style='font-size: 30px;'>
<li><a href="http://blog.satotaichi.info/mystery_of_sam_type_conversion/">KotlinにおけるSAMタイプの話 - さにあらず</a></li>
<li><a href="https://gist.github.com/taichi/2bd0fdbabbe977ed34e3">Kotlin SAM type conversion - taichi - GitHub</a></li>
<li><a href="http://qiita.com/RyotaMurohoshi/items/01b370f34a4bf96f5c39">Kotlinでリスナーやコールバックをスッキリと書く【関数リテラルとSAM変換】 - Qiita</a></li>
</ul>
---
## 参考
<h3>Kotlin Bytecode</h3>
<ul style='font-size: 30px;'>
<li><a href="https://www.gitbook.com/book/sys1yagi/anatomy-kotlin/details">解剖 Kotlin ~バイトコードを読み解く~ - GitBook</a></li>
<li><a href="http://yyyank.blogspot.jp/2016/08/show-kotlin-bytecodekotlin.html">「Show Kotlin Bytecode」でKotlinを理解する - Javaプログラマのはしくれダイアリー</a></li>
</ul>
<h3>高階関数</h3>
<ul style='font-size: 30px;'>
<li><a href="http://qiita.com/opengl-8080/items/36351dca891b6d9c9687">Kotlin のコレクション使い方メモ - Qiita</a></li>
<li><a href="http://kirimin.hatenablog.com/entry/2015/08/24/093646">Kotlinのリスト操作関数まとめ - みんからきりまで</a></li>
</ul>
---
## 参考
<h3>bound callable references</h3>
<ul style='font-size: 30px;'>
<li><a href="https://github.com/Kotlin/KEEP/blob/master/proposals/bound-callable-references.md">KEEP/bound-callable-references.md at master - Kotlin/KEEP - GitHub</a></li>
</ul>
<h3>Kotlin1.1</h3>
<ul style='font-size: 30px;'>
<li><a href="https://realm.io/news/andrey-breslav-whats-next-for-kotlin-roadmap/">Watch Where We Stand & What's Next for Kotlin - realm</a></li>
<li><a href="https://www.infoq.com/jp/news/2016/07/kotlin-11-roadmap">Kotlin1.1へのロードマップ - InfoQ</a></li>
</ul>
---
<div style='font-size: 70px; position:absolute; top: 40%;'>
Have a nice Kotlin !
</div>