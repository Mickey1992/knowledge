1. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

class Product {
    private String category;
    private int price;

    public Product(String category, int price) {
        this.category = category;
        this.price = price;
    }

    public String getCategory() {
        return category;
    }

    public int getPrice() {
        return price;
    }
}

public class Main {
    public static void main(String[] args) {
        List<Product> products = List.of(
            new Product("Book", 1500),
            new Product("Electronics", 80000),
            new Product("Book", 2500),
            new Product("Food", 500),
            new Product("Electronics", 65000)
        );

        Map<String, Integer> totalPriceByCategory = products.stream()
            .collect(Collectors.groupingBy(
                Product::getCategory,
                Collectors.summingInt(Product::getPrice)
            ));
        System.out.println(totalPriceByCategory);
    }
}
```

```
A. コンパイルエラーが発生する
B. {Food=[500], Book=[1500, 2500], Electronics=[80000, 65000]}
C. {Food=1, Book=2, Electronics=2}
D. {Food=500, Book=4000, Electronics=145000}
```

```
正解: D
解説:
A. コンパイルエラーが発生する - 提示されたコードはJavaの文法として正しく、ストリームAPIのcollectメソッド、Collectors.groupingBy、Collectors.summingIntの使い方も適切であるため、コンパイルエラーは発生しません。
B. {Food=[500], Book=[1500, 2500], Electronics=[80000, 65000]} - この出力は、Collectors.groupingByの第二引数（ダウンストリームコレクタ）を指定せず、デフォルトのCollectors.toList()が適用された場合の結果です。しかし、コードではCollectors.summingIntが指定されているため、価格のリストではなく、合計値が計算されます。
C. {Food=1, Book=2, Electronics=2} - この出力は、各カテゴリに属する要素の数を数えた場合の結果であり、Collectors.counting()を使用した場合に近いです。問題のコードで使用されているCollectors.summingIntは、要素の数を数えるのではなく、指定されたプロパティ（この場合は価格）の合計値を算出します。
D. {Food=500, Book=4000, Electronics=145000} - 正解です。Collectors.groupingBy(Product::getCategory, ...)は、商品をカテゴリごとにグループ化します。第二引数のCollectors.summingInt(Product::getPrice)は、各グループに属するProductオブジェクトのpriceを合計するダウンストリームコレクタです。そのため、"Food"は500、"Book"は1500+2500=4000、"Electronics"は80000+65000=145000となり、この出力が生成されます。
```

2. 問題文: 以下のコードを実行した際の出力として正しいものはどれですか。

```Java
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

public class Main {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        List<Callable<Integer>> tasks = List.of(
            () -> { TimeUnit.SECONDS.sleep(1); return 10; },
            () -> { TimeUnit.SECONDS.sleep(4); return 20; }
        );

        List<Future<Integer>> futures = executor.invokeAll(tasks, 2, TimeUnit.SECONDS);

        Future<Integer> future1 = futures.get(0);
        Future<Integer> future2 = futures.get(1);

        System.out.println(future1.get() + ", " + future2.isCancelled());

        executor.shutdown();
    }
}
```

```
A. 10, true
B. 例外がスローされる
C. 10, false
D. 20, false
```

```
正解: A
解説:
A. 10, true - 正解です。invokeAllメソッドはタイムアウト値（2秒）を指定して呼び出されています。1秒で完了する最初のタスクは時間内に終了するため、future1.get()は正常に結果の10を返します。一方、4秒かかる2番目のタスクはタイムアウトし、invokeAllによってキャンセルされます。そのため、future2.isCancelled()はtrueを返します。
B. 例外がスローされる - invokeAllメソッドは、タイムアウトした場合にInterruptedExceptionをスローする可能性がありますが、このコードの実行フローではスローされません。また、キャンセルされたfuture2に対してget()メソッドを呼び出していないため、CancellationExceptionも発生しません。プログラムは正常に完了します。
C. 10, false - 最初のタスクの結果が10である点は正しいですが、future2.isCancelled()がfalseになるという点が誤りです。invokeAllのタイムアウト（2秒）を超過したタスクはキャンセルされるため、isCancelled()はtrueを返します。
D. 20, false - 2番目のタスクはタイムアウトにより完了しないため、その結果である20が出力されることはありません。また、タスクがキャンセルされるためisCancelled()の結果もtrueが期待されます。
```

3. 問題文: 以下のディレクトリ構造が存在する前提で、指定された Java コードを実行した際の出力を選択してください。

```java
ディレクトリ構造:
/work/ (dir)
/work/report.csv (file)
/work/source/ (dir)
/work/source/Main.java (file)
/work/source/legacy.log (file)
/work/archive/ (dir)
/work/archive/backup.log (file)

コード:
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.stream.Stream;

public class FileCounter {
    public static void main(String[] args) throws IOException {
        Path start = Paths.get("/work");
        long fileCount = 0;
        try (Stream<Path> stream = Files.walk(start)) {
            fileCount = stream
                .filter(path -> path.toString().endsWith(".log"))
                .count();
        }
        System.out.println(fileCount);
    }
}
```

```
A. 1
B. 3
C. 2
D. コンパイルエラーが発生する
```

```
正解: C
解説:
A. 1 - Files.walkメソッドは、指定された開始パスから始まるディレクトリツリーを再帰的に探索します。そのため、/work/sourceディレクトリだけでなく、そのサブディレクトリである/work/archiveも探索の対象となります。結果として、ファイルは1つではありません。
B. 3 - .logで終わるファイルはlegacy.logとbackup.logの2つです。ディレクトリパス（/work/sourceなど）や他の拡張子のファイル（Main.javaなど）はfilterの条件に一致しないため、カウントに含まれません。
C. 2 - 正解です。Files.walk(start)は、/workディレクトリとその全てのサブディレクトリ、および含まれるファイルを探索するStream<Path>を返します。.filter(path -> path.toString().endsWith(".log"))によって、パスの文字列表現が.logで終わる要素のみが選択されます。これに該当するのは/work/source/legacy.logと/work/archive/backup.logの2つのパスです。したがって、count()メソッドは2を返します。
D. コンパイルエラーが発生する - 提示されたコードは、java.nio.fileパッケージとストリームAPIを正しく使用しており、構文上の誤りはありません。mainメソッドがIOExceptionをスローする宣言も適切であるため、コンパイルは正常に成功します。
```

4. 問題文: クラスパスのルートに以下の 3 つのプロパティファイルが存在する場合、指定された Java コードを実行するとどの文字列が出力されますか。

```
ファイルの内容:

Messages.properties
message=Default

Messages_en.properties
message=English

Messages_en_US.properties
message=American English
```

```java
import java.util.Locale;
import java.util.ResourceBundle;

public class Greeter {
    public static void main(String[] args) {
        Locale.setDefault(new Locale("fr", "FR"));
        Locale currentLocale = new Locale("en");
        ResourceBundle rb = ResourceBundle.getBundle("Messages", currentLocale);
        System.out.println(rb.getString("message"));
    }
}
```

```
A. American English
B. English
C. Default
D. MissingResourceExceptionがスローされる
```

```
正解: B
解説:
A. American English - ResourceBundleの検索では、より具体的なロケール（en_US）が存在しても、getBundleメソッドに指定されたロケール（en）が優先して検索されます。この値が出力されるのは、new Locale("en", "US")が指定された場合です。
B. English - 正解です。ResourceBundle.getBundleメソッドは、第二引数で渡されたcurrentLocale（en）に最も一致するリソースバンドルを検索します。この場合、Messages_en.propertiesが直接一致するため、このファイルが使用されます。Locale.setDefaultでデフォルトロケールを変更していますが、getBundleに明示的にロケールが渡されているため、デフォルトロケールは無視されます。
C. Default - デフォルトのリソースバンドル（Messages.properties）が使用されるのは、指定されたロケールに一致するバンドルが見つからない場合です。このコードではMessages_en.propertiesが存在し、指定されたロケールに一致するため、デフォルトバンドルは使用されません。
D. MissingResourceExceptionがスローされる - getBundleメソッドに指定されたcurrentLocaleに一致するMessages_en.propertiesファイルが存在するため、リソースが見つからないことを示すこの例外はスローされません。
```

5. 問題文: 以下の前提条件のもとで、指定された Java コードを実行した結果として正しいものはどれですか。

```
前提条件:

有効な JDBC Connection オブジェクト conn が存在する。

products テーブルには以下のデータが id の昇順で格納されている。
id | name
---|-----------
10 | Monitor
20 | Keyboard
30 | Mouse
40 | Webcam
```

```java
import java.sql.*;

public class JdbcCursorTest {
    public static void main(String[] args) throws SQLException {
        // conn は有効な Connection オブジェクトとする
        Connection conn = getYourConnection(); // この部分はダミーです

        String sql = "SELECT id, name FROM products ORDER BY id";
        try (Statement stmt = conn.createStatement(
                ResultSet.TYPE_SCROLL_INSENSITIVE,
                ResultSet.CONCUR_READ_ONLY);
                ResultSet rs = stmt.executeQuery(sql)) {

            rs.absolute(4);
            rs.relative(-3);
            System.out.println(rs.getString("name"));
        }
    }

}

```

```

A. Mouse
B. SQLException がスローされる
C. Monitor
D. Webcam

```

```

正解: C
解説:
A. Mouse - rs.absolute(4)の時点でカーソルは 4 行目('Webcam')を指しますが、その後に rs.relative(-3)が実行されるため、カーソル位置は変わります。'Mouse'は 3 行目のデータであり、最終的なカーソル位置ではありません。
B. SQLException がスローされる - rs.absolute(4)で有効な最後の行に移動し、そこから rs.relative(-3)で 1 行目に移動します。カーソルは常に結果セットの有効な範囲内にあるため、SQLException はスローされません。
C. Monitor - 正解です。createStatement の第一引数に ResultSet.TYPE_SCROLL_INSENSITIVE が指定されているため、この ResultSet は自由にカーソルを移動できます。rs.absolute(4)はカーソルを 4 番目のレコード('Webcam')に移動させます。次に rs.relative(-3)は現在の位置(4 行目)から 3 つ前のレコードにカーソルを移動させます。その結果、カーソルは 1 番目のレコード('Monitor')を指すことになり、その name が出力されます。
D. Webcam - rs.absolute(4)を実行した直後であれば、カーソルはこのレコードを指しています。しかし、その後に rs.relative(-3)が実行されるため、カーソルは別の位置に移動します。したがって、この値が出力されることはありません。

```

6.  問題文: 以下の 2 つのモジュールが定義されている場合、com.example.app モジュールをコンパイルした結果として最も適切なものはどれですか。

```java
モジュール com.example.lib:

module-info.java:

module com.example.lib {
    exports com.example.lib.api;
}
```

```java
com.example.lib.api.ApiService.java:

package com.example.lib.api;
public class ApiService {
    public static String getMessage() { return "API"; }
}
```

```java
com.example.lib.internal.InternalUtil.java:

package com.example.lib.internal;
public class InternalUtil {
    public static String getInfo() { return "Internal"; }
}
```

```java
モジュール com.example.app:

module-info.java:

module com.example.app {
    requires com.example.lib;
}
```

```java
com.example.app.Main.java:

package com.example.app;
import com.example.lib.api.ApiService;
import com.example.lib.internal.InternalUtil;

public class Main {
    public static void main(String[] args) {
        System.out.println(ApiService.getMessage());
        System.out.println(InternalUtil.getInfo());
    }
}
```

```
A. 実行時に IllegalAccessException がスローされる
B. 正常にコンパイルできるが、実行時にエラーが発生する
C. com.example.lib モジュールのコンパイル時にエラーが発生する
D. com.example.app モジュールのコンパイル時にエラーが発生する
```

```
正解: D
解説:
A. 実行時に IllegalAccessException がスローされる - モジュール間の可視性に関する問題は、Java プラットフォーム・モジュールシステムでは主にコンパイル時に検出されます。そのため、実行時まで問題が持ち越されることはなく、コンパイルの段階で失敗します。
B. 正常にコンパイルできるが、実行時にエラーが発生する - com.example.lib モジュールがエクスポートしていないパッケージにアクセスしようとしているため、com.example.app モジュールのコンパイルは成功しません。
C. com.example.lib モジュールのコンパイル時にエラーが発生する - com.example.lib モジュールの module-info.java と各クラスの定義は正しく記述されています。このモジュールは自己完結しており、コンパイル上の問題はありません。
D. com.example.app モジュールのコンパイル時にエラーが発生する - 正解です。com.example.app モジュールは com.example.lib モジュールを requires していますが、com.example.lib は com.example.lib.api パッケージしか exports していません。Main.java がエクスポートされていない com.example.lib.internal パッケージの InternalUtil クラスをインポートしようとしているため、「パッケージが不可視である」という趣旨のコンパイルエラーが発生します。

```

7.  問題文: 以下の Employee クラスと main メソッドを持つクラスがあります。このコードを実行した結果として正しいものはどれですか。

```java

import java.util.HashSet;
import java.util.Objects;
import java.util.Set;

class Employee {
private int id;
private String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return id == employee.id;
    }

    // hashCode()メソッドはオーバーライドされていない

}

public class SetBehavior {
    public static void main(String[] args) {
        Set<Employee> employees = new HashSet<>();
        employees.add(new Employee(1, "Suzuki"));
        employees.add(new Employee(1, "Sato"));
        System.out.println(employees.size());
    }
}

```

```

A. 1
B. 実行時に例外がスローされる
C. 2
D. 0

```

```

正解: C
解説:
A. 1 - equals メソッドは id フィールドのみを比較するようにオーバーライドされているため、2 つの Employee オブジェクトは論理的に等価です。しかし、HashSet は要素を追加する際にまず hashCode メソッドを使って格納場所を決定します。hashCode がオーバーライドされていないため、2 つのオブジェクトは異なるハッシュコードを返し、結果として equals メソッドが呼び出されることなく、別の要素として扱われます。
B. 実行時に例外がスローされる - equals と hashCode の規約違反（等しいオブジェクトは等しいハッシュコードを返さなければならない）は、コンパイルエラーや実行時例外を直接引き起こすわけではありません。これはコレクションが期待通りに動作しない原因となりますが、例外のスローには至りません。
C. 2 - 正解です。HashSet にオブジェクトを追加する際、まずそのオブジェクトの hashCode()が計算されます。Employee クラスでは hashCode()がオーバーライドされていないため、Object クラスのデフォルト実装が使われます。これにより、2 つの new Employee(...)インスタンスは、メモリ上のアドレスが異なるため、異なるハッシュコード値を生成します。HashSet はハッシュコードが異なると判断し、2 つのオブジェクトを別々のものとして格納するため、equals()メソッドを呼び出すまでには至りません。結果として、セットのサイズは 2 になります。
D. 0 - add メソッドは正常に機能し、セットに要素が追加されます。処理中に問題が発生して要素が追加されない、あるいは削除されるといった動作にはなりません。

```

8.  問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java

import java.util.List;
import java.util.function.Predicate;

public class PredicateChain {
    public static void main(String[] args) {
        List<String> data = List.of("Java", "Python", "Go", "JavaScript");

        Predicate<String> p1 = s -> s.length() > 4;
        Predicate<String> p2 = s -> s.contains("Java");

        long count = data.stream()
            .filter(p1.or(p2.negate()))
            .count();
        System.out.println(count);
    }
}
```

```
A. 1
B. 2
C. 3
D. 4
```

```
正解: C
解説:
A. 1 - この結果は、p1.and(p2)のような条件を適用した場合に近くなりますが、問題のコードは or と negate を使用しているため、評価ロジックが異なります。
B. 2 - この結果は、p1 のみ、または p2 のみを適用した場合に考えられますが、2 つの述語を組み合わせた論理評価の結果とは異なります。
C. 3 - 正解です。このコードは、ストリームの各要素に対して p1.or(p2.negate())という条件でフィルタリングを行います。これは「文字列の長さが 4 より大きい」(p1)、または「文字列に'Java'を含まない」(p2.negate())という条件に合致するかを評価します。

"Java": 長さが 4 なので p1 は false。'Java'を含むので p2 は true、p2.negate()は false。false or false で結果は false。

"Python": 長さが 6 なので p1 は true。true or ...となり、結果は true。

"Go": 長さが 2 なので p1 は false。'Java'を含まないので p2 は false、p2.negate()は true。false or true で結果は true。

"JavaScript": 長さが 10 なので p1 は true。true or ...となり、結果は true。
したがって、"Python", "Go", "JavaScript"の 3 つの要素が条件に合致するため、カウントは 3 になります。

D. 4 - "Java"という要素は、長さが 4 以下であり、かつ'Java'という文字列を含むため、p1 と p2.negate()のどちらの条件も満たしません。そのため、全ての要素がフィルタを通過することはありません。

```

9.  問題文: 以下のコードをコンパイルした結果として正しいものはどれですか。

```java

import java.io.IOException;

class CustomException extends Exception {}

class Base {
    void performAction() throws CustomException {
        System.out.println("Base action");
    }
}

class Derived extends Base {
    @Override
    void performAction() throws IOException {
        System.out.println("Derived action");
    }
}

```

```

A. Derived クラスのコンパイル時にエラーが発生する
B. 正常にコンパイルできるが、実行時に ClassCastException が発生する
C. Base クラスのコンパイル時にエラーが発生する
D. 正常にコンパイルおよび実行できる

```

```

正解: A
解説:
A. Derived クラスのコンパイル時にエラーが発生する - 正解です。Java のメソッドオーバーライドのルールでは、サブクラスのメソッドがスロー宣言できるチェック例外は、スーパークラスのメソッドがスロー宣言している例外、そのサブクラスの例外、または何もスローしない場合に限られます。このコードでは、スーパークラスの performAction メソッドが CustomException をスロー宣言しているのに対し、サブクラスの Derived は無関係なチェック例外である IOException を新たにスロー宣言しています。これはルールに違反するため、Derived クラスの performAction メソッドの定義でコンパイルエラーが発生します。
B. 正常にコンパイルできるが、実行時に ClassCastException が発生する - コンパイルが成功しないため、この選択肢は誤りです。また、ClassCastException は不正な型キャストが行われた場合に発生する例外であり、このコードのロジックとは直接関係ありません。
C. Base クラスのコンパイル時にエラーが発生する - Base クラスと CustomException クラスの定義は構文的に正しく、自己完結しているため、これらのコンパイルは正常に成功します。問題はサブクラスでのオーバーライド方法にあります。
D. 正常にコンパイルおよび実行できる - 前述の通り、チェック例外に関するオーバーライドのルールに違反しているため、コンパイルは成功しません。

```

10. 問題文: 以下のコードを実行した際の出力として正しいものはどれですか。なお、America/Los_Angeles のタイムゾーンでは、2025 年 3 月 9 日の
    午前 2 時に夏時間が開始され、時計が午前 3 時に進むものとします。

```java
import java.time.Duration;
import java.time.ZoneId;
import java.time.ZonedDateTime;

public class TimeTravel {
    public static void main(String[] args) {
        ZoneId zone = ZoneId.of("America/Los_Angeles");
        ZonedDateTime dt = ZonedDateTime.of(2025, 3, 9, 1, 0, 0, 0, zone);

        ZonedDateTime result = dt.plus(Duration.ofHours(2));

        System.out.println(result.getHour() + ":" + result.getMinute());
    }
}

```

```
A. 3:0
B. 2:0
C. 4:0
D. DateTimeException がスローされる
```

```
正解: C
解説:
A. 3:0 - この結果は、夏時間への移行を考慮せず、単純に開始時刻の 1 時に 2 時間を加算した場合の時刻です。ZonedDateTime はタイムゾーンのルールを自動的に適用するため、このような単純な計算にはなりません。
B. 2:0 - この時刻は、夏時間の切り替わりが発生する時刻そのものです。開始時刻から 2 時間の期間を加算しているため、この時刻が出力されることはありません。
C. 4:0 - 正解です。ZonedDateTime は、夏時間のようなローカル時刻の不連続性を正しく扱います。開始時刻は午前 1 時です。ここに Duration で表される 2 時間を加算します。まず 1 時間を加算すると、時刻は午前 2 時になります。この瞬間、夏時間への移行によりローカル時刻は午前 3 時にジャンプします。残りの 1 時間を午前 3 時に加算すると、最終的な時刻は午前 4 時になります。したがって、出力は 4:0 となります。
D. DateTimeException がスローされる - ZonedDateTime と Duration は、夏時間の切り替わりをまたぐ時間計算を安全に処理できるように設計されています。存在しないローカル時刻（この例では午前 2 時台）を直接作成しようとすると例外が発生することがありますが、期間を加算する操作では例外はスローされず、自動的に調整されます。
```

11. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
@interface Testable {}

class MyTestSuite {
    @Testable
    public void testCaseA() {}

    public void testCaseB() {}

}

public class AnnotationChecker {
    public static void main(String[] args) {
        int count = 0;
        for (var method : MyTestSuite.class.getDeclaredMethods()) {
            if (method.isAnnotationPresent(Testable.class)) {
                count++;
            }
        }
        System.out.println(count);
    }
}
```

```
A. 1
B. 0
C. 2
D. コンパイルエラーが発生する
```

```
正解: B
解説:
A. 1 - この結果は、@Testable アノテーションが実行時に保持されている場合に期待されるものです。しかし、@Retention(RetentionPolicy.CLASS)が指定されているため、アノテーションは実行時には破棄されており、リフレクションで検出することはできません。
B. 0 - 正解です。@Testable アノテーションの定義には@Retention(RetentionPolicy.CLASS)が付与されています。このリテンションポリシーは、アノテーションがコンパイル時に.class ファイルに含まれるものの、実行時には Java 仮想マシン(JVM)によって破棄されることを意味します。main メソッド内のコードは、リフレクションを用いて実行時にアノテーションの存在を確認しようとしますが、その時点では既にアノテーションは存在しないため、isAnnotationPresent は false を返します。したがって、カウントは 0 のままとなります。
C. 2 - このコードでは MyTestSuite クラスに 2 つのメソッドが定義されていますが、@Testable アノテーションは testCaseA メソッドにしか付与されていません。アノテーションの有無に関わらず、すべてのメソッドをカウントした場合の結果に近いため、不適切です。
D. コンパイルエラーが発生する - 提示されたコードは、アノテーションの定義と使用法、およびリフレクションの API の観点から、すべて構文的に正しいです。リテンションポリシーの設定によって実行時の振る舞いが変わるだけで、コンパイル自体は正常に成功します。

```

12. 問題文: 以下の Shape インタフェースとそれを実装するクラス群が定義されています。このコードをコンパイルした結果として正しいものはどれですか。

```java

// Shape.java
public sealed interface Shape permits Circle, Rectangle {
    double getArea();
}

// Circle.java
public final class Circle implements Shape {
    private final double radius;
    public Circle(double radius) { this.radius = radius; }
    public double getArea() { return Math.PI _ radius _ radius; }
}

// Rectangle.java
public non-sealed class Rectangle implements Shape {
    private final double width, height;
    public Rectangle(double width, double height) { this.width = width; this.height = height; }
    public double getArea() { return width \* height; }
}

// Triangle.java
public class Triangle implements Shape {
    private final double base, height;
    public Triangle(double base, double height) { this.base = base; this.height = height; }
    public double getArea() { return (base \* height) / 2; }
}

```

```

A. Shape インタフェースのコンパイル時にエラーが発生する
B. 正常にコンパイルできる
C. Rectangle クラスのコンパイル時にエラーが発生する
D. Triangle クラスのコンパイル時にエラーが発生する

```

```

正解: D
解説:
A. Shape インタフェースのコンパイル時にエラーが発生する - Shape インタフェースの定義は、sealed インタフェースとして正しく記述されています。permits 句で許可されたクラスを指定する構文は有効です。
B. 正常にコンパイルできる - Triangle クラスが permits 句で許可されていないにもかかわらず Shape インタフェースを実装しようとしているため、コンパイルは成功しません。
C. Rectangle クラスのコンパイル時にエラーが発生する - Rectangle クラスは permits 句で許可されており、non-sealed 修飾子を付けることで、このクラス自体はさらにサブクラス化が可能であることを示しています。これは sealed 階層における有効な実装方法の一つです。
D. Triangle クラスのコンパイル時にエラーが発生する - 正解です。Shape インタフェースは sealed として宣言されており、permits 句によって実装が許可されているのは Circle クラスと Rectangle クラスのみです。Triangle クラスは permits 句に含まれていないため、Shape インタフェースを実装しようとするとコンパイラによってエラーが検出されます。

```

13. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java

import java.util.List;
import java.util.stream.Collectors;

public class StreamReduceTest {
    public static void main(String[] args) {
    List<String> items = List.of("A", "B", "C");

    String result = items.stream()
        .reduce("-", (s1, s2) -> s1 + s2);

    System.out.println(result);
    }
}
```

```
A. -ABC
B. ABC-
C. ABC
D. コンパイルエラーが発生する
```

```
正解: A
解説:
A. -ABC - 正解です。reduce メソッドのこのオーバーロードは、第一引数に単位元（identity）を取ります。ストリームの各要素は、この単位元から始まり、指定された二項演算子（ラムダ式）を使って順次結合されます。処理の流れは ((("-" + "A") + "B") + "C") となり、最終的に文字列"-ABC"が生成されます。
B. ABC- - reduce 処理の単位元は、処理の開始値として先頭に結合されます。末尾に結合されるわけではありません。
C. ABC - この結果は、単位元なしの reduce メソッド（reduce((s1, s2) -> s1 + s2)）を呼び出し、Optional<String>から値を取り出した場合に得られます。問題のコードでは単位元として"-"が指定されているため、この結果にはなりません。
D. コンパイルエラーが発生する - 提示されたコードは、Stream API の reduce メソッドを正しく使用しており、構文上の誤りはありません。そのため、コンパイルは正常に成功します。

```

14. 問題文: 以下の Task クラスと main メソッドを持つクラスがあります。このコードを実行した結果として最も適切な説明はどれですか。

```java

class Task implements Runnable {
    private volatile boolean running = true;

    public void stopTask() {
        running = false;
        System.out.println("Stop signal sent.");
    }

    @Override
    public void run() {
        while (running) {
            // 作業を実行していると仮定
        }
        System.out.println("Task finished.");
    }
}

public class ConcurrencyTest {
    public static void main(String[] args) throws InterruptedException {
        Task task = new Task();
        Thread thread = new Thread(task);
        thread.start();

        Thread.sleep(100);
        task.stopTask();
        thread.join();
        System.out.println("Main thread finished.");
    }
}

```

```

A. コンパイルエラーが発生する
B. プログラムは無限ループに陥り、終了しない可能性がある
C. プログラムは確実に終了する
D. 実行時に InterruptedException がスローされて終了する

```

```

正解: C
解説:
A. コンパイルエラーが発生する - 提示されたコードは、Runnable インタフェースの実装、volatile キーワードの使用、および Thread クラスの操作に関して構文的に正しく、コンパイルは正常に成功します。
B. プログラムは無限ループに陥り、終了しない可能性がある - このシナリオは、running フラグが volatile で宣言されていない場合に発生する可能性があります。volatile がないと、task スレッドが running フラグの初期値（true）をキャッシュしてしまい、メインスレッドによる変更（false への更新）を検知できないことがあります。しかし、volatile キーワードによってこの問題は解決されます。
C. プログラムは確実に終了する - 正解です。running フィールドは volatile として宣言されています。これにより、あるスレッド（この場合はメインスレッド）が running の値を変更すると、その変更が他のスレッド（task スレッド）に即座に可視化されることが保証されます。したがって、stopTask メソッドが呼び出されると、while (running)ループは running の最新値 false を読み取り、ループを終了します。その結果、プログラムはハングすることなく正常に終了します。
D. 実行時に InterruptedException がスローされて終了する - InterruptedException は、スリープ中または待機中のスレッドが割り込まれた場合にスローされます。このコードでは thread.interrupt()のような割り込み処理は呼び出されておらず、main スレッドの sleep も正常に完了するため、この例外はスローされません。

```

15. 問題文: 以下の Record クラスと main メソッドを持つクラスがあります。Record は Java 17 で導入されたレコード・クラスです。このコードを実行した結果として正しいものはどれですか。

```java
import java.util.Objects;

record Point(int x, int y) {}

public class RecordTest {
    public static void main(String[] args) {
        Point p1 = new Point(10, 20);
        Point p2 = new Point(10, 20);
        Point p3 = new Point(20, 10);

        boolean b1 = p1 == p2;
        boolean b2 = p1.equals(p2);
        boolean b3 = p1.equals(p3);

        System.out.println(b1 + ", " + b2 + ", " + b3);
    }
}

```

```

A. true, true, false
B. false, true, false
C. false, false, false
D. true, true, true

```

```

正解: B
解説:
A. true, true, false - p1 == p2 の評価が誤っています。p1 と p2 は異なるオブジェクトインスタンスであるため、==による参照の比較は false を返します。
B. false, true, false - 正解です。

p1 == p2: p1 と p2 は new によって別々のオブジェクトとして生成されるため、メモリ上の参照（アドレス）が異なります。==演算子は参照を比較するため、結果は false になります。

p1.equals(p2): record は、その全てのコンポーネント（この場合は x と y）を比較する equals メソッドを自動的に実装します。p1 と p2 は x と y の値が両方とも等しいため、equals は true を返します。

p1.equals(p3): p1 と p3 は x と y の値が異なるため、equals は false を返します。

C. false, false, false - p1.equals(p2)の評価が誤っています。レコードの equals メソッドは、状態（フィールドの値）に基づいて等価性を判断します。
D. true, true, true - p1 == p2 および p1.equals(p3)の評価が誤っています。==は参照を比較し、p3 は p1 とは状態が異なるため equals は false を返します。

```

16. 問題文: カレントディレクトリに以下の内容を持つ config.properties というファイルが存在する前提で、指定された Java コードを実行した際の出力を選択してください。

```
config.properties ファイルの内容:

# Database settings

database.url=jdbc:mysql://localhost/db
database.user=admin

# Application settings

app.name=MainApp

# app.version=1.2

app.mode=production
```

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.stream.Stream;

public class FileProcessor {
    public static void main(String[] args) throws IOException {
        Path filePath = Path.of("config.properties");
        long count = 0;
        try (Stream<String> lines = Files.lines(filePath)) {
            count = lines
                .map(String::trim)
                .filter(line -> !line.startsWith("#"))
                .filter(line -> !line.isEmpty())
                .count();
        }
        System.out.println(count);
    }
}

```

```

A. 4
B. 5
C. 6
D. 7

```

```

正解: A
解説:
A. 4 - 正解です。Files.lines はファイルを 1 行ずつ読み取り Stream<String>を生成します。

map(String::trim): 各行の前後の空白を削除しますが、このファイルには影響ありません。

filter(line -> !line.startsWith("#")): #で始まるコメント行を除外します。これにより、ファイル内の 3 行のコメントが除外されます。

filter(line -> !line.isEmpty()): 空行を除外します。database.user=admin の後の空行がこれにより除外されます。
結果として、database.url, database.user, app.name, app.mode の 4 行が残り、count()は 4 を返します。

B. 5 - 空行を除外する filter(line -> !line.isEmpty())の処理を見落とした場合、この結果になります。
C. 6 - #で始まる行のうち、# app.version=1.2 のみをコメントとみなし、# Database settings と# Application settings を有効な行として誤ってカウントした場合の結果に近いです。
D. 7 - filter 処理が適用されず、ファイル内の全ての行（コメント、空行を含む）をカウントした場合の結果です。

```

17. 問題文: 以下の try-with-resources 文を含むコードを実行した結果として正しいものはどれですか。

```java

class MyResource implements AutoCloseable {
    private String name;

    public MyResource(String name) {
        this.name = name;
        System.out.println(name + " opened.");
    }

    @Override
    public void close() {
        System.out.println(name + " closed.");
    }

}

public class TryWithResourcesOrder {
    public static void main(String[] args) {
        try (MyResource r1 = new MyResource("R1");
        MyResource r2 = new MyResource("R2")) {
            System.out.println("Try block executing.");
        }
    }
}
```

```
A.
R1 opened.
R2 opened.
Try block executing.
R1 closed.
R2 closed.
B.
R1 opened.
R2 opened.
Try block executing.
R2 closed.
R1 closed.
C.
R1 opened.
R1 closed.
R2 opened.
R2 closed.
Try block executing.
D. コンパイルエラーが発生する
```

```
正解: B
解説:
A. R1 opened. R2 opened. Try block executing. R1 closed. R2 closed. - リソースのクローズ順序が誤っています。try-with-resources 文では、リソースは取得されたときと逆の順序でクローズされます。
B. R1 opened. R2 opened. Try block executing. R2 closed. R1 closed. - 正解です。try-with-resources 文では、リソースは宣言された順序で初期化（取得）されます。したがって、まず R1 が、次に R2 がオープンされます。try ブロックの実行が完了した後、リソースは初期化とは逆の順序でクローズされます。そのため、まず R2 が、次に R1 がクローズされます。
C. R1 opened. R1 closed. R2 opened. R2 closed. Try block executing. - try-with-resources 文の実行フローが誤っています。すべてのリソースが取得された後に try ブロックが実行され、ブロックの終了後にすべてのリソースがクローズされます。リソースごとに取得とクローズが繰り返されるわけではありません。
D. コンパイルエラーが発生する - try-with-resources 文で複数のリソースを宣言する構文は正しく、MyResource クラスも AutoCloseable を適切に実装しているため、コンパイルエラーは発生しません。

```

18. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java

import java.util.stream.Collectors;
import java.util.stream.Stream;

public class CollectorGrouping {
    public static void main(String[] args) {
        Stream<String> stream = Stream.of("apple", "apricot", "banana", "blueberry", "cherry");

        var result = stream.collect(
            Collectors.groupingBy(
                s -> s.charAt(0),
                Collectors.counting()
            )
        );
        System.out.println(result);
    }

}

```

```

A. {a=2, b=2, c=1}
B. {a=[apple, apricot], b=[banana, blueberry], c=[cherry]}
C. {a=1, b=1, c=1}
D. コンパイルエラーが発生する

```

```

正解: A
解説:
A. {a=2, b=2, c=1} - 正解です。Collectors.groupingBy は、第一引数の分類関数 (s -> s.charAt(0)) に基づいてストリームの要素をグループ化します。この場合、各文字列の最初の文字によってグループ化されます。第二引数のダウンストリームコレクタとして Collectors.counting()が指定されているため、各グループに属する要素の数が Long 型として集計されます。その結果、'a'で始まる単語は 2 つ、'b'で始まる単語は 2 つ、'c'で始まる単語は 1 つとなり、この Map が生成されます。
B. {a=[apple, apricot], b=[banana, blueberry], c=[cherry]} - この出力は、ダウンストリームコレクタを指定しない場合（デフォルトで Collectors.toList()が使われる）、または明示的に Collectors.toList()を指定した場合の結果です。問題のコードでは Collectors.counting()が指定されているため、リストではなく要素数が値となります。
C. {a=1, b=1, c=1} - この結果は、Collectors.groupingBy の代わりに Collectors.toMap を不適切に使用した場合や、各グループの最初の要素のみを数えるようなロジックを想像した場合に考えられますが、groupingBy と counting の正しい動作ではありません。
D. コンパイルエラーが発生する - Collectors.groupingBy に分類関数とダウンストリームコレクタを渡すこのコードの構文は正しく、Java の Stream API として有効です。したがって、コンパイルエラーは発生しません。

```

19. 問題文: 以下の calculate メソッドを呼び出した際の出力として正しいものはどれですか。

```java

public class FinallyFlow {

    public static int calculate() {
        try {
            System.out.print("A");
            return 10 / 0; // ArithmeticExceptionが発生
        } catch (ArithmeticException e) {
            System.out.print("B");
            return 20;
        } finally {
            System.out.print("C");
        }
    }

    public static void main(String[] args) {
        System.out.print(calculate());
    }

}
```

```
A. AB20C
B. AC20
C. ABC20
D. A と B が出力され、例外がスローされる
```

```
正解: C
解説:
A. AB20C - finally ブロックは、catch ブロックの return 文が実行される直前に実行されます。そのため、C が出力されるのは 20 が出力される前です。
B. AC20 - try ブロックで例外が発生したため、catch ブロックが実行されます。したがって、B が出力されないこの選択肢は誤りです。
C. ABC20 - 正解です。実行フローは以下の通りです。

calculate メソッドが呼び出されます。

try ブロックに入り、"A"が出力されます。

10 / 0 が ArithmeticException をスローします。

実行が catch ブロックに移り、"B"が出力されます。

catch ブロックは 20 を返そうとしますが、その前に finally ブロックを実行する必要があります。

finally ブロックが実行され、"C"が出力されます。

finally ブロックの完了後、catch ブロックで準備されていた 20 が返されます。

main メソッドが受け取った値 20 を出力します。
結果として、出力は ABC20 となります。

D. A と B が出力され、例外がスローされる - catch ブロックが ArithmeticException を捕捉しているため、例外は calculate メソッドの外にはスローされません。catch ブロック内の return 文によって、メソッドは正常に値を返して終了します。

```

20. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java

import java.util.ArrayDeque;
import java.util.Deque;

public class DequeTest {
    public static void main(String[] args) {
        Deque<String> deque = new ArrayDeque<>();
        deque.push("A");
        deque.push("B");
        deque.offerFirst("C");
        deque.offerLast("D");

        System.out.print(deque.pop());
        System.out.print(deque.removeLast());
        System.out.print(deque.peekFirst());
    }
}

```

```

A. CDB
B. ABD
C. BDA
D. CDA

```

```

正解: A
解説:
A. CDB - 正解です。Deque は両端キューとして機能します。処理の順序と deque の状態は以下の通りです。

push("A"): [A]

push("B"): [B, A] (push は先頭に追加)

offerFirst("C"): [C, B, A] (先頭に追加)

offerLast("D"): [C, B, A, D] (末尾に追加)

pop(): 先頭から C を削除して返します。出力: "C"。状態: [B, A, D]

removeLast(): 末尾から D を削除して返します。出力: "D"。状態: [B, A]

peekFirst(): 先頭の B を削除せずに返します。出力: "B"。状態: [B, A]
最終的な出力は CDB となります。

B. ABD - push と offerFirst の操作がキューのように末尾に追加されると誤解した場合や、pop が末尾から要素を取得すると考えた場合にこの結果になる可能性があります。
C. BDA - push の順序や pop の対象を誤って解釈した場合に考えられる結果です。
D. CDA - removeLast()が返す値を誤解した場合、例えば先頭から 2 番目の要素を返すなど、Deque の API を不正確に理解している場合にこの結果になる可能性があります。

```

21. 問題文: 以下の Files.find メソッドを使用したコードがあります。このコードを実行した結果として最も適切な説明はどれですか。

```
前提:

/data ディレクトリが存在する。

/data ディレクトリ配下には、サブディレクトリを含めて合計 10 個のファイルと 3 個のディレクトリが存在する。

10 個のファイルのうち、5 個は.txt 拡張子を持つ。
```

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.stream.Stream;

public class FileFinder {
public static void main(String[] args) throws IOException {
    Path startDir = Path.of("/data");

    try (Stream<Path> stream = Files.find(startDir, 3,
            (path, attrs) -> attrs.isRegularFile() && path.toString().endsWith(".txt"))) {
        System.out.println(stream.count());
    }
    }

}
```

```
A. 3
B. 5
C. 10
D. 13
```

```
正解: B
解説:
A. 3 - この数値は、Files.find の第二引数である maxDepth（最大探索深度）の値です。この引数は、ストリームが返す要素の数を直接決定するものではなく、探索する階層の深さを制限するものです。
B. 5 - 正解です。Files.find メソッドは、指定されたディレクトリからストリームを生成し、条件に一致する Path オブジェクトを返します。

startDir: 探索を開始するディレクトリです。

3: maxDepth で、開始ディレクトリからの探索の深さを 3 階層までに制限します。前提条件から、この深さで全てのファイルが探索範囲に含まれると考えられます。

(path, attrs) -> ...: BiPredicate で、各 Path オブジェクトをフィルタリングする条件です。attrs.isRegularFile()で通常のファイルのみを対象とし、path.toString().endsWith(".txt")でファイル名が.txt で終わるものに絞り込んでいます。
前提条件より、これらの条件に一致するファイルは 5 個であるため、stream.count()は 5 を返します。

C. 10 - この数値はディレクトリ配下のファイルの総数です。コード内の BiPredicate は.txt 拡張子を持つファイルのみを通過させるため、全てのファイルがカウントされることはありません。
D. 13 - この数値はファイルとディレクトリの総数です。attrs.isRegularFile()の条件により、ディレクトリは結果から除外されます。

```

22. 問題文: 以下の Map に対する一連の操作を実行した後の map.size()の出力として正しいものはどれですか。

```java

import java.util.HashMap;
import java.util.Map;

public class MapOperations {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("key1", "val1");
        map.put("key2", "val2");
        map.put("key3", null);

        map.compute("key2", (k, v) -> v.concat("-new"));
        map.compute("key3", (k, v) -> "val3");
        map.compute("key4", (k, v) -> k + "-val4");

        System.out.println(map.size());
    }

}

```

```

A. 3
B. 4
C. 5
D. NullPointerException がスローされる

```

```

正解: B
解説:
A. 3 - compute メソッドが新しいキーを追加できることを見落とした場合に、この結果を選択する可能性があります。
B. 4 - 正解です。compute メソッドは、指定されたキーに対するマッピングを計算します。

map.put(...): 3 つのキーバリューペアが追加されます。map のサイズは 3 です。

compute("key2", ...): key2 は存在するため、マッピング関数が適用され、値が"val2-new"に更新されます。サイズは変わりません。

compute("key3", ...): key3 は存在し、値は null です。マッピング関数が適用され、値が"val3"に更新されます。サイズは変わりません。

compute("key4", ...): key4 は存在しません。マッピング関数が適用され、キー"key4"と値"key4-val4"の新しいマッピングが作成されます。これにより、map のサイズが 1 つ増えて 4 になります。

C. 5 - compute の動作を put と混同し、全ての compute 呼び出しで要素が増加すると誤解した場合にこの結果になる可能性があります。
D. NullPointerException がスローされる - compute("key3", ...)の呼び出し時に、マッピング関数の第二引数 v は null になりますが、ラムダ式 (k, v) -> "val3"は v を使用していないため、NullPointerException は発生しません。もしラムダ式が v.concat(...)のような形であれば、この例外が発生したでしょう。

```

23. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java

import java.util.stream.IntStream;

public class StreamShortCircuit {
    public static void main(String[] args) {
        boolean result = IntStream.rangeClosed(1, 10)
        .peek(i -> System.out.print(i + " "))
        .anyMatch(i -> i % 4 == 0);

        System.out.println("\nResult: " + result);
    }

}
```

```
A.
1 2 3 4 5 6 7 8 9 10
Result: true
B.
1 2 3 4
Result: true
C.
4 8
Result: true
D.
1 2 3
Result: false
```

```
正解: B
解説:
A. 1 2 3 4 5 6 7 8 9 10 \nResult: true - anyMatch は短絡評価を行う終端操作です。条件に一致する要素が見つかった時点でストリームの処理を停止するため、全ての要素が処理されることはありません。
B. 1 2 3 4 \nResult: true - 正解です。IntStream.rangeClosed(1, 10)は 1 から 10 までの整数のストリームを生成します。peek は各要素が処理される際にその要素を出力します。anyMatch は、述語（i % 4 == 0）が true を返す最初の要素を見つけると、即座に true を返してストリームの処理を終了します（短絡評価）。

1 が処理され、出力されます。1 % 4 == 0 は false です。

2 が処理され、出力されます。2 % 4 == 0 は false です。

3 が処理され、出力されます。3 % 4 == 0 は false です。

4 が処理され、出力されます。4 % 4 == 0 は true です。
anyMatch は true を返し、ストリームパイプラインはここで停止します。そのため、5 以降の要素は peek に渡されません。

C. 4 8 \nResult: true - peek は anyMatch のような終端操作の前に配置されているため、フィルタリングされた要素だけでなく、判定のために処理された全ての要素に対して作用します。
D. 1 2 3 \nResult: false - anyMatch の条件は 4 で満たされるため、結果が false になることはありません。また、4 が処理されないという点も誤りです。

```

24. 問題文: 以下のディレクトリ構造が存在する前提で、指定された Java コードを実行した際の出力を選択してください。

```
    ディレクトリ構造:
    /app
    |-- config/
    |-- user.properties
    -- data.log
```

```java
コード:
import java.io.IOException;
import java.nio.file.*;
import java.nio.file.attribute.BasicFileAttributes;

public class FileTreeWalker {
    public static void main(String[] args) throws IOException {
        Path start = Path.of("/app");
        Files.walkFileTree(start, new SimpleFileVisitor<>() {
            @Override
            public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
            System.out.print("D-"); // Directory
            return FileVisitResult.CONTINUE;
            }

            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
                System.out.print("F-"); // File
                return FileVisitResult.CONTINUE;
            }

            @Override
            public FileVisitResult postVisitDirectory(Path dir, IOException exc) {
                System.out.print("E-"); // End of Directory
                return FileVisitResult.CONTINUE;
            }
        });
    }
}

```

```

A. D-D-F-E-F-E-
B. D-F-D-F-E-E-
C. D-F-E-D-F-E-
D. D-D-E-F-F-E-

```

```

正解: A
解説:
A. D-D-F-E-F-E- - 正解です。Files.walkFileTree は深さ優先でファイルツリーを探索します。実行フローは以下の通りです。

/app ディレクトリに入る前に preVisitDirectory が呼ばれます。出力: "D-"

/app 内の/config ディレクトリに入る前に preVisitDirectory が呼ばれます。出力: "D-"

/config 内の user.properties ファイルに対して visitFile が呼ばれます。出力: "F-"

/config 内の探索が終わり、ディレクトリを出た後に postVisitDirectory が呼ばれます。出力: "E-"

/app 内に戻り、data.log ファイルに対して visitFile が呼ばれます。出力: "F-"

/app 内の探索が終わり、ディレクトリを出た後に postVisitDirectory が呼ばれます。出力: "E-"
これらを連結すると D-D-F-E-F-E-となります。

B. D-F-D-F-E-E- - この出力は、data.log ファイルを先に訪れてから/config ディレクトリに入るような、幅優先に近い探索順序を示唆していますが、walkFileTree のデフォルトの動作は深さ優先です。
C. D-F-E-D-F-E- - この出力パターンは、各ディレクトリにファイルが 1 つずつあり、それらを独立して処理するような構造を仮定していますが、実際の入れ子構造とは異なります。
D. D-D-E-F-F-E- - この出力は、全てのディレクトリを訪れた後に全てのファイルを訪れるという順序を示唆しており、FileVisitor の各メソッドが呼び出されるタイミングを正しく反映していません。

```

25. 問題文: 以下の CyclicBarrier を使用したコードを実行した結果として、最も可能性が高い出力はどれですか。

```java

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;
import java.util.concurrent.Executors;

public class BarrierTest {
    public static void main(String[] args) {
        var executor = Executors.newFixedThreadPool(4);
        var barrier = new CyclicBarrier(3, () -> System.out.print("Barrier-Action "));

            try {
                for (int i = 0; i < 6; i++) {
                    executor.submit(() -> {
                        try {
                            System.out.print("A ");
                            barrier.await();
                            System.out.print("B ");
                        } catch (InterruptedException | BrokenBarrierException e) {
                            e.printStackTrace();
                        }
                    });
                }
            } finally {
                executor.shutdown();
            }
    }

}
```

```
A. A A A B B B Barrier-Action A A A B B B Barrier-Action
B. A A A Barrier-Action B B B A A A Barrier-Action B B B
C. A B A B A B A B A B A B
D. A A A A A A Barrier-Action Barrier-Action B B B B B B
```

```
正解: B
解説:
A. A A A B B B Barrier-Action A A A B B B Barrier-Action - バリアアクションは、バリアが解放された後に実行されるのではなく、バリアで待機している最後のスレッドによってトリガーされ、待機中のスレッドが再開する前に完了します。
B. A A A Barrier-Action B B B A A A Barrier-Action B B B - 正解です。CyclicBarrier は、指定された数のスレッド（この場合は 3 つ）が await()メソッドを呼び出すまで待機します。3 つのスレッドがバリアに到達すると、バリアアクション（() -> System.out.print("Barrier-Action ")）が実行され、その後、待機していた 3 つのスレッドがすべて解放されて処理を再開します。このプロセスが 2 回繰り返されます。

3 つのスレッドが"A "を出力し、バリアで待機します。

3 番目のスレッドが await()を呼び出した瞬間に、バリアアクションがトリガーされ、"Barrier-Action "が出力されます。

バリアが解放され、待機していた 3 つのス read が"B "を出力します。

残りの 3 つのスレッドで同じサイクルが繰り返されます。
スレッドの実行順序には非決定性がありますが、このパターンが最も論理的で可能性が高いです。

C. A B A B A B A B A B A B - CyclicBarrier は、3 つのスレッドが揃うまで待機させるため、個々のスレッドが A と B を連続して出力することはありません。
D. A A A A A A Barrier-Action Barrier-Action B B B B B B - 6 つのスレッドすべてが await()に到達するまでバリアが待機するわけではありません。バリアは 3 つのスレッドが到達するたびに解放されます。

```

26. 問題文: 以下の ReentrantLock を使用したコードを実行した際に、出力される可能性のある結果としてありえないものはどれですか。

```java

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.TimeUnit;

public class LockTest {
    public static void main(String[] args) throws InterruptedException {
        ReentrantLock lock = new ReentrantLock();
        ExecutorService executor = Executors.newFixedThreadPool(2);

        Runnable task = () -> {
            lock.lock();
            System.out.print("L"); // Locked
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) { /* ignore */ }
            System.out.print("U"); // Unlocking
            lock.unlock();
        };

        executor.submit(task);
        executor.submit(task);

        executor.shutdown();
    }

}

```

```

A. LUL U
B. LU LU
C. LL UU
D. L U L U

```

```

正解: C
解説:
A. LUL U - 1 番目のスレッドがロックを取得し(L)、ロックを解放する(U)前に 2 番目のスレッドがロックを取得しようとして待機します。1 番目のスレッドがロックを解放した直後に 2 番目のスレッドがロックを取得し(L)、その後ロックを解放する(U)という流れは十分に考えられます。
B. LU LU - 1 番目のスレッドがロックを取得し(L)、処理を終えてロックを解放(U)します。その後、2 番目のスレッドがロックを取得し(L)、同様に解放する(U)という逐次的な実行も、スレッドスケジューリングによってはあり得ます。
C. LL UU - 正解です。この選択肢が、ありえない出力です。ReentrantLock は相互排他ロックであり、一度に 1 つのスレッドしかロックを取得できません。1 つのスレッドがロックを取得(L)してから解放(U)するまでの間、他のスレッドは lock.lock()でブロックされます。したがって、L が 2 回連続で出力されることはありません。
D. L U L U - 選択肢 B と同じく、2 つのタスクが完全に逐次的に実行された場合の結果です。これは起こりうるシナリオの一つです。

```

27. 問題文: 以下の java.time API を使用したコードを実行した結果として正しいものはどれですか。

```java

import java.time.LocalDate;
import java.time.Month;
import java.time.Period;

public class PeriodTest {
    public static void main(String[] args) {
        LocalDate startDate = LocalDate.of(2025, Month.JANUARY, 31);
        Period period = Period.ofMonths(1);
        LocalDate endDate = startDate.plus(period);

        System.out.println(endDate);
    }

}
```

```
A. 2025-02-28
B. 2025-03-01
C. 2025-03-03
D. DateTimeException がスローされる
```

```
正解: A
解説:
A. 2025-02-28 - 正解です。LocalDate に Period を加算する場合、java.time API は日付の有効性を考慮して賢明な調整を行います。この例では、2025-01-31 に 1 ヶ月を加算しようとしています。2025 年の 2 月は 28 日までしかありません（うるう年ではない）。そのため、存在しない日付である 2025-02-31 になる代わりに、API はその月の最終日である 2025-02-28 に日付を自動的に調整します。
B. 2025-03-01 - この結果は、単純に 31 日後を計算した場合に近いですが、Period.ofMonths(1)は暦月単位で加算するため、日数を単純に加算するのとは異なります。
C. 2025-03-03 - この日付は、1 ヶ月と 2 日後、あるいは他の複雑な計算の結果ですが、Period.ofMonths(1)の加算ロジックとは一致しません。
D. DateTimeException がスローされる - java.time API は、このような存在しない日付への加算操作を例外をスローするのではなく、その月の有効な最終日に調整することで処理します。したがって、例外は発生しません。

```

28. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java

import java.util.stream.Collectors;
import java.util.List;
import java.util.Map;

record Item(String category, String name) {}

public class ToMapTest {
    public static void main(String[] args) {
        List<Item> items = List.of(
        new Item("Fruit", "Apple"),
        new Item("Vegetable", "Carrot"),
        new Item("Fruit", "Banana")
        );

        try {
            Map<String, String> map = items.stream()
                .collect(Collectors.toMap(
                    Item::category,
                    Item::name
                ));
            System.out.println(map);
        } catch (Exception e) {
            System.out.println(e.getClass().getSimpleName());
        }
    }

}

```

```

A. {Fruit=Banana, Vegetable=Carrot}
B. {Fruit=Apple, Vegetable=Carrot}
C. IllegalStateException
D. {Fruit=[Apple, Banana], Vegetable=[Carrot]}

```

```

正解: C
解説:
A. {Fruit=Banana, Vegetable=Carrot} - この結果は、キーが重複した場合に後の要素の値で上書きされることを示唆していますが、Collectors.toMap のデフォルトの振る舞いではありません。
B. {Fruit=Apple, Vegetable=Carrot} - この結果は、キーが重複した場合に最初の要素の値が保持されることを示唆していますが、これも Collectors.toMap のデフォルトの振る舞いではありません。
C. IllegalStateException - 正解です。Collectors.toMap(keyMapper, valueMapper)を使用する際、ストリーム内にキーマッパーが同じ値を返す要素が複数存在すると（キーの重複）、マージ方法が指定されていないため IllegalStateException がスローされます。このコードでは、"Fruit"というキーを持つ Item が 2 つ存在するため、キーの衝突が発生し、この例外がスローされます。
D. {Fruit=[Apple, Banana], Vegetable=[Carrot]} - この出力形式は、Collectors.groupingBy(Item::category)を使用した場合に生成されるものです。Collectors.toMap は、値をリストにまとめるのではなく、単一の値にマッピングしようとします。

```

29. 問題文: 以下の Text Blocks と String.format メソッドを使用したコードがあります。このコードを実行した結果として正しいものはどれですか。

```java

public class TextBlockFormatting {
    public static void main(String[] args) {
        String template = """
        {
        "name": "%s",
        "id": %d
        }
        """;
        String formatted = String.format(template, "Alice", 101);

        System.out.println(formatted);
    }

}
```

```
A.
{
"name": "Alice",
"id": 101
}
B. {"name":"Alice","id":101}
C. IllegalFormatException がスローされる
D.
{
"name": "%s",
"id": %d
}
```

```
正解: A
解説:
A.
{
"name": "Alice",
"id": 101
}

正解です。Text Blocks は、ソースコード内のインデントや改行を含む複数行の文字列を直感的に表現するための機能です。template 変数は、改行とインデントを含む JSON 形式の文字列を保持します。String.format(template, ...)メソッドは、この template 文字列内のフォーマット指定子（%s と%d）を、指定された引数（"Alice"と 101）に置き換えます。元の文字列の空白や改行は保持されるため、整形された JSON 文字列が出力されます。

B. {"name":"Alice","id":101} - この出力は、Text Blocks が改行や空白を保持しないと誤解した場合の結果です。Text Blocks は、内容的なインデントと改行を文字列の一部として維持します。
C. IllegalFormatException がスローされる - String.format メソッドは、フォーマット指定子の型と提供される引数の型が一致しない場合にこの例外をスローします。このコードでは、%s（文字列）に String 型、%d（整数）に int 型が対応しており、型は一致しているため例外は発生しません。
D.
{
"name": "%s",
"id": %d
}

この出力は、String.format メソッドが機能しなかった場合の結果です。String.format は指定されたテンプレート文字列のフォーマット指定子を正しく置換するため、指定子がそのまま出力されることはありません。

```

30. 問題文: 以下の java.util.stream.Collectors.toList()メソッドを使用したコードがあります。Java 10 以降の環境でこのコードを実行した結果として正しいものはどれですか。

```java
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class CollectorBehavior {
    public static void main(String[] args) {
        List<String> list = Stream.of("a", "b", "c")
        .filter(s -> s.length() < 2)
        .collect(Collectors.toList());

        try {
            list.add("d");
            System.out.println(list);
        } catch (Exception e) {
            System.out.println(e.getClass().getSimpleName());
        }
    }
}

```

```

A. [a, b, c, d]
B. コンパイルエラーが発生する
C. UnsupportedOperationException
D. [a, b, c]

```

```

正解: C
解説:
A. [a, b, c, d] - この出力は、collect(Collectors.toList())が変更可能なリスト（ArrayList など）を返す場合に期待される結果です。しかし、Java 10 以降、このメソッドは変更不可能なリストを返すように変更されました。
B. コンパイルエラーが発生する - List インタフェースには add メソッドが定義されており、list 変数の型は List であるため、list.add("d")の呼び出しは構文的に正しいです。問題はコンパイル時ではなく実行時に発生します。
C. UnsupportedOperationException - 正解です。Java 10 以降、Collectors.toList()が返す List は変更不可能です。つまり、要素の追加や削除といった構造を変更する操作はサポートされていません。そのため、list.add("d")を呼び出すと、実行時に UnsupportedOperationException がスローされます。この例外が catch ブロックで捕捉され、そのクラス名が出力されます。
D. [a, b, c] - add メソッドが例外をスローせずに単に失敗する（サイレントフェイル）ことはありません。サポートされていない操作を呼び出すと、明確に例外がスローされるのが Java コレクションフレームワークの一般的な振る舞いです。

```

31. 問題文: 以下の java.util.concurrent.atomic.AtomicInteger クラスを使用したコードを実行した結果として、最も可能性が高い出力はどれですか。

```java

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.stream.IntStream;

public class AtomicCounter {
    public static void main(String[] args) throws InterruptedException {
        AtomicInteger counter = new AtomicInteger(0);
        ExecutorService executor = Executors.newFixedThreadPool(10);

        IntStream.range(0, 1000).forEach(i ->
            executor.submit(counter::incrementAndGet)
        );

        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.SECONDS);

        System.out.println(counter.get());
    }

}

```

```
A. 1000 未満のいずれかの数値
B. 1000
C. 1000 を超えるいずれかの数値
D. ConcurrentModificationException がスローされる
```

```
正解: B
解説:
A. 1000 未満のいずれかの数値 - この結果は、counter が通常の int 型で、複数のスレッドからのインクリメント操作がアトミック（不可分）でない場合に発生します。いわゆる「失われた更新」の問題ですが、AtomicInteger を使用することでこの問題は防がれます。
B. 1000 - 正解です。AtomicInteger クラスは、整数値に対するアトミックな操作を保証します。incrementAndGet()メソッドは、現在の値を取得し、1 増やし、新しい値を書き込むという一連の操作を、他のスレッドから割り込まれることのない単一の操作として実行します。10 個のスレッドが並行して 1000 回の incrementAndGet 操作を実行しても、全てのインクリメントが正しく反映されるため、最終的なカウンターの値は正確に 1000 になります。
C. 1000 を超えるいずれかの数値 - incrementAndGet は値を 1 ずつ増やす操作であり、1000 回のループで呼び出されているため、最終的な値が 1000 を超えることはありません。
D. ConcurrentModificationException がスローされる - この例外は、コレクションをイテレータで走査中に、そのコレクションが不正に変更された場合にスローされます。このコードは単一の AtomicInteger オブジェクトを操作しているだけで、コレクションの走査は行われていないため、この例外は関係ありません。

```

32. 問題文: 以下の java.util.stream.Stream の dropWhile メソッドを使用したコードがあります。このコードを実行した結果として正しいものはどれですか。

```java
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class DropWhileExample {
    public static void main(String[] args) {
        List<Integer> result = Stream.of(2, 4, 5, 6, 8)
        .dropWhile(n -> n % 2 == 0)
        .collect(Collectors.toList());

        System.out.println(result);
    }

}

```

```

A. [5, 6, 8]
B. [5]
C. []
D. [2, 4]

```

```

正解: A
解説:
A. [5, 6, 8] - 正解です。dropWhile メソッドは、ストリームの先頭から指定された述語（n % 2 == 0）が true である間、要素を破棄し続けます。述語が最初に false を返した要素が見つかると、その要素と後続のすべての要素をストリームに残します。

2 は偶数なので破棄されます。

4 は偶数なので破棄されます。

5 は偶数ではないため、述語は false を返します。この時点で dropWhile の処理は実質的に終了します。

5 と、それ以降のすべての要素（6, 8）が後続のストリームに渡されます。
したがって、最終的なリストは[5, 6, 8]となります。

B. [5] - この結果は、dropWhile が述語に一致しなくなった最初の要素のみを返し、その後ストリームを終了すると誤解した場合に考えられます。dropWhile は、一度条件が満たされなくなると、残りのすべての要素を通過させます。
C. [] - ストリーム内のすべての要素が述語に一致する場合、結果は空のリストになります。しかし、このストリームには奇数である 5 が含まれています。
D. [2, 4] - この結果は、takeWhile メソッドを使用した場合の出力です。takeWhile は dropWhile とは逆の動作をします。

```

33. 問題文: 以下のコードを実行した結果として正しいものはどれですか。

```java

import java.util.NavigableMap;
import java.util.TreeMap;

public class MapSlicing {
    public static void main(String[] args) {
        NavigableMap<Integer, String> map = new TreeMap<>();
        map.put(10, "A");
        map.put(20, "B");
        map.put(30, "C");
        map.put(40, "D");
        map.put(50, "E");

        NavigableMap<Integer, String> subMap = map.subMap(15, false, 45, true);

        System.out.println(subMap);
}

}
```

```
A. {20=B, 30=C, 40=D}
B. {20=B, 30=C, 40=D, 50=E}
C. {30=C, 40=D}
D. {10=A, 20=B, 30=C, 40=D}
```

```
正解: A
解説:
A. {20=B, 30=C, 40=D} - 正解です。TreeMap はキーを自然順序でソートして保持します。subMap(fromKey, fromInclusive, toKey, toInclusive)メソッドは、指定された範囲のキーを持つ部分マップのビューを返します。

fromKey は 15、fromInclusive は false なので、範囲は 15 より大きいキーから始まります。

toKey は 45、toInclusive は true なので、範囲は 45 以下のキーで終わります。
したがって、この範囲(15 < key <= 45)に合致するキーは 20, 30, 40 です。

B. {20=B, 30=C, 40=D, 50=E} - toKey の上限（45）が考慮されていないため、この選択肢は誤りです。キー 50 は範囲外です。
C. {30=C, 40=D} - fromKey の下限（15）の解釈が誤っています。キー 20 は 15 より大きいため、範囲に含まれます。
D. {10=A, 20=B, 30=C, 40=D} - fromInclusive が false であることを見落とし、15 より小さいキー 10 を含めてしまっているため、この選択肢は誤りです。

```

34. 問題文: 以下の Files.mismatch メソッドを使用したコードがあります。このコードを実行した結果として正しいものはどれですか。

```
前提:

fileA.txt の内容は ABCDE です。

fileB.txt の内容は ABCFE です。

両ファイルは同じディレクトリに存在します。
```

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class FileMismatchTest {
    public static void main(String[] args) throws IOException {
        Path pathA = Path.of("fileA.txt");
        Path pathB = Path.of("fileB.txt");

        long mismatchPosition = Files.mismatch(pathA, pathB);

        System.out.println("Mismatch at: " + mismatchPosition);
    }

}

```

```

A. Mismatch at: 2
B. Mismatch at: 3
C. Mismatch at: -1
D. IOException がスローされる

```

```

正解: B
解説:
A. Mismatch at: 2 - インデックス 2 の文字（3 番目の文字）は両ファイルとも C であり、一致しています。mismatch メソッドは、最初に一致しないバイトが見つかった位置を返します。
B. Mismatch at: 3 - 正解です。Files.mismatch メソッドは、2 つのファイルの内容をバイト単位で比較し、最初に異なるバイトが見つかった位置（0 から始まるインデックス）を返します。

インデックス 0: A と A は一致。

インデックス 1: B と B は一致。

インデックス 2: C と C は一致。

インデックス 3: D と F は一致しません。
したがって、最初に不一致が発生する位置はインデックス 3 です。

C. Mismatch at: -1 - -1 は、2 つのファイルの内容が完全に一致する場合に返される値です。この 2 つのファイルは 4 バイト目で内容が異なるため、この値は返されません。
D. IOException がスローされる - Files.mismatch はファイルが存在しない場合などに IOException をスローする可能性がありますが、問題の前提としてファイルは存在し、内容は既知であるため、このコードの実行中に例外が発生するとは考えにくいです。

```

35. 問題文: 以下の java.util.stream.Collectors クラスの summarizingInt メソッドを使用したコードがあります。このコードを実行した結果として正しいものはどれですか。

```java

import java.util.IntSummaryStatistics;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class StatisticsCollector {
    public static void main(String[] args) {
        IntSummaryStatistics stats = Stream.of("apple", "banana", "cherry")
        .collect(Collectors.summarizingInt(String::length));

        System.out.printf("Count=%d, Max=%d", stats.getCount(), stats.getMax());
    }

}

```

```

A. Count=3, Max=5
B. Count=18, Max=6
C. Count=3, Max=18
D. Count=3, Max=6

```

```

正解: D
解説:
A. Count=3, Max=5 - Max の値が誤っています。"apple"の長さは 5 ですが、"banana"と"cherry"の長さは 6 であり、こちらの方が大きいです。
B. Count=18, Max=6 - Count の値が誤っています。18 は長さの合計（sum）であり、要素の数（count）ではありません。
C. Count=3, Max=18 - Max の値が誤っています。18 は長さの合計であり、最大の長さではありません。
D. Count=3, Max=6 - 正解です。Collectors.summarizingInt(String::length)は、ストリームの各要素（文字列）の長さ（整数）に関する要約統計を計算します。

ストリームには "apple" (長さ 5), "banana" (長さ 6), "cherry" (長さ 6) の 3 つの要素があります。

stats.getCount()は要素の数である 3 を返します。

stats.getMax()はこれらの長さの最大値である 6 を返します。
したがって、指定されたフォーマットで Count=3, Max=6 と出力されます。

```

36. 問題文: 以下の java.time.format.DateTimeFormatter を使用したコードがあります。このコードを実行した結果として正しいものはどれですか。

```java

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.util.Locale;

public class DateFormatting {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2025, 10, 5);
        DateTimeFormatter formatter = DateTimeFormatter
        .ofLocalizedDate(FormatStyle.FULL)
        .withLocale(Locale.JAPAN);

        System.out.println(date.format(formatter));
    }

}

```

```

A. 2025 年 10 月 5 日 日曜日
B. 2025/10/05
C. Sunday, October 5, 2025
D. 2025-10-05

```

```

正解: A
解説:
A. 2025 年 10 月 5 日 日曜日 - 正解です。DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL)は、ロケールに固有の最も詳細な日付フォーマットを指定します。.withLocale(Locale.JAPAN)によって、フォーマットのロケールが日本に設定されます。日本の FULL 形式では、年月日と曜日が漢字とかなで表現されるため、この出力が生成されます。2025 年 10 月 5 日は日曜日です。
B. 2025/10/05 - この形式は、FormatStyle.SHORT や FormatStyle.MEDIUM を日本のロケールで使用した場合に近い出力です。FormatStyle.FULL はより詳細な形式を生成します。
C. Sunday, October 5, 2025 - この出力は、Locale.US のような英語圏のロケールで FormatStyle.FULL を使用した場合の結果です。コードでは Locale.JAPAN が指定されているため、日本語のフォーマットが適用されます。
D. 2025-10-05 - この形式は、事前定義フォーマッタの DateTimeFormatter.ISO_LOCAL_DATE を使用した場合、またはパターン文字列"yyyy-MM-dd"を使用した場合の出力です。ローカライズされたフォーマットとは異なります。

```

37. 問題文: 以下の Path オブジェクトに対する一連の操作を実行した後の出力として正しいものはどれですか。

```java

import java.nio.file.Path;

public class PathManipulation {
    public static void main(String[] args) {
        Path p1 = Path.of("/app/data/../logs/log.txt");
        Path p2 = p1.normalize();

        System.out.println(p2.getName(1));
    }

}
```

```
A. logs
B. data
C. app
D. IllegalArgumentException がスローされる
```

```
正解: A
解説:
A. logs - 正解です。まず、p1.normalize()が呼び出されます。normalize()メソッドは、パス文字列から.や..のような冗長な要素を取り除きます。

Path.of("/app/data/../logs/log.txt")は、"/app/logs/log.txt"に正規化されます。これが p2 となります。

p2.getName(1)は、正規化されたパスの 1 番目（0 から数えて）の要素を取得します。

パスの要素は app (index 0), logs (index 1), log.txt (index 2) です。
したがって、インデックス 1 の要素である logs が返されます。

B. data - normalize()メソッドによって data/..の部分が解決されてパスから取り除かれるため、data は最終的なパスの要素にはなりません。
C. app - app はパスの 0 番目の要素です。getName(1)は 1 番目の要素を要求しているため、これは誤りです。
D. IllegalArgumentException がスローされる - normalize()によってパスが有効な形式になり、そのパスにはインデックス 1 の要素が存在するため、この例外はスローされません。インデックスが範囲外（例: 3 以上）の場合にスローされる可能性があります。

```

38. 問題文: 以下のコードを実行した結果に関する記述として、最も適切なものはどれですか。

```java

import java.util.List;
import java.util.Optional;

public class ParallelStreamBehavior {
    public static void main(String[] args) {
        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8);

        Optional<Integer> result = numbers.parallelStream()
            .filter(n -> n > 4)
            .findAny();

        result.ifPresent(System.out::println);
    }

}

```

```

A. コードは常に 5 を出力する
B. findAny は並列ストリームでは使用できないため、コンパイルエラーが発生する
C. 5 から 8 の間のいずれかの数値が出力される可能性があり、実行ごとに結果は異なることがある
D. コードは常に 8 を出力する

```

```

正解: C
解説:
A. コードは常に 5 を出力する - この動作は、findFirst()メソッドを逐次ストリームまたは並列ストリームで使用した場合に期待される結果です。findFirst は常に遭遇順で最初の要素を返そうとします。しかし、findAny は並列実行においてパフォーマンスを優先するため、最初の要素を返す保証はありません。
B. findAny は並列ストリームでは使用できないため、コンパイルエラーが発生する - findAny メソッドは並列ストリームと組み合わせて使用するために設計された操作であり、構文は完全に有効です。したがって、コンパイルエラーは発生しません。
C. 5 から 8 の間のいずれかの数値が出力される可能性があり、実行ごとに結果は異なることがある - 正解です。parallelStream()は、ストリームの要素を複数のスレッドで並行して処理します。findAny()は、フィルタ条件（n > 4）を満たす要素をいずれかのスレッドが最初に見つけた時点で、その要素を返します。どの要素が最初に見つかるかはスレッドのスケジューリングに依存するため、非決定的です。したがって、5, 6, 7, 8 のいずれかが出力される可能性があり、その結果は実行のたびに変わる可能性があります。
D. コードは常に 8 を出力する - findAny が常に最後の要素を返すという保証やロジックはありません。

```

39. 問題文: 以下の PriorityQueue を使用したコードがあります。Task レコードは Comparable を実装しており、priority フィールドの昇順（値が小さいほど優先度が高い）で順序付けられます。このコードを実行した結果として正しいものはどれですか。

```java
import java.util.PriorityQueue;
import java.util.Queue;

record Task(String name, int priority) implements Comparable<Task> {
    @Override
    public int compareTo(Task other) {
        return Integer.compare(this.priority, other.priority);
    }
    @Override
    public String toString() { return this.name; }
}

public class PriorityQueueNaturalOrder {
    public static void main(String[] args) {
        Queue<Task> taskQueue = new PriorityQueue<>();
        taskQueue.offer(new Task("Write Report", 3));
        taskQueue.offer(new Task("Fix Bug", 1));
        taskQueue.offer(new Task("Attend Meeting", 2));

        System.out.print(taskQueue.poll());
        System.out.print(taskQueue.poll());
        System.out.print(taskQueue.poll());
    }

}

```

```

A. Fix BugAttend MeetingWrite Report
B. Write ReportFix BugAttend Meeting
C. Write ReportAttend MeetingFix Bug
D. ClassCastException がスローされる

```

```

正解: A
解説:
A. Fix BugAttend MeetingWrite Report - 正解です。PriorityQueue は、要素を自然順序付け（Comparable インタフェースによる）またはコンストラクタで提供される Comparator に従って順序付けします。この場合、Task は priority フィールドの昇順でソートされます。poll()メソッドは、キューの先頭、つまり優先度が最も高い（priority の値が最も小さい）要素を取得して削除します。したがって、要素は Fix Bug (1)、Attend Meeting (2)、Write Report (3)の順に取り出されます。
B. Write ReportFix BugAttend Meeting - この出力は、要素が追加された順（FIFO）に取り出されることを示唆していますが、PriorityQueue は追加順ではなく優先度順に要素を管理します。
C. Write ReportAttend MeetingFix Bug - この出力は、優先度が降順（値が大きいほど優先度が高い）であると誤解した場合の結果です。
D. ClassCastException がスローされる - この例外は、順序付けできない要素（Comparable を実装していないなど）を PriorityQueue に追加しようとした場合にスローされます。Task レコードは Comparable を正しく実装しているため、この例外は発生しません。

```

40. 問題文: 以下の java.util.stream.Stream の reduce メソッドを使用したコードがあります。このコードを実行した結果として正しいものはどれですか。

```java
import java.util.List;

record Sale(String item, int amount) {}

public class StreamReduceComplex {
    public static void main(String[] args) {
        List<Sale> sales = List.of(
        new Sale("Apple", 200),
        new Sale("Orange", 300),
        new Sale("Banana", 150)
        );

        int totalAmount = sales.stream().reduce(
            0,
            (subtotal, sale) -> subtotal + sale.amount(),
            Integer::sum
        );

        System.out.println(totalAmount);
    }

}

```

```

A. 0
B. 650
C. コンパイルエラーが発生する
D. 150

```

```

正解: B
解説:
A. 0 - 0 は reduce 操作の初期値（単位元）ですが、ストリームの各要素が正しく累算されるため、最終結果が 0 になることはありません。
B. 650 - 正解です。このコードは、Stream<Sale>から Integer 型の結果を得るために、3 つの引数を取る reduce メソッドを使用しています。

0: identity（単位元）。計算の初期値です。

(subtotal, sale) -> subtotal + sale.amount(): accumulator（累算器）。現在の部分合計 subtotal と次の Sale オブジェクトを受け取り、新しい部分合計を返します。

Integer::sum: combiner（結合器）。並列ストリームで部分合計を結合するために使用されますが、この逐次ストリームでは使用されません。
処理は ((0 + 200) + 300) + 150 と進み、最終的な合計は 650 になります。

C. コンパイルエラーが発生する - この 3 引数の reduce メソッドは、ストリームの要素の型（Sale）と結果の型（Integer）が異なる場合に一般的に使用される有効な形式です。累算器のラムダ式の引数の型（(Integer, Sale)）も正しく推論されるため、コンパイルエラーは発生しません。
D. 150 - 150 はリスト内の最後の Sale オブジェクトの amount ですが、reduce はストリームの全ての要素を累算するため、最後の要素の値だけが結果になるわけではありません。

```

41. 問題文: 以下の並列ストリームを使用したコードがあります。このコードの実行結果に関する記述として、最も適切なものはどれですか。

```java

import java.util.ArrayList;
import java.util.List;

public class ParallelStatefulLambda {
    public static void main(String[] args) {
        List<Integer> sourceList = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        List<Integer> resultList = new ArrayList<>();

        sourceList.parallelStream()
            .map(i -> {
                resultList.add(i); // ステートフルなラムダ
                return i;
            })
            .forEach(i -> {}); // 処理を起動するための終端操作

        System.out.println(resultList.size());
    }

}
```

```
A. コードは常に 10 を出力する
B. forEach 内のラムダが空であるため、コンパイルエラーが発生する
C. 実行時に常に ConcurrentModificationException がスローされる
D. 結果は非決定的であり、10 未満の数値を出力するか、実行時例外をスローする可能性がある
```

```
正解: D
解説:
A. コードは常に 10 を出力する - ArrayList はスレッドセーフではないため、複数のスレッドから同時に add メソッドを呼び出すと競合状態（レースコンディション）が発生します。これにより、一部の追加操作が失われる可能性があるため、サイズが常に 10 になる保証はありません。
B. forEach 内のラムダが空であるため、コンパイルエラーが発生する - ラムダ式の中身が空であることは構文的に有効です。この forEach は、ストリームの中間操作を実行させるためだけの役割を果たしており、コンパイルエラーの原因にはなりません。
C. 実行時に常に ConcurrentModificationException がスローされる - ConcurrentModificationException は、コレクションの反復処理中にそのコレクションが変更された場合にスローされることが一般的です。このシナリオで発生する可能性があるのは、競合状態による ArrayIndexOutOfBoundsException などですが、例外の発生自体が保証されているわけではありません。
D. 結果は非決定的であり、10 未満の数値を出力するか、実行時例外をスローする可能性がある - 正解です。parallelStream は内部で複数のスレッドを使用して処理を行います。スレッドセーフではない ArrayList に対して、これらのスレッドが同時に add 操作を行うと競合状態が発生します。この結果、一部の要素の追加が失われてリストの最終的なサイズが 10 未満になったり、リストの内部状態が破壊されて ArrayIndexOutOfBoundsException のような予期せぬ実行時例外がスローされたりする可能性があります。したがって、実行結果は非決定的です。

```

42. 問題文: 以下の java.util.stream.Stream の flatMapToInt メソッドを使用したコードがあります。このコードを実行した結果として正しいものはどれですか。（文字'a'の ASCII 値は 97、'b'は 98、'c'は 99 とします）

```java
import java.util.List;

public class StreamFlattenPrimitive {
    public static void main(String[] args) {
        List<String> data = List.of("a", "bc");

        long sum = data.stream()
            .flatMapToInt(String::chars)
            .sum();

        System.out.println(sum);
    }

}

```

```

A. 294
B. 3
C. 197
D. コンパイルエラーが発生する

```

```

正解: A
解説:
A. 294 - 正解です。flatMapToInt メソッドは、各要素を IntStream にマップし、それらのストリームを 1 つの IntStream に連結します。String::chars メソッドは、文字列の各文字の int 値（文字コード）からなる IntStream を返します。

"a"は文字コード 97 を持つ IntStream になります。

"bc"は文字コード 98 と 99 を持つ IntStream になります。

これらが連結され、[97, 98, 99]という単一の IntStream が生成されます。

sum()メソッドはこれらの値を合計するため、97 + 98 + 99 = 294 となります。

B. 3 - この数値は、flatMapToInt によって生成された IntStream の要素数（count）です。問題では合計（sum）を求めているため、これは誤りです。
C. 197 - この数値は、"bc"の文字コードの合計（98 + 99 = 197）ですが、"a"の文字コードが含まれていません。
D. コンパイルエラーが発生する - flatMapToInt と String::chars メソッド参照の組み合わせは、Stream<String>を IntStream に変換するための有効で一般的な方法です。コードは構文的に正しく、コンパイルエラーは発生しません。

```

43. 問題文: 以下のディレクトリ構造が存在し、指定された Java コードを実行したとします。実行結果として最も適切なものはどれですか。

```
    ディレクトリ構造:
    app/
    ├── data/
    │ ├── file1.txt
    │ └── report.csv
    └── logs/
    ├── app.log
    └── old_log.txt
```

```java
import java.io.IOException;
import java.nio.file.*;
import java.nio.file.attribute.BasicFileAttributes;

public class FileTreeWalker {
    public static void main(String[] args) throws IOException {
        Path start = Paths.get("app");
        Files.walkFileTree(start, new SimpleFileVisitor<>() {
            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
                if (file.getFileName().toString().equals("report.csv")) {
                    System.out.println("File found: " + file);
                    return FileVisitResult.TERMINATE;
                }
                return FileVisitResult.CONTINUE;
            }

            @Override
            public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
                System.out.println("Visiting dir: " + dir);
                return FileVisitResult.CONTINUE;
            }
        });
    }
}
```

```
A. 下記の内容が順不同で出力される。
Visiting dir: app
Visiting dir: app/data
File found: app/data/report.csv
Visiting dir: app/logs
B. 下記の内容がこの順序で出力される。
Visiting dir: app
Visiting dir: app/data
Visiting dir: app/logs
File found: app/data/report.csv
C. 下記の内容がこの順序で出力される。
Visiting dir: app
Visiting dir: app/data
File found: app/data/report.csv
D. visitFile メソッド内で TERMINATE を返しているため、コンパイルエラーが発生する。
```

```
正解: C
解説:
A. 下記の内容が順不同で出力される。
Visiting dir: app
Visiting dir: app/data
File found: app/data/report.csv
Visiting dir: app/logs

Files.walkFileTree はファイルツリーを深さ優先で探索するため、出力順序は一定になります。また、report.csv を見つけた時点で TERMINATE が返されるため、app/logs ディレクトリが訪問されることはありません。

B. 下記の内容がこの順序で出力される。
Visiting dir: app
Visiting dir: app/data
Visiting dir: app/logs
File found: app/data/report.csv

FileVisitResult.TERMINATE を返すと、ファイルツリーの走査はその時点で即座に終了します。app/data/report.csv を訪問した後に app/logs ディレクトリを訪問することはありません。

C. 下記の内容がこの順序で出力される。
Visiting dir: app
Visiting dir: app/data
File found: app/data/report.csv

正解です。walkFileTree はまず app ディレクトリに入り (Visiting dir: app を出力）、次に app/data ディレクトリに入ります (Visiting dir: app/data を出力）。その後、app/data 内のファイルを訪問し、report.csv を見つけた時点で File found: app/data/report.csv を出力し、FileVisitResult.TERMINATE を返します。これにより、それ以降のファイルとディレクトリの走査（app/logs ディレクトリの訪問など）はすべて中止されます。

D. visitFile メソッド内で TERMINATE を返しているため、コンパイルエラーが発生する。

visitFile メソッドの戻り値の型は FileVisitResult であり、FileVisitResult.TERMINATE はその有効な enum 定数の一つです。したがって、このコードは文法的に正しく、コンパイルエラーは発生しません。

```

44. 問題文: 以下のコードは、ExecutorService を使用して非同期にタスクを実行し、その結果を取得することを目的としています。1 から 10 までの整数の合計を計算し、その結果をコンソールに出力するコードとして、正しくコンパイルされ、意図通りに動作するものはどれですか。なお、コードは必要な java.util.concurrent.\*クラスがインポートされており、main メソッドは Exception をスローするシグネチャを持つものとします。

```java
A.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<Integer> future = es.execute(() -> {
    int sum = 0;
    for (int i = 1; i <= 10; i++) { sum += i; }
    return sum;
});
System.out.println(future.get());
es.shutdown();
```

```java
B.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<?> future = es.submit(() -> {
    int sum = 0;
    for (int i = 1; i <= 10; i++) { sum += i; }
    // No return value
});
System.out.println(future.get());
es.shutdown();
```

```java
C.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<Integer> future = es.submit(() -> {
    int sum = 0;
    for (int i = 1; i <= 10; i++) { sum += i; }
    return sum;
});
System.out.println(future.get());
es.shutdown();
```

```java
D.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<Integer> future = es.submit(() -> {
    int sum = 0;
    for (int i = 1; i <= 10; i++) { sum += i; }
});
System.out.println(future.get());
es.shutdown();

```

```

正解: C
解説:
A.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<Integer> future = es.execute(() -> {
int sum = 0;
for (int i = 1; i <= 10; i++) { sum += i; }
return sum;
});
System.out.println(future.get());
es.shutdown();

この選択肢はコンパイルエラーになります。ExecutorService の execute メソッドは Runnable オブジェクトを引数にとり、戻り値は void です。そのため、Future オブジェクトを返すことはなく、future 変数に結果を代入しようとしている箇所でエラーが発生します。
B.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<?> future = es.submit(() -> {
int sum = 0;
for (int i = 1; i <= 10; i++) { sum += i; }
// No return value
});
System.out.println(future.get());
es.shutdown();

この選択肢は、コンソールに null を出力します。submit メソッドに渡されたラムダ式は値を返さないため、Runnable として扱われます。submit(Runnable)は Future<?>を返しますが、タスクの完了を示すだけで結果は保持しません。そのため、future.get()はタスク完了後に null を返します。
C.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<Integer> future = es.submit(() -> {
int sum = 0;
for (int i = 1; i <= 10; i++) { sum += i; }
return sum;
});
System.out.println(future.get());
es.shutdown();

正解です。このコードでは、submit メソッドに戻り値を持つラムダ式（Callable インターフェースの実装）を渡しています。submit メソッドは Future<Integer>オブジェクトを返し、その get()メソッドを呼び出すことで、別スレッドで実行されたタスクの計算結果（55）をブロックして取得し、正しく出力します。
D.
ExecutorService es = Executors.newSingleThreadExecutor();
Future<Integer> future = es.submit(() -> {
int sum = 0;
for (int i = 1; i <= 10; i++) { sum += i; }
});
System.out.println(future.get());
es.shutdown();

この選択肢はコンパイルエラーになります。submit メソッドに渡されたラムダ式には return 文がありません。そのため、このラムダ式は Callable<Integer>ではなく Runnable として型推論されます。Runnable を引数にとる submit メソッドの戻り値は Future<?>型（実質的には Future<Void>）であり、Future<Integer>型の変数 future に代入しようとしているため、型の不一致でエラーが発生します。

```

45. 問題文: `java.nio.file` パッケージを使用してファイル操作を行います。ディレクトリ`/app/source に data.log` ファイルが存在し、ディレクトリ/app/target にも同名の data.log ファイルが存在する状況を考えます。`/app/source/data.log` を`/app/target` ディレクトリに移動させ、既存の`/app/target/data.log` を上書きするコードとして、正しく動作するものはどれですか。なお、必要な `java.nio.file.*`クラスはインポート済みで、メソッドがスローする可能性のあるチェック例外は適切に処理されているものとします。

```java
A.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.move(source, targetDir.resolve(source.getFileName()), StandardCopyOption.REPLACE_EXISTING);

B.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.copy(source, targetDir, StandardCopyOption.REPLACE_EXISTING);

C.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.move(source, targetDir.resolve(source.getFileName()));

D.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.move(source, targetDir);
```

```
正解: A
解説:
A.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.move(source, targetDir.resolve(source.getFileName()), StandardCopyOption.REPLACE_EXISTING);

正解です。Files.move メソッドはファイルの移動を行います。第二引数で targetDir.resolve(source.getFileName())とすることで、移動先のフルパス(/app/target/data.log)を正しく指定しています。第三引数に StandardCopyOption.REPLACE_EXISTING オプションを指定しているため、移動先に同名のファイルが存在しても上書きされ、要件を満たします。
B.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.copy(source, targetDir, StandardCopyOption.REPLACE_EXISTING);

この選択肢は、ファイルの「移動」ではなく「コピー」を行います。Files.copy メソッドは元のファイルを削除しないため、/app/source/data.log が残ってしまいます。問題の要件である「移動」とは異なる操作です。
C.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.move(source, targetDir.resolve(source.getFileName()));

この選択肢は、実行時に FileAlreadyExistsException をスローします。Files.move メソッドは、上書きを許可するオプションが指定されていない場合、移動先に同名のファイルが存在すると操作を失敗させ、例外を発生させます。
D.
Path source = Paths.get("/app/source/data.log");
Path targetDir = Paths.get("/app/target");
Files.move(source, targetDir);

この選択肢は、実行時に IOException（またはそのサブクラスである FileAlreadyExistsException など）をスローします。Files.move メソッドの第二引数には、移動先の「ファイルパス」を指定する必要があります。ディレクトリのパスを指定した場合、ファイルをディレクトリとして上書きすることはできないため、操作は失敗します。

```

46. 問題文: DriverManager.getConnection()メソッドが有効な Connection オブジェクトを返すと仮定します。JDBC を使用してデータベースから従業員情報を取得する際、リソースリークを防ぐために Connection, PreparedStatement, ResultSet を確実にクローズするための、最も堅牢で推奨されるコードはどれですか。

```java

A.
Connection con = null;
PreparedStatement ps = null;
ResultSet rs = null;
try {
    con = DriverManager.getConnection(URL);
    ps = con.prepareStatement("SELECT _ FROM employees");
    rs = ps.executeQuery();
    while (rs.next()) { /_ ... \*/ }
} catch (SQLException e) {
    e.printStackTrace();
} finally {
    try { if (rs != null) rs.close(); } catch (SQLException e) { e.printStackTrace(); }
    try { if (ps != null) ps.close(); } catch (SQLException e) { e.printStackTrace(); }
    try { if (con != null) con.close(); } catch (SQLException e) { e.printStackTrace(); }
}
```

```java
B.
try (Connection con = DriverManager.getConnection(URL);
PreparedStatement ps = con.prepareStatement("SELECT * FROM employees")) {
    try (ResultSet rs = ps.executeQuery()) {
        while (rs.next()) {
        // ...
        }
    }
} catch (SQLException e) {
    e.printStackTrace();
}
```

```java
C.
ResultSet rs = null;
try (Connection con = DriverManager.getConnection(URL);
PreparedStatement ps = con.prepareStatement("SELECT * FROM employees")) {
    rs = ps.executeQuery();
    while (rs.next()) {
    // ...
    }
} catch (SQLException e) {
    e.printStackTrace();
}
```

```java
D.
try (Connection con = DriverManager.getConnection(URL);
PreparedStatement ps = con.prepareStatement("SELECT * FROM employees");
    ResultSet rs = ps.executeQuery()) {
    while (rs.next()) {
    // ...
    }
} catch (SQLException e) {
    e.printStackTrace();
}

```

```

正解: B
解説:
A.
Connection con = null;
PreparedStatement ps = null;
ResultSet rs = null;
try {
con = DriverManager.getConnection(URL);
ps = con.prepareStatement("SELECT _ FROM employees");
rs = ps.executeQuery();
while (rs.next()) { /_ ... \*/ }
} catch (SQLException e) {
e.printStackTrace();
} finally {
try { if (rs != null) rs.close(); } catch (SQLException e) { e.printStackTrace(); }
try { if (ps != null) ps.close(); } catch (SQLException e) { e.printStackTrace(); }
try { if (con != null) con.close(); } catch (SQLException e) { e.printStackTrace(); }
}

この選択肢は、Java 7 より前の古い形式のリソース管理方法です。コードが冗長であり、finally ブロックの記述が複雑になりがちです。現代の Java 開発においては、より簡潔で安全な try-with-resources 構文の使用が推奨されるため、最も適切なコードとは言えません。
B.
try (Connection con = DriverManager.getConnection(URL);
PreparedStatement ps = con.prepareStatement("SELECT \* FROM employees")) {
try (ResultSet rs = ps.executeQuery()) {
while (rs.next()) {
// ...
}
}
} catch (SQLException e) {
e.printStackTrace();
}

正解です。ネストされた try-with-resources 構文を利用しています。Connection と PreparedStatement が外側の try ブロックで、ResultSet が内側の try ブロックで管理されています。この方法により、ps.executeQuery()で例外が発生した場合でも、con と ps が確実にクローズされます。これは、すべてのリソースを安全かつ確実に閉じるための最も堅牢な方法です。
C.
ResultSet rs = null;
try (Connection con = DriverManager.getConnection(URL);
PreparedStatement ps = con.prepareStatement("SELECT \* FROM employees")) {
rs = ps.executeQuery();
while (rs.next()) {
// ...
}
} catch (SQLException e) {
e.printStackTrace();
}

この選択肢では、ResultSet オブジェクトが try-with-resources 構文の管理対象外です。try ブロック内で rs が初期化されていますが、ブロックを抜ける際に自動的に close()メソッドが呼び出されないため、リソースリークが発生する可能性があります。
D.
try (Connection con = DriverManager.getConnection(URL);
PreparedStatement ps = con.prepareStatement("SELECT \* FROM employees");
ResultSet rs = ps.executeQuery()) {
while (rs.next()) {
// ...
}
} catch (SQLException e) {
e.printStackTrace();
}

この選択肢は一見正しく見えますが、潜在的なリソースリークの問題があります。もし con.prepareStatement は成功し、ps.executeQuery が失敗して例外をスローした場合、生成された PreparedStatement オブジェクト ps がクローズされない可能性があります。ResultSet は PreparedStatement に依存するため、分けて管理するのがより安全です。

```

47. 問題文: 以下の Java コードを実行したとき、標準出力に表示される結果はどれですか。

```java

import java.util.List;
import java.util.Optional;

public class StreamApiQuestion {
    public static void main(String[] args) {
        List<String> fruits = List.of("Apple", "Orange", "Banana", "Apricot");
        Optional<String> result = fruits.stream()
        .filter(s -> s.startsWith("A"))
        .map(String::toLowerCase)
        .reduce((s1, s2) -> s1 + "-" + s2);

        result.ifPresent(System.out::println);
    }

}
```

```
A. apple-apricot
B. Apple-Apricot
C. Optional[apple-apricot]
D. apple,apricot
```

```
正解: A
解説:
A. apple-apricot - 正解です。このコードは、まず List からストリームを生成します。filter メソッドで"A"から始まる文字列（"Apple", "Apricot"）を抽出し、map メソッドでそれらを小文字（"apple", "apricot"）に変換します。最後に reduce メソッドで、変換された文字列をハイフン("-")で連結します。reduce 操作は Optional を返すため、ifPresent メソッドを使用して結果が存在する場合にのみ標準出力に出力されます。
B. Apple-Apricot - map(String::toLowerCase)の中間操作により、各要素は小文字に変換されます。そのため、大文字のまま連結されることはありません。
C. Optional[apple-apricot] - result.ifPresent(System.out::println)は、Optional オブジェクトが値を持つ場合にその値（この場合は連結された文字列）を出力します。Optional オブジェクト自体の文字列表現が出力されるわけではありません。
D. apple,apricot - reduce メソッドの引数として渡されたラムダ式 (s1, s2) -> s1 + "-" + s2 は、要素をハイフンで連結します。カンマで連結されることはありません。

```

48. 問題文: 以下のコードを実行した結果として、標準出力に出力されるものはどれですか。

```java

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.ExecutionException;

public class ParallelProcessingQuiz {
    public static void main(String[] args) {
        ExecutorService executor = null;
        try {
            executor = Executors.newSingleThreadExecutor();
            Future<String> future = executor.submit(() -> {
                throw new IllegalStateException("Execution error");
            });
            String result = future.get();
            System.out.println(result);
        } catch (ExecutionException e) {
            System.out.println(e.getCause().getClass().getSimpleName());
        } catch (Exception e) {
            System.out.println(e.getClass().getSimpleName());
        } finally {
            if (executor != null) {
                executor.shutdown();
            }
        }
    }
}

```

```

A. ExecutionException
B. IllegalStateException
C. Exception
D. コンパイルエラーが発生する

```

```

正解: B
解説:
A. ExecutionException - 不正解です。future.get()メソッドは ExecutionException をスローしますが、この例外は try ブロック内の最初の catch 節で捕捉されます。しかし、catch ブロック内では e.getCause()メソッドを呼び出しており、これは ExecutionException の原因となった元の例外（この場合は IllegalStateException）を取得します。そのため、出力されるのは ExecutionException そのもののクラス名ではありません。
B. IllegalStateException - 正解です。ExecutorService にサブミットされた Callable タスク内で IllegalStateException がスローされます。この例外は ExecutionException にラップされて、future.get()の呼び出し元にスローされます。最初の catch (ExecutionException e)ブロックでこの例外が捕捉され、e.getCause()によって元の IllegalStateException オブジェクトが取り出されます。そして、.getClass().getSimpleName()によってそのクラス名である"IllegalStateException"が出力されます。
C. Exception - 不正解です。ExecutionException は Exception のサブクラスですが、より具体的な catch (ExecutionException e)ブロックが先に記述されているため、そちらで捕捉されます。2 番目の catch (Exception e)ブロックが実行されることはありません。
D. コンパイルエラーが発生する - 不正解です。このコードは構文的に正しく、スローされる可能性のあるチェック例外（ExecutionException や InterruptedException）も適切に処理されているため、コンパイルエラーは発生しません。

```

49. 問題文: Locale オブジェクトの生成に関する説明として、最も適切なものはどれですか。

```java
A. new Locale("ja", "JP")は、言語コードのみを指定してインスタンスを生成する。
B. Locale.Builder を使用して、言語、スクリプト、国などの要素を段階的に設定してインスタンスを生成できる。
C. Locale.getDefault()は、常に en-US に対応する Locale オブジェクトを返す。
D. Locale クラスのコンストラクタは、引数として渡された文字列が有効なコードであるかを検証する。
```

```
正解: B
解説:
A. new Locale("ja", "JP")は、言語コードのみを指定してインスタンスを生成する。 - 不正解です。このコンストラクタは第 1 引数に言語コード("ja")を、第 2 引数に国コード("JP")を指定してインスタンスを生成します。言語コードのみを指定するコンストラクタは new Locale("ja")です。
B. Locale.Builder を使用して、言語、スクリプト、国などの要素を段階的に設定してインスタンスを生成できる。 - 正解です。Locale.Builder クラスは、setLanguage、setScript、setRegion などのメソッドチェーンを用いて、Locale オブジェクトを柔軟かつ安全に構築するメカニズムを提供します。
C. Locale.getDefault()は、常に en-US に対応する Locale オブジェクトを返す。 - 不正解です。Locale.getDefault()は、Java 仮想マシンが実行されているホスト環境のデフォルトロケールを返します。この値は OS の設定に依存するため、環境によって異なります。
D. Locale クラスのコンストラクタは、引数として渡された文字列が有効なコードであるかを検証する。 - 不正解です。Locale クラスのコンストラクタは、渡された文字列が ISO 639（言語）や ISO 3166（国）に準拠しているかどうかの厳密な検証を行いません。不正な形式のコードでもインスタンスは生成されますが、意図通りに動作しない可能性があります。

```

50. 問題文: 2 つのモジュール com.data と com.report があります。com.data モジュールは com.data.model パッケージを外部に公開しています。com.report モジュールが com.data.model パッケージ内のクラスを利用するためには、それぞれの module-info.java ファイルにどのような宣言が必要ですか。

```java

A. com.data: opens com.data.model; と com.report: requires com.data;
B. com.data: exports com.data.model; と com.report: requires com.data;
C. com.data: exports com.data.model; と com.report: imports com.data.model;
D. com.data: requires com.report; と com.report: exports com.data.model;

```

```

正解: B
解説:
A. com.data: opens com.data.model; と com.report: requires com.data; - 不正解です。opens キーワードは、特定のパッケージをリフレクション（実行時の動的なアクセス）のために公開します。コンパイル時の通常のクラス利用のためには exports キーワードが必要です。
B. com.data: exports com.data.model; と com.report: requires com.data; - 正解です。Java モジュールシステムでは、あるモジュールが別のモジュールのパッケージを利用するためには、提供側が exports ディレクティブでパッケージを明示的に公開し、利用側が requires ディレクティブでそのモジュールへの依存関係を宣言する必要があります。
C. com.data: exports com.data.model; と com.report: imports com.data.model; - 不正解です。module-info.java ファイルにおいて、モジュール間の依存関係を定義するために imports というキーワードは使用されません。import は Java のソースファイル内でクラスをインポートするために使用されるものです。
D. com.data: requires com.report; と com.report: exports com.data.model; - 不正解です。依存関係の方向が逆です。com.report モジュールが com.data モジュールを利用するため、com.data がパッケージを exports し、com.report が com.data を requires する必要があります。

```

51. 問題文: 以下のコードを実行した結果として、コンソールに出力される値はどれですか。

```java

import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Integer> numbers = List.of(5, 2, 8, 1);
        Integer result = numbers.stream()
        .reduce(2, (a, b) -> a * b);
        System.out.println(result);
    }
}
```

```
A. 160
B. 80
C. 162
D. 2
```

```
正解: A
解説:
A. 160 - 正解です。reduce メソッドは、ストリームの要素を結合して単一の結果を生成します。このコードでは、初期値として 2 が指定されています。最初の演算は 2 _ 5 で 10、次に 10 _ 2 で 20、次に 20 _ 8 で 160、最後に 160 _ 1 で 160 となります。
B. 80 - reduce 操作の初期値を無視した場合、5 _ 2 _ 8 \* 1 という計算になり、結果は 80 となります。しかし、このコードでは初期値 2 が提供されているため、計算に含める必要があります。
C. 162 - reduce の第二引数は BiFunction であり、a が累積値、b がストリームの要素です。もし a + b のような加算処理と誤解した場合、2 + 5 + 2 + 8 + 1 の結果は 18 となり、この選択肢にはなりません。この選択肢は、特定の計算間違いを誘うものです。
D. 2 - この値は reduce 操作に与えられた初期値です。ストリームが空の場合、reduce 操作は初期値を返しますが、このストリームには要素が含まれているため、各要素に対して演算が実行されます。

```

52. 問題文: employees という名前のテーブルに、id が 101、name が'Tanaka'のレコードが存在します。以下の JDBC コードが正常に実行された後、このレコードの name の値はどうなりますか。

```java
import java.sql.*;

public class Main {
    public static void main(String[] args) {
        // DriverManager.getConnection()は有効な Connection を返すと仮定
        String url = "jdbc:yourdatabase:yourdb";
        String user = "user";
        String password = "password";

        String sql = "UPDATE employees SET name = ? WHERE id = ?";

        try (Connection conn = DriverManager.getConnection(url, user, password);
             PreparedStatement pstmt = conn.prepareStatement(sql)) {

            pstmt.setString(1, "Suzuki");
            pstmt.setInt(2, 101);
            // executeUpdate()の呼び出しを意図的に省略

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

}

```

```

A. Suzuki
B. null
C. Tanaka
D. 空文字

```

```

正解: C
解説:
A. Suzuki - PreparedStatement オブジェクトのセッターメソッド（setString や setInt）は、SQL 文のパラメータを設定するだけで、データベースへの問い合わせを実際には実行しません。更新を実行するには executeUpdate()メソッドを呼び出す必要があります。
B. null - UPDATE 文が実行されていないため、name カラムが null に変更されることはありません。null が設定されるのは、pstmt.setNull()が使われ、かつ executeUpdate()が呼び出された場合です。
C. Tanaka - 正解です。このコードでは PreparedStatement オブジェクトが作成され、setString()と setInt()によってパラメータが設定されていますが、データベースに対して更新を実行する executeUpdate()メソッドが呼び出されていません。そのため、データベース上のデータは一切変更されず、'Tanaka'のままです。
D. 空文字 - name が空文字になるのは、pstmt.setString(1, "")のように設定され、executeUpdate()が呼び出された場合です。このコードでは更新自体が行われないため、値は変更されません。

```

53. 問題文: 以下のコードでは、java.time.LocalDate と java.time.format.DateTimeFormatter を使用して日付の書式設定を行っています。このコードを実行した結果、コンソールに出力される内容はどれですか。

```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class Main {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2025, 1, 5);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd E");
        System.out.println(date.format(formatter));
    }
}

```

```
A. 2025/01/05 Sun
B. 2025/1/5 日
C. 2025/01/05 日
D. 2025/01/05 Sunday
```

```
正解: C
解説:
A. 2025/01/05 Sun - DateTimeFormatter はロケールに依存します。デフォルトのロケールが英語圏の場合、曜日は Sun と出力される可能性がありますが、日本のロケール環境で実行した場合の挙動とは異なります。パターン文字 E はロケールに応じた曜日の短縮形を出力します。
B. 2025/1/5 日 - パターン文字 MM と dd は、それぞれ月と日を 2 桁で表現することを示します。したがって、1 月 5 日は 01 と 05 のようにゼロパディングされて出力されます。この選択肢はゼロパディングされていないため、不正確です。
C. 2025/01/05 日 - 正解です。LocalDate.of(2025, 1, 5)は日曜日です。yyyy は年を 4 桁、MM は月を 2 桁（ゼロパディングあり）、dd は日を 2 桁（ゼロパディングあり）でフォーマットします。E はロケールに合わせた曜日の短縮形を出力し、日本のデフォルトロケールでは日となります。
D. 2025/01/05 Sunday - パターン文字 E は曜日の短縮形を出力します。Sunday のような完全な曜日名を出力するには、EEEE のようにパターン文字を 4 つ使用する必要があります。

```

54. 問題文: 以下の ExecutorService を利用したコードを実行したとき、コンソールに出力される可能性が最も高いものはどれですか。

```java

import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws Exception {
        ExecutorService service = Executors.newSingleThreadExecutor();
        try {
            Future<String> future = service.submit(() -> {
                System.out.print("Task");
                return "Complete";
            });
            System.out.print("Main");
            String result = future.get();
            System.out.print(result);
        } finally {
            service.shutdown();
        }
    }
}

```

```

A. MainTaskComplete
B. TaskMainComplete
C. MainComplete
D. TaskComplete

```

```

正解: A
解説:
A. MainTaskComplete - 正解です。service.submit()はタスクをワーカースレッドに投入し、即座に Future オブジェクトを返します。メインスレッドはブロックされずに次の行に進み、"Main"を出力します。その後 future.get()が呼び出されると、メインスレッドはタスクが完了するまで待機（ブロック）します。その間にワーカースレッドが"Task"を出力し、"Complete"を返します。タスク完了後、future.get()が結果を返し、最後に"Complete"が出力されます。
B. TaskMainComplete - submit()メソッドは非同期であるため、タスクの実行とメインスレッドの処理は並行して進みます。メインスレッドが future.get()で待機する前に"Main"を出力するため、"Task"が先に表示される可能性は低いと考えられます。
C. MainComplete - submit()に渡された Callable タスクは System.out.print("Task")という処理を含んでいます。future.get()はタスクの完了を待つため、この処理は結果が取得される前に必ず実行されます。したがって、"Task"という文字列が出力に含まれます。
D. TaskComplete - メインスレッドの System.out.print("Main")という処理が future.get()の呼び出しより前に行われます。そのため、"Main"という文字列が出力に含まれないこの選択肢は不正確です。

```

55. 問題文: 以下のコードスニペットについて、Book レコードと Main クラスが定義されています。このコードを実行した結果、コンソールに出力される内容はどれですか。

```java
// Book.java
public record Book(String title, String author) {}

// Main.java
import java.util.stream.Stream;
import java.util.stream.Collectors;
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        Stream<Book> books = Stream.of(
        new Book("Tale A", "Author X"),
        new Book("Tale B", "Author Y"),
        new Book("Tale C", "Author X")
        );

        Map<String, String> map = books.collect(
            Collectors.toMap(Book::author, Book::title, (t1, t2) -> t1 + ", " + t2)
        );

        System.out.println(map);
    }

}

```

```

A. {Author Y=Tale B, Author X=Tale A}
B. {Author Y=Tale B, Author X=Tale A, Tale C}
C. {Author Y=Tale B, Author X=Tale A, Tale C}のような形式で、実行時にキーの順序が不定なもの
D. {Author Y=Tale B, Author X=Tale A, Tale C}

```

```

正解: C
解説:
A. {Author Y=Tale B, Author X=Tale A} - Collectors.toMap に渡されたマージ関数(t1, t2) -> t1 + ", " + t2 は、重複するキー（この場合は "Author X"）が見つかったときに、どのように値を結合するかを定義します。この選択肢は、重複キーの後から来た値が無視されたかのような結果を示しており、マージ関数が適用されていないため不正確です。
B. {Author Y=Tale B, Author X=Tale A, Tale C} - この選択肢は、値の結合方法が不正です。マージ関数 t1 + ", " + t2 に従い、"Tale A"と"Tale C"はカンマ区切りで結合されるべきです。
C. {Author Y=Tale B, Author X=Tale A, Tale C}のような形式で、実行時にキーの順序が不定なもの - 正解です。ストリームから収集された Map のデフォルト実装（HashMap）は、キーの順序を保証しません。キー"Author X"が重複するため、マージ関数が適用され、その値は"Tale A, Tale C"となります。キー"Author Y"の値は"Tale B"です。したがって、この 2 つのエントリを含むマップが出力されますが、その表示順序は実行ごとに変わる可能性があります。
D. {Author Y=Tale B, Author X=Tale A, Tale C} - Collectors.toMap の第三引数であるマージ関数は、重複したキーに関連付けられた値をどのようにマージするかを定義します。この場合、"Tale A"と"Tale C"は結合され、"Tale A, Tale C"という単一の文字列になります。この選択肢では、値が 2 つの別々の文字列として表現されており、不正です。

```

56. 問題文: 以下のコードスニペットは、java.nio.file.Path の relativize メソッドの動作を示しています。このコードを実行した結果、コンソールに出力されるものはどれですか。

```java
import java.nio.file.Path;
import java.nio.file.Paths;

public class Main {
    public static void main(String[] args) {
        Path p1 = Paths.get("/app/src/main");
        Path p2 = Paths.get("/app/build/classes");

        Path result = p1.relativize(p2);
        System.out.println(result);
    }

}

```

```

A. ../../build/classes
B. ../build/classes
C. build/classes
D. ../../../app/build/classes

```

```

正解: A
解説:
A. ../../build/classes - 正解です。p1.relativize(p2)は、p1 から p2 への相対パスを計算します。p1 のパス/app/src/main から共通の親である/app に戻るには、2 階層上がる必要があります(../..)。/app まで戻った後、build ディレクトリ、次に classes ディレクトリへと進むため、結果は../../build/classes となります。
B. ../build/classes - このパスは、p1 が/app/src である場合に正しい結果となります。現在の p1 は/app/src/main であり、共通の親/app に戻るには 2 階層上がる必要があるため、..が 1 つ不足しています。
C. build/classes - このパスは、p1 と p2 の親ディレクトリが同じである場合（例：p1 が/app/src、p2 が/app/build）の結果に近くなります。しかし、両者の間には階層の違いがあるため、親ディレクトリへの移動が必要です。
D. ../../../app/build/classes - このパスは、3 階層上がってから app ディレクトリに下るという冗長なパスです。relativize メソッドは、2 つのパス間の最短の相対的な道筋を計算します。

```

57. 問題文: クラスパス上に以下の 3 つのプロパティファイルが存在します。

```

Messages.properties (内容: message=Default)

Messages_ja.properties (内容: message=Japanese)

Messages_ja_JP.properties (内容: message=Japanese (Japan))
```

このとき、以下の Java コードを実行した結果として、コンソールに出力されるものはどれですか。

```java
import java.util.Locale;
import java.util.ResourceBundle;

public class Main {
    public static void main(String[] args) {
        Locale locale = Locale.JAPAN; // new Locale("ja", "JP") と等価
        ResourceBundle rb = ResourceBundle.getBundle("Messages", locale);
        System.out.println(rb.getString("message"));
    }
}
```

```
A. Japanese
B. Japanese (Japan)
C. Default
D. コンパイルエラーが発生する
```

```
正解: B
解説:
A. Japanese - この値は Messages_ja.properties ファイルに含まれています。ResourceBundle は、指定されたロケールに最も具体的に一致するファイルを探します。ja_JP が指定された場合、ja よりも ja_JP の方がより具体的であるため、Messages_ja_JP.properties が優先的に使用されます。
B. Japanese (Japan) - 正解です。ResourceBundle.getBundle は、指定されたロケールに最も一致するリソースバンドルを検索します。Locale.JAPAN (ja_JP)が指定された場合、Java は以下の順序でファイルを検索します。1. Messages_ja_JP.properties、2. Messages_ja.properties、3. Messages.properties（デフォルト）。このケースでは、最も具体的な Messages_ja_JP.properties が存在するため、このファイルが読み込まれます。
C. Default - この値はデフォルトの Messages.properties ファイルに含まれています。このファイルが使用されるのは、指定されたロケール（およびその上位のロケール）に一致するファイルがクラスパス上に一つも見つからない場合です。今回はより具体的なファイルが存在するため、デフォルトは使用されません。
D. コンパイルエラーが発生する - このコードは ResourceBundle と Locale の標準的な使い方を示しており、構文的に正しいです。したがって、コンパイルエラーは発生しません。

```

58. 問題文: 以下の Item レコードと Main クラスが定義されています。このコードを実行した結果として、コンソールに出力されるものはどれですか。

```java

// Item.java
public record Item(String name, int price) {
    public Item {
        if (price < 0) {
            throw new IllegalArgumentException("Price cannot be negative");
        }
    }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        Item item1 = new Item("Apple", 150);
        Item item2 = new Item("Apple", 150);
        Item item3 = new Item("Orange", 150);

        System.out.print(item1 == item2);
        System.out.print(", ");
        System.out.print(item1.equals(item2));
        System.out.print(", ");
        System.out.print(item1.equals(item3));
    }

}

```

```

A. true, true, false
B. false, true, true
C. false, true, false
D. true, true, true

```

```

正解: C
解説:
A. true, true, false - ==演算子は、2 つの参照がメモリ上の同じオブジェクトを指しているかどうかを比較します。item1 と item2 は new を使用して個別に作成された異なるインスタンスであるため、item1 == item2 は false となります。
B. false, true, true - record の equals メソッドは、全てのコンポーネント（この場合は name と price）が等しい場合にのみ true を返します。item1 と item3 は name の値が異なるため（"Apple"と"Orange"）、item1.equals(item3)は false を返します。
C. false, true, false - 正解です。item1 == item2 は、2 つの変数が異なるオブジェクトを参照しているため false です。record の equals メソッドは状態に基づいて比較を行うため、全てのフィールドが同じ値を持つ item1 と item2 の比較では true が返されます。item1 と item3 は name フィールドが異なるため、equals メソッドは false を返します。
D. true, true, true - この選択肢は、item1 == item2 と item1.equals(item3)の両方の評価が誤っています。==は参照を比較し、equals はレコードの全てのフィールドの状態を比較します。

```

59. 問題文: 以下の Animal、Dog、Cat クラスが同一パッケージ内に定義されています。このコードをコンパイルした結果、正しい説明はどれですか。

```java

// Animal.java
public abstract class Animal {
    public abstract void speak();
}

// Dog.java
public class Dog extends Animal {
    public void speak() { System.out.println("Woof"); }
    public void fetch() { System.out.println("Fetch"); }
}

// Cat.java
public class Cat extends Animal {
    public void speak() { System.out.println("Meow"); }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        Animal animal = new Dog();
        // line X
    }
}
```

```
A. line X に animal.fetch();を記述すると、コンパイルエラーが発生する。
B. line X に((Dog)animal).fetch();を記述すると、実行時に ClassCastException がスローされる。
C. line X に animal.speak();を記述すると、実行時に"Fetch"と出力される。
D. Dog クラスの speak メソッドを final で宣言すると、Cat クラスでコンパイルエラーが発生する。
```

```
正解: A
解説:
A. animal.fetch();を記述すると、コンパイルエラーが発生する。 - 正解です。変数 animal は Animal 型として宣言されています。Animal クラスには fetch()メソッドが定義されていないため、animal 参照を通じて fetch()メソッドを呼び出すことはできません。これはコンパイル時にチェックされます。
B. ((Dog)animal).fetch();を記述すると、実行時に ClassCastException がスローされる。 - animal 変数が参照している実際のオブジェクトは Dog インスタンスです。そのため、Dog 型へのダウンキャストは成功し、fetch()メソッドを呼び出すことができます。ClassCastException は発生しません。
C. animal.speak();を記述すると、実行時に"Fetch"と出力される。 - animal 変数は Dog オブジェクトを参照しているため、ポリモーフィズムにより Dog クラスの speak()メソッドが呼び出されます。このメソッドは"Woof"と出力します。"Fetch"と出力されることはありません。
D. Dog クラスの speak メソッドを final で宣言すると、Cat クラスでコンパイルエラーが発生する。 - Dog クラスと Cat クラスは継承関係にありません。そのため、Dog クラスのメソッド宣言が Cat クラスに影響を与えることはなく、コンパイルエラーは発生しません。

```

60. 問題文: equals メソッドと hashCode メソッドをオーバーライドしていない Person クラスがあります。以下のコードを実行した結果、コンソールに出力されるものはどれですか。

```java
import java.util.HashSet;
import java.util.Set;

class Person {
    private String name;
    public Person(String name) { this.name = name; }
    // equals() and hashCode() are not overridden
}

public class Main {
    public static void main(String[] args) {
        Set<Person> set = new HashSet<>();
        set.add(new Person("Alice"));
        set.add(new Person("Bob"));
        set.add(new Person("Alice"));

        System.out.println(set.size());
    }

}

```

```

A. 1
B. 2
C. 3
D. コンパイルエラーが発生する

```

```

正解: C
解説:
A. 1 - HashSet が内容に基づいて重複を判断する場合、この結果になる可能性があります。しかし、equals と hashCode がオーバーライドされていないため、参照に基づいて判断されます。
B. 2 - この結果は、new Person("Alice")の 2 つのインスタンスが等しいと見なされ、重複として扱われた場合に得られます。equals と hashCode がオーバーライドされていればこの結果になりますが、このコードではオーバーライドされていないため、2 つのインスタンスは別物として扱われます。
C. 3 - 正解です。Person クラスは equals()と hashCode()をオーバーライドしていないため、Object クラスのデフォルト実装を継承します。Object クラスの equals()は参照の等価性（==演算子と同じ）をチェックします。コード内で new Person()を使って 3 つの異なるオブジェクトが生成されており、それぞれメモリ上のアドレスが異なるため、HashSet はこれら 3 つをすべて異なる要素として扱います。
D. コンパイルエラーが発生する - Set に追加するクラスが equals()と hashCode()をオーバーライドすることは、期待される動作を保証する上で重要ですが、これは Java のコンパイラが強制するルールではありません。したがって、このコードは正常にコンパイルされます。

```

61. 問題文: 以下のコードスニペットは、Java 17 の switch 式を利用しています。このコードを実行した結果、コンソールに出力されるものはどれですか。

```java
public class Main {
    public static void main(String[] args) {
        String day = "WED";
        String type = switch (day) {
            case "MON", "TUE", "WED", "THU", "FRI" -> "Weekday";
            case "SAT", "SUN" -> "Weekend";
            default -> "Invalid day";
        };
        System.out.println(type);
    }
}

```

```
A. Weekday
B. Weekend
C. Invalid day
D. コンパイルエラーが発生する
```

```
正解: A
解説:
A. Weekday - 正解です。変数 day の値は"WED"です。switch 式は day の値を評価し、最初に一致した case ラベルの右辺の値を返します。"WED"は最初の case ラベル（"MON", "TUE", "WED", "THU", "FRI"）に含まれているため、switch 式は"Weekday"という文字列を返します。
B. Weekend - 変数 day の値は"WED"であり、"SAT"でも"SUN"でもないため、この case には一致しません。
C. Invalid day - default ケースは、他のどの case ラベルにも一致しなかった場合に実行されます。"WED"は最初の case ラベルに一致するため、default ケースは評価されません。
D. コンパイルエラーが発生する - このコードは Java 14 以降で標準機能となった switch 式と、複数の case ラベルをカンマで区切る構文を使用しています。これは Java 17 では有効な構文であり、コンパイルエラーは発生しません。

```

62. 問題文: 以下のディレクトリ構造が存在すると仮定します。

```
    /data
    ├── reports
    │ ├── daily.csv
    │ └── weekly.csv
    └── logs
    └── app.log
```

このとき、以下の Java コードを実行した結果として、コンソールに出力されるものはどれですか。

```java
import java.nio.file.*;
import java.io.IOException;
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) throws IOException {
        Path start = Paths.get("/data");
        try (Stream<Path> stream = Files.walk(start)) {
            long count = stream.filter(p -> Files.isDirectory(p)).count();
            System.out.println(count);
        }
    }
}

```

```

A. 2
B. 3
C. 5
D. 6

```

```

正解: B
解説:
A. 2 - この値は、/data ディレクトリの直下にあるディレクトリ（reports と logs）の数です。Files.walk は開始パス自体（/data）もストリームに含めるため、この結果は不正確です。
B. 3 - 正解です。Files.walk は指定されたパスから始まる全てのファイルとディレクトリを再帰的にトラバースする Stream を返します。この場合、ストリームには/data、/data/reports、/data/reports/daily.csv、/data/reports/weekly.csv、/data/logs、/data/logs/app.log が含まれます。.filter(p -> Files.isDirectory(p))によって、この中からディレクトリのみがフィルタリングされます。該当するのは/data、/data/reports、/data/logs の 3 つです。
C. 5 - この値は、ディレクトリ（3 つ）とファイル（2 つ、daily.csv と weekly.csv）の合計数から app.log を除いたものに近いです。フィルタ条件はディレクトリのみを数えるため、ファイルはカウントに含まれません。
D. 6 - この値は、Files.walk によってトラバースされる全てのパス（ファイルとディレクトリの両方）の総数です。フィルタ条件 Files.isDirectory(p)が適用されているため、ファイルはカウントから除外されます。

```

63. 問題文: 以下のコードスニペットは、並列ストリーム(parallelStream)とステートフルなラムダ式を使用しています。このコードを実行した結果に関する最も正確な説明はどれですか。

```java
import java.util.List;
import java.util.ArrayList;
import java.util.stream.IntStream;
import java.util.stream.Collectors;

public class Main {
    public static void main(String[] args) {
        List<Integer> numbers = IntStream.rangeClosed(1, 1000)
        .boxed()
        .collect(Collectors.toList());

        List<Integer> evenNumbers = new ArrayList<>();
        numbers.parallelStream()
               .filter(n -> n % 2 == 0)
               .forEach(n -> evenNumbers.add(n)); // Stateful lambda

        System.out.println(evenNumbers.size());
    }

}

```

```

A. コードは常に 500 を出力する
B. コードは実行時に ConcurrentModificationException をスローする
C. コードはコンパイルに失敗する
D. コードは予測不能な結果を生成し、500 以外の値を出力する可能性がある

```

```

正解: D
解説:
A. コードは常に 500 を出力する - 逐次ストリーム(stream())であれば、1 から 1000 までの偶数の数は 500 なので、この結果になります。しかし、並列ストリームからスレッドセーフでない ArrayList を同期なしで変更すると、複数のスレッドが同時に add メソッドを呼び出す競合状態が発生し、一部の要素が失われる可能性があります。
B. コードは実行時に ConcurrentModificationException をスローする - この例外は、通常、コレクションのイテレーション中にそのコレクションが変更された場合にスローされます。このコードでは、numbers リストは変更されていません。evenNumbers リストに対する競合状態は、この例外ではなく、データ損失や ArrayIndexOutOfBoundsException などを引き起こす可能性があります。
C. コードはコンパイルに失敗する - このコードは構文的に有効です。ラムダ式から外部の非 final な ArrayList オブジェクトのメソッドを呼び出すことは許可されています。問題はコンパイル時ではなく、実行時の並列処理の安全性にあります。
D. コードは予測不能な結果を生成し、500 以外の値を出力する可能性がある - 正解です。ArrayList はスレッドセーフではありません。並列ストリーム内の forEach から複数のスレッドが同時に evenNumbers.add(n)を呼び出すと、データレースが発生します。これにより、リストの内部配列のサイズ変更時に更新が失われたり、意図しない上書きが発生したりする可能性があり、結果としてリストの最終的なサイズが期待値の 500 と異なる、予測不能なものになります。

```

64. 問題文: employees テーブルに複数のレコードが存在し、有効な Connection オブジェクト conn が利用可能であるとします。以下の JDBC コードを実行した際の結果として、最も適切な説明はどれですか。

```java
import java.sql.*;

public class Main {
    public static void main(String[] args) {
        // conn は有効な Connection オブジェクトと仮定
        try (Connection conn = DriverManager.getConnection("url", "user", "pass")) {
            Statement stmt = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_READ_ONLY);
            ResultSet rs = stmt.executeQuery("SELECT \* FROM employees");

            if (rs.last()) {
                rs.afterLast();
            }

            // rs.previous()が呼び出された後のカーソルの状態を問う
            if (rs.previous()) {
                System.out.println(rs.getRow() + " " + rs.getString("name"));
            } else {
                System.out.println("No record found.");
            }

        } catch (SQLException e) {
            System.out.println("SQL Error.");
        }
    }

}

```

```

A. 最終行のレコードの行番号と名前が出力される
B. 先頭行のレコードの行番号と名前が出力される
C. "No record found." が出力される
D. "SQL Error." が出力される

```

```

正解: A
解説:
A. 最終行のレコードの行番号と名前が出力される - 正解です。Statement はスクロール可能な ResultSet を生成するように作成されています。rs.last()はカーソルを最終行に移動させ、成功すると true を返します。次に rs.afterLast()がカーソルを最終行の直後に移動させます。その後の rs.previous()呼び出しは、カーソルを 1 つ前に戻し、再び最終行に位置付けます。rs.getRow()は現在の行番号（最終行の番号）を返し、rs.getString("name")はその行の名前を返します。
B. 先頭行のレコードの行番号と名前が出力される - カーソルは last()、afterLast()、previous()と移動しており、先頭行に移動する操作は行われていないため、この選択肢は不正確です。
C. "No record found." が出力される - rs.previous()は、カーソルが最終行の直後にある状態から呼び出されるため、正常に最終行に戻り true を返します。したがって、else ブロックは実行されません。
D. "SQL Error." が出力される - Statement が ResultSet.TYPE_SCROLL_INSENSITIVE で作成されているため、last()、afterLast()、previous()といったカーソル移動メソッドはサポートされています。したがって、SQLException はスローされません。

```

65. 問題文: 以下の Database クラスと Main クラスが定義されています。このコードを実行した結果、コンソールに出力されるものはどれですか。

```java

import java.util.Optional;

class Database {
    public static Optional<String> findData(String key) {
        if ("Success".equals(key)) {
            return Optional.of("Data");
        }
        return Optional.empty();
    }
}

public class Main {
    public static Optional<String> getKey() {
        return Optional.of("Success");
    }

    public static void main(String[] args) {
        String result = getKey()
                        .flatMap(Database::findData)
                        .orElse("Default");
        System.out.println(result);
    }

}
```

```
A. Default
B. Data
C. Optional[Data]
D. コンパイルエラーが発生する
```

```
正解: B
解説:
A. Default - orElse メソッドのデフォルト値が返されるのは、メソッドチェーンの途中で Optional が空になった場合です。このコードでは、getKey()も findData()も値を含む Optional を返すため、この選択肢は不正確です。
B. Data - 正解です。getKey()メソッドは"Success"を含む Optional<String>を返します。次に、flatMap がこの Optional から値"Success"を取り出し、Database::findData メソッドに渡します。findData("Success")は"Data"を含む Optional<String>を返します。flatMap は、このようにネストされた Optional を平坦化するため、結果は"Data"を含む単一の Optional となります。最後に orElse がその値を取り出します。
C. Optional[Data] - orElse メソッドは、Optional インスタンスが保持する値を直接返します。Optional オブジェクト自体の toString()表現である Optional[Data]を返すわけではありません。
D. コンパイルエラーが発生する - このコードは、Optional を返すメソッドを連鎖させるための flatMap の正しい使い方を示しています。map(Database::findData)を使用すると、戻り値の型が Optional<Optional<String>>となり型不一致でコンパイルエラーになりますが、flatMap の使用は適切です。

```

66. 問題文: 以下の Vehicle クラスと Car クラスが定義されています。このコードを実行した結果、コンソールに出力されるものはどれですか。

```java

    class Vehicle {
    protected String type;
    Vehicle() {
    this.type = "Unknown";
    System.out.print("Vehicle ");
    }
    public String getType() {
    return type;
    }
    }

class Car extends Vehicle {
Car() {
this.type = "Car";
System.out.print("Car ");
}
}

public class Main {
public static void main(String[] args) {
Vehicle v = new Car();
System.out.print(v.getType());
}
}

```

```

A. Vehicle Car Unknown
B. Vehicle Car Car
C. Car Vehicle Car
D. Car Vehicle Unknown

```

```

正解: B
解説:
A. Vehicle Car Unknown - v.getType()が返す値は、Car のコンストラクタで type フィールドが"Car"に設定された後の値です。スーパークラスのコンストラクタで設定された"Unknown"は上書きされます。
B. Vehicle Car Car - 正解です。new Car()が実行されると、まず暗黙的にスーパークラス Vehicle のコンストラクタが呼び出され、type が"Unknown"に設定され、"Vehicle "が出力されます。次にサブクラス Car のコンストラクタが実行され、type フィールドが"Car"に上書きされ、"Car "が出力されます。最後に v.getType()が呼び出され、v が参照する Car オブジェクトの type フィールドの値"Car"が出力されます。
C. Car Vehicle Car - サブクラスのコンストラクタの実行に先立って、必ずスーパークラスのコンストラクタが呼び出されます。そのため、出力は"Vehicle "から始まります。
D. Car Vehicle Unknown - スーパークラスのコンストラクタが先に呼び出されるため、出力は"Vehicle "から始まります。また、type フィールドの値はサブクラスのコンストラクタによって上書きされるため、getType()は"Car"を返します。

```

67. 問題文: 以下のコードスニペットを実行したとき、標準出力に出力される結果はどれですか。

```java

    import java.util.Map;
    import java.util.TreeMap;

public class Main {
public static void main(String[] args) {
Map<String, Integer> map = new TreeMap<>();
map.put("C", 3);
map.put("A", 1);

        map.compute("A", (k, v) -> v + 10);
        map.compute("B", (k, v) -> v == null ? 20 : v * 2);
        map.compute("C", (k, v) -> null);

        System.out.println(map);
    }

}
```

```
A. {A=11, B=20}
B. {A=11, B=20, C=3}
C. {A=1, B=20}
D. 実行時に NullPointerException がスローされる
```

```
正解: A
解説:
A. {A=11, B=20} - 正解です。TreeMap はキーの自然順序でエントリをソートします。compute("A", ...)は既存の値 1 に 10 を加えて 11 に更新します。compute("B", ...)はキーが存在しないため値 v が null となり、結果として 20 がキー"B"に関連付けられます。compute("C", ...)は計算結果が null のため、キー"C"のエントリをマップから削除します。
B. {A=11, B=20, C=3} - compute メソッドで第二引数の計算結果が null を返した場合、そのキーと値のマッピングはマップから削除されます。したがって、キー"C"のエントリは最終的なマップに含まれません。
C. {A=1, B=20} - compute メソッドは、指定されたキーに対するマッピングを計算し、その結果で値を更新します。キー"A"の値は 1 + 10 の計算結果である 11 に更新されます。この選択肢では値が更新されていないため不正確です。
D. 実行時に NullPointerException がスローされる - compute メソッドの第二引数として渡される BiFunction では、キーが存在しない場合に値 v が null となりますが、これは仕様通りの動作です。コード内のラムダ式 v == null ? 20 : v \* 2 では null を適切に扱っているため、NullPointerException はスローされません。

```

68. 問題文: 以下の java.util.List の removeIf メソッドを使用したコードがあります。このコードを実行した結果、コンソールに出力されるものはど```java
    れですか。

        import java.util.ArrayList;
        import java.util.List;

public class Main {
public static void main(String[] args) {
List<String> list = new ArrayList<>();
list.add("apple");
list.add("banana");
list.add("apricot");
list.add("blueberry");

        list.removeIf(s -> s.startsWith("b"));

        System.out.println(list);
    }

}

```

```

A. [apple, banana, apricot, blueberry]
B. [apple, apricot]
C. [banana, blueberry]
D. [apple, apricot, blueberry]

```

```

正解: B
解説:
A. [apple, banana, apricot, blueberry] - removeIf メソッドは、指定された述語（Predicate）が true を返す要素をリストから削除します。このコードでは条件に一致する要素が存在するため、リストは変更されます。この選択肢は、リストが変更されなかった場合の結果です。
B. [apple, apricot] - 正解です。removeIf メソッドは、ラムダ式 s -> s.startsWith("b")をリストの各要素に適用します。この条件は、文字列が"b"で始まる場合に true を返します。"banana"と"blueberry"がこの条件に一致するため、これら 2 つの要素がリストから削除されます。残った要素は"apple"と"apricot"です。
C. [banana, blueberry] - この選択肢は、条件に一致した要素がリストに残り、一致しなかった要素が削除された場合の結果です。removeIf は条件に一致した要素を「削除」します。
D. [apple, apricot, blueberry] - この選択肢は、"banana"のみが削除された場合の結果です。removeIf はストリームの filter のように一度だけ一致する要素を見つけて停止するのではなく、リスト内の全ての要素を評価します。

````

69. 問題文: 以下の try-with-resources 文を使用したコードスニペットがあります。Resource クラスの close メソッドと try ブロックの両方で例```java
    外がスローされる場合、どのような結果になりますか。

        class Resource implements AutoCloseable {
        public void process() {
        System.out.print("Process ");
        throw new RuntimeException("Error in process");
        }
        @Override
        public void close() throws Exception {
        System.out.print("Close ");
        throw new Exception("Error in close");
        }
        }

public class Main {
public static void main(String[] args) {
try (Resource r = new Resource()) {
r.process();
} catch (Exception e) {
System.out.print("Caught:" + e.getMessage());
for (Throwable t : e.getSuppressed()) {
System.out.print(", Suppressed:" + t.getMessage());
}
}
}
}

````

```
A. Process Close Caught:Error in process, Suppressed:Error in close
B. Process Close Caught:Error in close, Suppressed:Error in process
C. Process Caught:Error in process
D. Process Close Caught:Error in close
```

```
正解: A
解説:
A. Process Close Caught:Error in process, Suppressed:Error in close - 正解です。try-with-resources ブロック内で、まず r.process()が呼び出され "Process "が出力され、RuntimeException がスローされます。catch ブロックに移る前に、リソースの close()メソッドが自動的に呼び出されます。close()メソッドは "Close "を出力し、Exception をスローします。try ブロックから発生した例外がプライマリな例外となり、close()で発生した例外は抑制された（suppressed）例外としてプライマリ例外に追加されます。catch ブロックはプライマリ例外を捕捉し、そのメッセージと抑制された例外のメッセージを出力します。
B. Process Close Caught:Error in close, Suppressed:Error in process - try-with-resources 文では、try ブロック本体からスローされた例外が、リソースのクローズ時にスローされた例外よりも優先されます。したがって、捕捉されるプライマリ例外は"Error in process"です。
C. Process Caught:Error in process - try-with-resources 文の重要な機能は、try ブロックで何が起ころうとも、リソースの close()メソッドの呼び出しを保証することです。この選択肢は close()メソッドの実行（"Close "という出力と、そこで発生した例外）を無視しています。
D. Process Close Caught:Error in close - この選択肢は、close()メソッドからの例外がプライマリ例外として扱われ、try ブロックからの例外が抑制された例外として扱われるか、あるいは失われたかのような結果を示しています。これは try-with-resources の例外処理のルールとは逆です。

```

70. 問題文: Serializable インターフェースを実装した User クラスがあります。password フィールドは transient として宣言されています。この```java
    クラスのインスタンスをシリアライズし、その後デシリアライズする以下のコードを実行した結果、コンソールに出力されるものはどれですか。

        import java.io.\*;

class User implements Serializable {
private static final long serialVersionUID = 1L;
String username;
transient String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

}

public class Main {
public static void main(String[] args) throws Exception {
// Serialize
ByteArrayOutputStream baos = new ByteArrayOutputStream();
try (ObjectOutputStream oos = new ObjectOutputStream(baos)) {
oos.writeObject(new User("testuser", "pass123"));
}

        // Deserialize
        User user = null;
        ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
        try (ObjectInputStream ois = new ObjectInputStream(bais)) {
            user = (User) ois.readObject();
        }

        System.out.println(user.username + ":" + user.password);
    }

}

```

```

A. testuser:pass123
B. testuser:null
C. null:null
D. 実行時に java.io.NotSerializableException がスローされる

```

```

正解: B
解説:
A. testuser:pass123 - password フィールドは transient キーワードで修飾されているため、シリアライズの対象から除外されます。そのため、デシリアライズ後に元の値"pass123"が復元されることはありません。
B. testuser:null - 正解です。transient とマークされたフィールドは、Java のシリアライズ機構によって無視されます。オブジェクトがデシリアライズされる際、transient フィールドはそのデータ型のデフォルト値で初期化されます。String のようなオブジェクト参照型のデフォルト値は null です。username フィールドは transient ではないため、値は正しく保持されます。
C. null:null - username フィールドは transient ではないため、その値はシリアライズされ、デシリアライズ時に復元されます。したがって、username が null になることはありません。
D. 実行時に java.io.NotSerializableException がスローされる - User クラスは Serializable インターフェースを実装しているため、そのインスタンスはシリアライズ可能です。transient フィールドを持つことはシリアライズの妨げにはならず、特定のフィールドを永続化の対象外とすることを示す正当な方法です。

````

71. 問題文: 以下のジェネリクスのワイルドカードを使用したコードがあります。main メソッド内のどの行でコンパイルエラーが発生しますか。

```java

    import java.util.List;
    import java.util.ArrayList;

public class Main {
public static void processElements(List<? extends Number> list) {
for (Number n : list) {
System.out.println(n.intValue());
}
}

    public static void main(String[] args) {
        List<Integer> integers = new ArrayList<>();
        List<Double> doubles = new ArrayList<>();
        List<String> strings = new ArrayList<>();
        List<Object> objects = new ArrayList<>();

        processElements(integers); // line 1
        processElements(doubles);  // line 2
        processElements(strings);  // line 3
        processElements(objects);  // line 4
    }

}
```

```
A. line 1 と line 2
B. line 3 と line 4
C. line 3
D. line 4
```

```
正解: B
解説:
A. line 1 と line 2 - processElements メソッドの引数は List<? extends Number>であり、これは Number またはそのサブクラスの List を受け入れることを意味します。Integer と Double はどちらも Number のサブクラスなので、List<Integer>と List<Double>を渡すことは有効です。
B. line 3 と line 4 - 正解です。line 3 では List<String>を渡そうとしています。String は Number のサブクラスではないため、型が一致せずコンパイルエラーとなります。line 4 では List<Object>を渡そうとしています。Object は Number のスーパークラスであり、サブクラスではないため、? extends Number の境界要件を満たさず、こちらもコンパイルエラーとなります。
C. line 3 - line 3 (List<String>)はコンパイルエラーになりますが、それだけではありません。line 4 (List<Object>)も同様にエラーとなります。
D. line 4 - line 4 (List<Object>)はコンパイルエラーになりますが、それだけではありません。line 3 (List<String>)も同様にエラーとなります。

```

72. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

    import java.util.List;

public class Main {
public static void main(String[] args) {
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
Integer result = numbers.stream()
.reduce(10, (subtotal, element) -> subtotal + element);
System.out.println(result);
}
}

```

```

A. 15
B. 25
C. 10
D. コンパイルエラーが発生する

```

```

正解: B
解説:
A. 15 - reduce メソッドの第一引数は初期値（identity）であり、この値が計算の開始点となります。このコードでは Stream の要素を単純に合計するのではなく、初期値 10 に各要素を加算していきます。したがって、Stream の要素の合計である 15 は正しい結果ではありません。
B. 25 - 正解です。reduce メソッドは、第一引数で指定された初期値 10 を開始点として、Stream 内の各要素を順番に処理します。第二引数のラムダ式 (subtotal, element) -> subtotal + element に基づき、subtotal に現在の合計値、element に Stream の要素が渡され、その和が次の subtotal になります。計算プロセスは 10 + 1 = 11、11 + 2 = 13、13 + 3 = 16、16 + 4 = 20、20 + 5 = 25 となり、最終的な結果は 25 です。
C. 10 - reduce メソッドは、Stream が空でない限り、指定された処理を要素に対して実行します。そのため、初期値の 10 がそのまま結果として返されることはありません。Stream が空であった場合に初期値が返されます。
D. コンパイルエラーが発生する - 提示されたコードは Java の文法として正しく、Stream API の reduce メソッドのシグネチャにも合致しています。ジェネリクスやラムダ式の型も適切であるため、コンパイルエラーは発生しません。

```

73. 問題文: 以下の sealed 修飾子を使用したインターフェースとクラスの定義があります。これらのファイルをコンパイルした結果について、最も適切な説```java
    明はどれですか。
    Shape.java:

        public sealed interface Shape permits Figure {}

Figure.java:
public non-sealed class Figure implements Shape {}

Triangle.java:
public class Triangle extends Figure {}

```

```

A. コンパイルは成功する
B. Figure.java のコンパイルに失敗する。なぜなら、sealed インターフェースを実装するクラスは final でなければならないため
C. Triangle.java のコンパイルに失敗する。なぜなら、Shape の permits 句に Triangle が含まれていないため
D. Triangle.java のコンパイルに失敗する。なぜなら、Figure を拡張するクラスは final でなければならないため

```

```

正解: A
解説:
A. コンパイルは成功する - 正解です。Shape インターフェースは sealed であり、その直接の実装を Figure クラスに限定しています。Figure クラスは non-sealed として宣言されているため、このクラス自体はどのクラスからでも自由に拡張できます。Triangle クラスが non-sealed である Figure クラスを拡張することは sealed クラスのルールに違反しないため、すべてのコードが正常にコンパイルされます。
B. Figure.java のコンパイルに失敗する。なぜなら、sealed インターフェースを実装するクラスは final でなければならないため - sealed なクラスやインターフェースの直接のサブクラスは、final、sealed、または non-sealed のいずれかとして宣言する必要があります。non-sealed は有効な選択肢の一つです。
C. Triangle.java のコンパイルに失敗する。なぜなら、Shape の permits 句に Triangle が含まれていないため - permits 句は、sealed なクラスやインターフェースを「直接」継承または実装するクラスをリストアップします。Triangle は Shape を直接実装しているわけではないため、permits 句にリストする必要はありません。
D. Triangle.java のコンパイルに失敗する。なぜなら、Figure を拡張するクラスは final でなければならないため - この制約は、sealed クラスを拡張する場合に適用されることがあります。しかし、Figure クラスは non-sealed と宣言されているため、それを拡張するクラス（この場合は Triangle）には特別な修飾子（final、sealed、non-sealed）は要求されません。

````

74. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

    public class Main {
    record User(String id, String name) {}

        public static void main(String[] args) {
            User user1 = new User("001", "Alice");
            User user2 = new User("001", "Alice");

            System.out.println(user1.equals(user2) + ", " + (user1 == user2));
        }

    }

```

```

A. true, true
B. false, false
C. true, false
D. false, true

```

```

正解: C
解説:
A. true, true - user1 と user2 は new によって個別のインスタンスとしてメモリ上に作成されるため、参照先が異なります。したがって、==演算子による参照の比較は false を返します。
B. false, false - record クラスは、全てのフィールドの値が等しい場合に true を返す equals メソッドを自動的に実装します。このコードでは user1 と user2 の id と name フィールドはそれぞれ等しいため、user1.equals(user2)は true になります。
C. true, false - 正解です。record 型では、コンパイラが全てのフィールドを比較する equals メソッドを自動生成します。user1 と user2 は同じ id と name を持つため、user1.equals(user2)は true を返します。一方で、user1 と user2 は別々のオブジェクトとしてインスタンス化されているため、メモリ上の参照が異なります。そのため、参照を比較する==演算子の結果は false となります。
D. false, true - equals メソッドは内容の等価性を評価し、==演算子は参照の同一性を評価します。この選択肢は、それらの役割が逆になった場合の結果を示しており、正しくありません。

```

75. 問題文: 以下のコードに関する説明として正しいものはどれですか。

```java

    import java.util.concurrent.\*;

public class Main {
public static void main(String[] args) {
ExecutorService es = Executors.newSingleThreadExecutor();
Future<String> future = es.submit(() -> "Result");
String result = future.get();
System.out.println(result);
es.shutdown();
}
}
```

```
A. "Result"と出力された後、RejectedExecutionException がスローされる。
B. コンパイルエラーが発生する。
C. "Result"と出力され、プログラムは正常に終了する。
D. future.get()の呼び出しで TimeoutException がスローされる。
```

```
正解: B
解説:
A. "Result"と出力された後、RejectedExecutionException がスローされる。 - RejectedExecutionException は、ExecutorService が停止した後に新しいタスクを投入しようとした場合に発生します。このコードでは、submit が shutdown の前に呼び出されているため、この例外はスローされません。
B. コンパイルエラーが発生する。 - 正解です。Future.get()メソッドは、チェック例外である InterruptedException および ExecutionException をスローする可能性があります。チェック例外は try-catch ブロックで処理するか、メソッドシグネチャに throws 句を追加して呼び出し元にスローすることを明示する必要があります。このコードではどちらの対応も行われていないため、コンパイルエラーとなります。
C. "Result"と出力され、プログラムは正常に終了する。 - future.get()がスローする可能性のあるチェック例外が処理されていないため、このコードはコンパイルに失敗します。したがって、実行されて"Result"と出力されることはありません。
D. future.get()の呼び出しで TimeoutException がスローされる。 - TimeoutException は、タイムアウト時間を指定するバージョンの get メソッド（例: get(long, TimeUnit)) を使用し、指定時間内に結果が返されない場合にスローされます。このコードでは引数なしの get メソッドを使用しているため、この例外は発生しません。

```

76. 問題文: 以下の IntStream を使用したコードスニペットがあります。このコードを実行した結果、コンソールに出力されるものはどれですか。

```java

    import java.util.stream.IntStream;
    import java.util.IntSummaryStatistics;

public class Main {
public static void main(String[] args) {
IntStream stream = IntStream.rangeClosed(1, 5); // 1, 2, 3, 4, 5
IntSummaryStatistics stats = stream.summaryStatistics();

        long sum = stats.getSum();
        long count = stats.getCount();

        System.out.println(sum + ", " + count);
    }

}

```

```

A. 15, 5
B. 15, 6
C. 10, 5
D. 10, 4

```

```

正解: A
解説:
A. 15, 5 - 正解です。IntStream.rangeClosed(1, 5)は、1、2、3、4、5 の 5 つの整数を含むストリームを生成します。summaryStatistics()メソッドはこれらの統計情報を計算します。getSum()は要素の合計（1+2+3+4+5=15）を返し、getCount()は要素の数（5）を返します。
B. 15, 6 - IntStream.rangeClosed(1, 5)は終端値である 5 を範囲に含みます。そのため、要素数は 5 つです。もし range(1, 6)が使われていれば要素数は 5 ですが、rangeClosed は両端の値を含むため、このカウントは不正確です。
C. 10, 5 - getSum()はストリームの全ての要素の合計を返します。1 から 5 までの合計は 15 です。10 は 1 から 4 までの合計であり、range(1, 5)を使用した場合の合計値です。
D. 10, 4 - この選択肢は、range(1, 5)（終端を含まない）を想定した場合の結果です。IntStream.range(1, 5)は 1, 2, 3, 4 のストリームを生成し、その場合の合計は 10、カウントは 4 となります。しかし、コードでは rangeClosed が使用されています。

```

77. 問題文: 以下のコードを実行した結果として、コンソールに出力される内容はどれですか。

```java

    public class Main {
    public static void main(String[] args) {
    System.out.println(checkReturn());
    }

        private static String checkReturn() {
            try {
                // 例外をスローする可能性がある処理
                if (true) {
                    throw new IllegalStateException();
                }
                return "From Try";
            } catch (IllegalStateException e) {
                return "From Catch";
            } finally {
                // このブロックはreturnの前に必ず実行される
                System.out.print("From Finally ");
            }
        }

    }
```

```
A. From Finally From Catch
B. From Catch
C. From Finally From Try
D. From Try
```

```
正解: A
解説:
A. From Finally From Catch - 正解です。try ブロック内で IllegalStateException がスローされ、すぐに catch ブロックに処理が移ります。catch ブロックは"From Catch"という文字列を返そうとしますが、メソッドが実際にリターンする直前に finally ブロックが実行されます。そのため、まず"From Finally "が出力され、その後に catch ブロックが返した値"From Catch"が出力されます。
B. From Catch - finally ブロックは、try または catch ブロックで return 文が実行される場合でも、その return の直前に必ず実行されます。したがって、"From Finally "という出力が欠けているこの選択肢は不正確です。
C. From Finally From Try - try ブロック内で例外がスローされているため、try ブロック内の return "From Try";は実行されません。制御は catch ブロックに移るため、この選択肢は不正確です。
D. From Try - try ブロック内で例外がスローされるため、try ブロックの末尾にある return 文には到達しません。そのため、"From Try"が返されることはありません。

```

78. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

    import java.util.Map;
    import java.util.HashMap;

public class Main {
public static void main(String[] args) {
Map<String, String> map = new HashMap<>();
map.put("A", "Apple");
map.put("B", "Banana");

        String key = "A";
        String value = "pple";

        map.merge(key, value, (v1, v2) -> v1.concat(v2));

        System.out.println(map.get(key));
    }

}

```

```

A. Apple
B. pple
C. Applepple
D. NullPointerException がスローされる

```

```

正解: C
解説:
A. Apple - map.merge メソッドは、指定されたキーがマップに存在する場合、第 3 引数で渡された関数を実行して値を更新します。したがって、キー"A"の値は変更されるため、元の値"Apple"が出力されることはありません。
B. pple - この結果は、map.put("A", "pple")のように値を単純に上書きした場合の動作です。merge メソッドはキーが存在する場合、既存の値と新しい値を使って新たな値を計算するための関数を適用します。
C. Applepple - 正解です。Map インターフェースの merge メソッドは、指定されたキーがマップに存在する場合、既存の値と新しい値を引数として再マッピング関数を実行します。このコードでは、キー"A"が存在するため、既存の値"Apple"(v1)と新しい値"pple"(v2)がラムダ式に渡され、concat メソッドによって連結された"Applepple"が生成されます。この結果がキー"A"の新しい値として更新されます。
D. NullPointerException がスローされる - merge メソッドの再マッピング関数は、キーがマップに存在する場合にのみ実行されます。そのため、第一引数(v1)が null になることはありません。したがって、v1.concat(v2)が NullPointerException をスローすることはありません。

```

79. 問題文: 以下の JDBC コードに関する説明として、最も適切なものはどれですか。（conn は有効な Connection オブジェクトであるとします）

```java

    String sql = "SELECT name FROM products";
    try (Statement stmt = conn.createStatement();
    ResultSet rs = stmt.executeQuery(sql)) {
    if (rs.next()) {
    System.out.println(rs.getString(1));
    }
    } catch (SQLException e) {
    // 例外処理
    }
```

```
A. try ブロックを抜ける際に rs と stmt は自動的にクローズされるが、conn は手動でクローズする必要がある。
B. コード実行後、ResultSet のカーソルは結果セットの最後の行の直後に位置する。
C. products テーブルにレコードが 1 件も存在しない場合、SQLException がスローされる。
D. rs.next()が false を返した場合でも、rs.getString(1)は最初の行の名前を返す。
```

```
正解: A
解説:
A. try ブロックを抜ける際に rs と stmt は自動的にクローズされるが、conn は手動でクローズする必要がある。 - 正解です。try-with-resources 文の()内で宣言されたリソースは、ブロックを抜ける際に自動的に close()メソッドが呼び出されます。このコードでは Statement と ResultSet が対象です。conn はこの構文の管理外であるため、別途クローズ処理を行う必要があります。
B. コード実行後、ResultSet のカーソルは結果セットの最後の行の直後に位置する。 - このコードは if (rs.next())を使用しているため、結果セットにデータがあればカーソルは最初の行に一度だけ移動します。結果セットの最後まで反復処理は行われないため、カーソルが最後の行の直後に位置することはありません。
C. products テーブルにレコードが 1 件も存在しない場合、SQLException がスローされる。 - クエリの結果が 0 件であることはエラーではありません。その場合、rs.next()は false を返し、if ブロック内の処理が実行されないだけで、SQLException はスローされません。
D. rs.next()が false を返した場合でも、rs.getString(1)は最初の行の名前を返す。 - rs.next()が false を返した場合、if 文の条件が満たされないため、ブロック内の rs.getString(1)は実行されません。カーソルが有効な行を指していない状態でデータ取得メソッドを呼び出すと、通常は SQLException がスローされます。

```

80. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

    public class Main {
    public static void main(String[] args) {
    System.out.println(checkValue());
    }

        private static int checkValue() {
            try {
                return 10;
            } finally {
                return 20;
            }
        }

    }

```

```

A. 10
B. 20
C. コンパイルエラーが発生する。
D. IllegalStateException がスローされる。

```

```

正解: B
解説:
A. 10 - try ブロック内の return 10;は実行の準備がされますが、メソッドからリターンする前に finally ブロックが実行されます。その finally ブロック内で異なる値がリターンされるため、try ブロックからの戻り値 10 は破棄されます。
B. 20 - 正解です。try-finally 構文では、finally ブロックは try ブロックの処理が完了（return を含む）する直前に必ず実行されます。もし finally ブロック内に return 文が存在する場合、その return 文が try ブロックの return 文を上書きします。したがって、このメソッドは最終的に 20 を返します。
C. コンパイルエラーが発生する。 - try ブロックと finally ブロックの両方に return 文を記述することは、Java の言語仕様上は有効です。そのため、コンパイルエラーは発生しません。ただし、このようなコーディングスタイルはコードの意図を不明確にするため、一般的には避けられます。
D. IllegalStateException がスローされる。 - このコードの実行パターンは Java の言語仕様で明確に定義されており、特定の実行時例外がスローされる原因にはなりません。プログラムは例外をスローせずに正常に実行され、値を返します。

```

81. 問題文: 以下の 3 つのモジュールが存在する状況を考えます。

```java
    module com.app.main の module-info.java:

    module com.app.main {
    requires com.app.service;
    }

module com.app.service の module-info.java:
module com.app.service {
requires com.app.util;
exports com.app.service.api;
}

module com.app.util の module-info.java:
module com.app.util {
exports com.app.util.helper;
}
```

```
com.app.main モジュール内のコードから com.app.util.helper パッケージ内のクラスを利用しようとした場合、どのような結果になりますか。この状況を解決するための最も適切な方法はどれですか。
A. com.app.service モジュールが com.app.util.helper パッケージを exports すれば、アクセスが可能になる。
B. com.app.util モジュールが com.app.main モジュールに対して exports com.app.util.helper to com.app.main; と宣言すれば、アクセスが可能になる。
C. com.app.service モジュールの requires ディレクティブを requires transitive com.app.util; に変更すれば、アクセスが可能になる。
D. com.app.main モジュールが com.app.util モジュールを requires することで、アクセスが可能になる。
```

```
正解: C
解説:
A. com.app.service モジュールが com.app.util.helper パッケージを exports すれば、アクセスが可能になる。 - exports ディレクティブは、自モジュールに含まれるパッケージを公開するためのものです。他のモジュールからインポートしたパッケージを再公開する機能はありません。
B. com.app.util モジュールが com.app.main モジュールに対して exports com.app.util.helper to com.app.main; と宣言すれば、アクセスが可能になる。 - この方法はパッケージへのアクセスを特定のモジュールに限定しますが、com.app.main モジュールが com.app.util モジュール自体を読み込む（requires する）宣言がない限り、アクセスはできません。
C. com.app.service モジュールの requires ディレクティブを requires transitive com.app.util; に変更すれば、アクセスが可能になる。 - 正解です。requires transitive は推移的依存関係を示します。これにより、com.app.service を requires するモジュールは、com.app.util も暗黙的に requires することになります。その結果、com.app.main は com.app.util が公開しているパッケージにアクセスできるようになります。
D. com.app.main モジュールが com.app.util モジュールを requires することで、アクセスが可能になる。 - この変更によって com.app.main モジュールは com.app.util パッケージにアクセスできるようになりますが、問題は com.app.service の API が com.app.util の型を返すような場合に、利用側（main）で毎回 requires を追加する必要がある点です。requires transitive は、このような依存関係をモジュール提供側（service）で解決するための、より適切な方法です。

```

82. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

    import java.nio.file.Path;
    import java.nio.file.Paths;

public class Main {
public static void main(String[] args) {
Path path1 = Paths.get("/app/data/files");
Path path2 = Paths.get("/app/data/logs/2025/log.txt");
System.out.println(path1.relativize(path2));
}
}

```

```

A. ../logs/2025/log.txt
B. files/../logs/2025/log.txt
C. ../../files
D. java.lang.IllegalArgumentException がスローされる

```

```

正解: A
解説:
A. ../logs/2025/log.txt - 正解です。path1.relativize(path2)は、path1 の場所から path2 の場所へ移動するための相対パスを構築します。この場合、/app/data/files から一つ上の階層/app/data へ移動し(..)、そこから logs/2025/log.txt へ移動するため、この出力となります。
B. files/../logs/2025/log.txt - relativize メソッドは、基点となるパス(path1)のディレクトリ構造を考慮して共通の親までさかのぼるため、path1 の末尾の要素 files が結果の先頭に含まれることはありません。
C. ../../files - この選択肢は、操作を逆にした path2.relativize(path1)の結果とも異なります（その場合の結果は../../../files となります）。relativize メソッドは引数の順序が重要であり、path1 を基点に path2 へのパスを求めます。
D. java.lang.IllegalArgumentException がスローされる - この例外は、絶対パスと相対パスのように異なる種類のパス間で相対パスを解決しようとした場合にスローされます。提示されたコードでは両方の Path オブジェクトが絶対パスであるため、例外は発生しません。

```

83. 問題文: 以下の 2 つのクラス定義が存在する状況で、Square クラスの定義としてコンパイルが成功するものはどれですか。

```java

    public sealed interface Shape permits Circle, Square {
    // members
    }

public final class Circle implements Shape {
// members
}
```

```
A. public class Square implements Shape { }
B. public non-sealed class Square implements Shape { }
C. public abstract class Square implements Shape { }
D. public open class Square implements Shape { }
```

```
正解: B
解説:
A. public class Square implements Shape { } - sealed なインターフェースやクラスを実装または継承するクラスは、final、sealed、または non-sealed のいずれかの修飾子を明示的に指定する必要があります。この宣言にはそのいずれも含まれていないため、コンパイルエラーとなります。
B. public non-sealed class Square implements Shape { } - 正解です。sealed な階層の一部であるクラスは、non-sealed と宣言することができます。これにより、Square クラス自体は Shape の直接の実装クラスとしての制約を満たしつつ、Square クラス自身はどのクラスによっても自由に拡張されることが可能になります。
C. public abstract class Square implements Shape { } - クラスが abstract であるかどうかに関わらず、sealed なインターフェースを実装する場合には、sealed、non-sealed、final のいずれかの修飾子が必要です。abstract 修飾子だけでは不十分です。
D. public open class Square implements Shape { } - open は Java のキーワードではありません。sealed クラスの継承制約を緩和し、それ以降の自由な継承を許可するキーワードは non-sealed です。

```

84. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

    import java.util.List;
    import java.util.stream.Stream;

public class Main {
public static void main(String[] args) {
List<String> list1 = List.of("A", "B");
List<String> list2 = List.of("C", "D", "E");

        long count = Stream.of(list1, list2)
                           .flatMap(list -> list.stream())
                           .count();
        System.out.println(count);
    }

}

```

```

A. 2
B. 3
C. 5
D. コンパイルエラーが発生する。

```

```

正解: C
解説:
A. 2 - この値は、flatMap 操作を適用する前のストリームに含まれる要素の数（list1 と list2 という 2 つのリストオブジェクト）です。flatMap はストリームを平坦化するため、この結果にはなりません。
B. 3 - この値は list2 のサイズであり、処理全体の要素数を示しているわけではありません。ストリーム処理は list1 と list2 の両方を対象としています。
C. 5 - 正解です。Stream.of(list1, list2)は list1 と list2 の 2 つのリストを要素に持つストリーム (Stream<List<String>>) を生成します。flatMap メソッドは、各要素（この場合は各リスト）を新しいストリームに変換し、それらのストリームをすべて連結して一つの平坦なストリーム (Stream<String>) を作り出します。その結果、要素は "A", "B", "C", "D", "E" となり、count()メソッドがその総数である 5 を返します。
D. コンパイルエラーが発生する。 - このコードは Stream API とラムダ式を正しく使用しています。flatMap の引数として渡されるラムダ式は、List<String>を受け取り Stream<String>を返すため、型にも問題はなく、コンパイルは正常に完了します。

```

85. 問題文: クラスパス上に以下のプロパティファイルが存在する状況で、提示された Java コードを実行した際に出力される結果として正しいものはどれで```java
    すか。
    resources/MyLabels_de.properties:
    hello = Hallo
    resources/MyLabels.properties:
    hello = Hello
    goodbye = Bye
    resources/MyLabels_en.properties:
    hello = Hello

        import java.util.Locale;
        import java.util.ResourceBundle;

public class Main {
public static void main(String[] args) {
Locale.setDefault(new Locale("de", "DE"));
ResourceBundle bundle = ResourceBundle.getBundle("resources.MyLabels", new Locale("en"));
System.out.println(bundle.getString("goodbye"));
}
}

```

```

A. Bye
B. MissingResourceException がスローされる。
C. Hello
D. Hallo

```

```

正解: A
解説:
A. Bye - 正解です。ResourceBundle.getBundle メソッドにロケールとして en が指定されています。Java はまず MyLabels_en.properties ファイルを探しますが、このファイルに goodbye キーは存在しません。次に、親となるデフォルトのバンドル MyLabels.properties を検索します。このファイルには goodbye キーが存在し、その値は"Bye"であるため、この値が返されます。
B. MissingResourceException がスローされる。 - この例外は、指定されたキーが該当のバンドルおよびその親をさかのぼっても見つからない場合にスローされます。このシナリオでは、キー goodbye がデフォルトのプロパティファイル(MyLabels.properties)に見つかるため、例外は発生しません。
C. Hello - "Hello"は hello キーに対応する値です。コードでは getString("goodbye")を呼び出しているため、"Hello"が出力されることはありません。
D. Hallo - Locale.setDefault でデフォルトロケールが de_DE に設定されていますが、ResourceBundle.getBundle メソッドで new Locale("en")とロケールが明示的に指定されているため、デフォルトロケールの設定は無視され、指定された en ロケールが使用されます。

````

86. 問題文: 外部から受信したシリアライズされたデータを安全にデシリアライズするために、許可されたクラスのみをインスタンス化するように制限したいと```java
    考えています。この「デシリアライゼーション・フィルタリング」を実装するために、Java SE プラットフォームで提供されている最も適切なメカニズムはどれですか。

````

```

A. SecurityManager クラスを使用して、checkPermission メソッドをオーバーライドする。
B. デシリアライズ対象のクラスのフィールドに transient 修飾子を付与する。
C. java.io.ObjectInputFilter インターフェースを実装し、ObjectInputStream に設定する。
D. java.io.Serializable インターフェースに定義されている validateObject メソッドを実装する。

```

```

正解: C
解説:
A. SecurityManager クラスを使用して、checkPermission メソッドをオーバーライドする。 - SecurityManager は Java のセキュリティ機構の一部ですが、Java 17 では非推奨となっており、デシリアライズされるクラスを細かくフィルタリングする用途には不向きです。より現代的で推奨される方法は ObjectInputFilter の使用です。
B. デシリアライズ対象のクラスのフィールドに transient 修飾子を付与する。 - transient 修飾子は、特定のフィールドがシリアライゼーションの対象外となることを示すマーカーです。これはデータの永続化を制御するものであり、デシリアライズされるクラスの種類を制限するセキュリティ機能ではありません。
C. java.io.ObjectInputFilter インターフェースを実装し、ObjectInputStream に設定する。 - 正解です。ObjectInputFilter は、デシリアライゼーションの過程で読み込まれるクラスを検証するための専用の仕組みです。ObjectInputStream にフィルタを設定することで、許可リストや拒否リストに基づいてクラスをチェックし、意図しないクラスのインスタンス化を防ぐことができ、セキュリティを向上させます。
D. java.io.Serializable インターフェースに定義されている validateObject メソッドを実装する。 - Serializable インターフェース自体には validateObject メソッドは存在しません。似た機能を持つ ObjectInputValidation インターフェースの validateObject メソッドは、オブジェクトが完全にデシリアライズされた後にその状態の整合性を検証するためのものであり、クラスがインスタンス化されるのを事前に防ぐフィルタリング機能とは異なります。

```

87. 問題文: 以下の Java コードをコンパイルした際の結果として、正しいものはどれですか。

```java

    import java.util.List;
    import java.util.ArrayList;

public class Main {
public static void main(String[] args) {
List<Integer> intList = new ArrayList<>();
List<? extends Number> numList = intList;
numList.add(20);
System.out.println(numList.get(0));
}
}
```

```
A. "20"と出力される。
B. NullPointerException が実行時にスローされる。
C. numList.add(20); の行でコンパイルエラーが発生する。
D. System.out.println(numList.get(0)); の行でコンパイルエラーが発生する。
```

```
正解: C
解説:
A. "20"と出力される。 - このコードはコンパイルを通過しないため、実行されて何らかの値が出力されることはありません。上限境界ワイルドカードを持つコレクションに要素を追加することは、原則として許可されていません。
B. NullPointerException が実行時にスローされる。 - プログラムはコンパイルの段階でエラーとなるため、実行されて NullPointerException のような実行時例外が発生することはありません。
C. numList.add(20); の行でコンパイルエラーが発生する。 - 正解です。ジェネリクスにおける上限境界ワイルドカード ? extends T は、そのコレクションが T または T のサブタイプのオブジェクトを生成する（Producer）ことを示します。コンパイラは numList が具体的にどの型（List<Integer>や List<Double>など）を参照しているかをコンパイル時に確定できないため、型安全性を破壊する可能性のある要素の追加（add 操作）を許可しません。
D. System.out.println(numList.get(0)); の行でコンパイルエラーが発生する。 - 上限境界ワイルドカードを持つコレクションから要素を取得する（get 操作）ことは安全であるため許可されています。コンパイラは、リストから取得した要素が少なくとも Number 型であることを保証できるため、この行でコンパイルエラーは発生しません。

```

88. 問題文: 以下のコードは java.io.Console クラスを使用してユーザーからの入力を読み取ろうとします。このコードを、対話型のコンソールが利用でき```java
    ない一般的な統合開発環境（IDE）内から実行した場合、最も可能性の高い結果はどれですか。

        import java.io.Console;

public class Main {
public static void main(String[] args) {
Console console = System.console();
try {
String input = console.readLine("Input: ");
System.out.println("Output: " + input);
} catch (Exception e) {
System.out.println(e.getClass().getSimpleName());
}
}
}

```

```

A. NullPointerException
B. IOException
C. プログラムが"Input: "と表示し、入力待ちになる
D. 何も出力されない

```

```

正解: A
解説:
A. NullPointerException - 正解です。IDE など、JVM にシステムコンソールが関連付けられていない環境でプログラムを実行すると、System.console()は null を返します。その結果、null である console 変数に対して readLine メソッドを呼び出そうとすると、NullPointerException がスローされます。この例外は catch ブロックによって捕捉され、そのクラスの単純名が出力されます。
B. IOException - Console.readLine()メソッドは IOException をスローする可能性がありますが、それは I/O エラーが発生した場合です。この状況では、readLine メソッドが呼び出される前に NullPointerException が発生するため、IOException がスローされることはありません。
C. プログラムが"Input: "と表示し、入力待ちになる - この動作は、プログラムがコマンドラインなど、対話型コンソールを持つ環境で実行された場合に期待されるものです。IDE 内では System.console()が null を返すため、このようには動作しません。
D. 何も出力されない - console 変数が null になるため、try ブロック内で NullPointerException がスローされます。この例外は catch ブロックで捕捉され、例外名が出力されるため、何も出力されないということはありません。

````

89. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

    import java.util.List;
    import java.util.Map;
    import java.util.stream.Collectors;
    import java.util.stream.Stream;

public class Main {
public static void main(String[] args) {
Stream<String> fruits = Stream.of("apple", "banana", "kiwi", "orange");
Map<Boolean, List<String>> result = fruits.collect(
Collectors.partitioningBy(s -> s.length() > 5)
);
System.out.println(result.get(true).size());
}
}
```

```
A. 1
B. 2
C. 4
D. NullPointerException がスローされる。
```

```
正解: B
解説:
A. 1 - 条件 s.length() > 5 に一致する文字列は "banana"（長さ 6）と "orange"（長さ 6）の 2 つ存在します。したがって、サイズが 1 になることはありません。
B. 2 - 正解です。Collectors.partitioningBy は、ストリームの要素を述語（Predicate）に基づいて 2 つのパーティションに分割します。このコードでは、文字列の長さが 5 より大きいかどうかを判定しています。"banana"と"orange"がこの条件を満たすため、キー true に対応するリストに格納されます。result.get(true)はこのリストを取得し、その size()を呼び出すため、結果として 2 が出力されます。
C. 4 - この数字は、分割前のストリームに含まれる要素の総数です。partitioningBy は要素をグループ化するものであり、result.get(true).size()はそのうちの一方のグループのサイズを返すため、総数とは異なります。
D. NullPointerException がスローされる。 - Collectors.partitioningBy は、述語に一致する要素が存在しない場合でも、キーに対応する空のリストを生成します。そのため、result.get(true)が null を返すことはなく、NullPointerException は発生しません。

```

90. 問題文: 以下の Counter クラスのインスタンスが 1 つあり、2 つの異なるスレッドがそれぞれ 1000 回ずつこのインスタンスの increment メソッ```java
    ドを呼び出します。すべてのスレッドの処理が完了した後、getCount メソッドが返す値に関する説明として最も適切なものはどれですか。

        class Counter {
        private int count = 0;

            public synchronized void increment() {
                count++;
            }

            public int getCount() {
                return count;
            }

        }

```

```

A. 最終的な値は 2000 になる。
B. 最終的な値は 1000 から 2000 の間の不確定な値になる。
C. どちらか一方のスレッドがロックを取得するため、最終的な値は 1000 になる。
D. IllegalMonitorStateException が実行時にスローされる。

```

```

正解: A
解説:
A. 最終的な値は 2000 になる。 - 正解です。increment メソッドに synchronized キーワードが付与されているため、このメソッドはスレッドセーフです。synchronized なインスタンスメソッドは、そのインスタンス（Counter オブジェクト）をロック（モニター）として使用します。これにより、一度に 1 つのスレッドしか increment メソッドを実行できなくなり、count++という操作が競合状態に陥ることを防ぎます。したがって、2 つのスレッドからの加算が漏れなく反映され、最終的な値は正確に 2000 になります。
B. 最終的な値は 1000 から 2000 の間の不確定な値になる。 - この現象は、increment メソッドが synchronized で保護されていない場合に発生する典型的なレースコンディションです。synchronized キーワードによって排他制御が保証されているため、このような結果にはなりません。
C. どちらか一方のスレッドがロックを取得するため、最終的な値は 1000 になる。 - synchronized のロックはメソッドの呼び出し単位で取得・解放されます。1 つのスレッドがすべての処理を終えるまでロックを保持し続けるわけではないため、両方のスレッドが交互に increment メソッドを実行し、両方の処理が最終的な結果に反映されます。
D. IllegalMonitorStateException が実行時にスローされる。 - この例外は、スレッドが所有していないモニター（ロック）に対して wait()や notify()などを呼び出した場合にスローされます。このコードではそのような操作は行われていないため、この例外は発生しません。

````

91. 問題文: 以下のインターフェースとクラスが定義されている場合、Main クラスの main メソッド内で記述するとコンパイルエラーとなるコードはどれで```java
    すか。

        interface Communicator {
        default void sendMessage() {
        System.out.println("Sending message...");
        }
        static void checkStatus() {
        System.out.println("Status OK.");
        }
        }

class Radio implements Communicator {
}

````

```
A. new Radio().sendMessage();
B. Communicator.checkStatus();
C. Radio radio = new Radio(); radio.checkStatus();
D. Communicator c = new Radio(); c.sendMessage();
```

```
正解: C
解説:
A. new Radio().sendMessage(); - このコードはコンパイルに成功します。sendMessage は default メソッドであり、実装クラスである Radio にインスタンスメソッドとして継承されるため、Radio のインスタンスから呼び出すことができます。
B. Communicator.checkStatus(); - このコードはコンパイルに成功します。checkStatus は static メソッドであり、インターフェース名 (Communicator) を通じて直接呼び出すのが正しい使い方です。
C. Radio radio = new Radio(); radio.checkStatus(); - 正解です。インターフェースで定義された static メソッドは、そのインターフェースの実装クラスやサブインターフェースには継承されません。したがって、Radio クラスのインスタンス変数 radio から checkStatus メソッドを呼び出すことはできず、この行はコンパイルエラーとなります。
D. Communicator c = new Radio(); c.sendMessage(); - このコードはコンパイルに成功します。default メソッドはインスタンスに属するため、インターフェース型の参照変数からも問題なく呼び出すことができます。これはポリモーフィズムの有効な使用例です。

```

92. 問題文: 以下のコードを実行した際に出力される内容として、最も適切なものはどれですか。

```java

    class BadResource implements AutoCloseable {
    public void process() {
    throw new IllegalArgumentException("Processing failed");
    }
    @Override
    public void close() {
    throw new IllegalStateException("Closing failed");
    }
    }

public class Main {
public static void main(String[] args) {
try (BadResource br = new BadResource()) {
br.process();
} catch (Exception e) {
System.out.print(e.getMessage());
if (e.getSuppressed().length > 0) {
System.out.print(", " + e.getSuppressed()[0].getMessage());
}
}
}
}

```

```

A. Closing failed
B. Processing failed
C. Closing failed, Processing failed
D. Processing failed, Closing failed

```

```

正解: D
解説:
A. Closing failed - try-with-resources 構文では、リソースの close()メソッドで発生した例外は、try ブロックで発生した例外を上書きしません。close()からの例外は抑制された例外として扱われるため、この出力にはなりません。
B. Processing failed - try ブロック内の process()メソッドからスローされた IllegalArgumentException は主たる例外として捕捉されます。しかし、その後にリソースがクローズされる際に close()メソッドからも IllegalStateException がスローされ、これは抑制された例外として追加されるため、この出力は最終的な結果として不完全です。
C. Closing failed, Processing failed - catch ブロックで捕捉される主たる例外は、try ブロック内で最初に発生したものです。この選択肢は、close()メソッドの例外が主たる例外であると誤解しており、順序が逆になっています。
D. Processing failed, Closing failed - 正解です。try-with-resources 文の実行中、まず br.process()で IllegalArgumentException がスローされます。try ブロックが異常終了するため、リソースの close()メソッドが自動的に呼び出されますが、ここでも IllegalStateException がスローされます。この場合、最初に発生した IllegalArgumentException が主たる例外として catch ブロックに渡され、close()メソッドの IllegalStateException は「抑制された(suppressed)例外」として主たる例外に追加されます。したがって、まず主たる例外のメッセージが出力され、次に抑制された例外のメッセージが出力されます。

```

93. 問題文: Employee レコードと SalarySummary レコードが以下のように定義されています。提示されたコードを実行した際の出力として正しいものは```java
    どれですか。

        import java.util.List;
        import java.util.stream.Collectors;

public class Main {
record Employee(String name, int salary) {}
record SalarySummary(long total, double average) {}

    public static void main(String[] args) {
        List<Employee> employees = List.of(
            new Employee("Alice", 5000),
            new Employee("Bob", 6000),
            new Employee("Charlie", 7000)
        );

        SalarySummary summary = employees.stream().collect(
            Collectors.teeing(
                Collectors.summingLong(Employee::salary),
                Collectors.averagingDouble(Employee::salary),
                SalarySummary::new
            )
        );
        System.out.println(summary);
    }

}

```

```

A. SalarySummary[total=18000, average=6000.0]
B. コンパイルエラーが発生する。
C. SalarySummary[total=6000, average=6000.0]
D. SalarySummary{total=18000, average=6000.0}

```

```

正解: A
解説:
A. SalarySummary[total=18000, average=6000.0] - 正解です。Collectors.teeing メソッドは、ストリームの要素を 2 つの異なるコレクターで同時に処理し、その結果をマージ関数で結合します。このコードでは、summingLong が給与の合計(5000 + 6000 + 7000 = 18000)を計算し、averagingDouble が給与の平均(18000 / 3 = 6000.0)を計算します。これらの結果が SalarySummary レコードのコンストラクタに渡されてインスタンスが生成され、その toString()表現が出力されます。
B. コンパイルエラーが発生する。 - Collectors.teeing は Java 12 で、record は Java 16 で正式に導入された機能です。このコードは Java 17 の環境では有効な構文であり、コンパイルエラーは発生しません。
C. SalarySummary[total=6000, average=6000.0] - この選択肢では合計給与の計算が誤っています。summingLong コレクターはストリームの全要素の合計を計算するため、18000 が正しい値です。
D. SalarySummary{total=18000, average=6000.0} - 計算された合計値と平均値は正しいですが、record のデフォルトの toString()メソッドが出力する書式が異なります。record の toString()は、クラス名に続いて角括弧[]を使用します。

````

94. 問題文: カレントディレクトリに以下の内容を持つ 2 つのファイル path1.txt と path2.txt が存在します。

```java
    path1.txt の内容: JavaTest
    path2.txt の内容: Javatest
    以下のコードを実行した際に出力される結果として正しいものはどれですか。（ファイル I/O に関する例外は発生しないものとします）

    import java.nio.file.Files;
    import java.nio.file.Path;
    import java.nio.file.Paths;
    import java.io.IOException;

public class Main {
public static void main(String[] args) throws IOException {
Path p1 = Paths.get("path1.txt");
Path p2 = Paths.get("path2.txt");
System.out.println(Files.mismatch(p1, p2));
}
}

```

```

A. 3
B. 4
C. -1
D. IOException がスローされる。

```

```

正解: B
解説:
A. 3 - Files.mismatch メソッドは、0 から始まるバイト位置で最初の不一致点を返します。ファイルの内容を比較すると、4 文字目（インデックス 3）の'a'までは一致しています。不一致は 5 文字目（インデックス 4）で発生します。
B. 4 - 正解です。Files.mismatch(Path, Path)メソッドは、2 つのファイルの内容をバイトごとに比較し、最初に異なるバイトがあった位置を 0 から始まるインデックスで返します。JavaTest と Javatest を比較すると、インデックス 0 から 3 までは一致していますが、インデックス 4 で T と t が異なるため、4 が返されます。
C. -1 - 戻り値として-1 が返されるのは、2 つのファイルの内容が完全に同一である場合です。この問題では、大文字と小文字の違いによりファイルの内容は同一ではないため、-1 は返されません。
D. IOException がスローされる。 - Files.mismatch はファイルの読み取り中に問題が発生した場合に IOException をスローする可能性がありますが、問題の前提条件で例外は発生しないとされています。ファイルの内容が異なること自体は例外の原因にはなりません。

```

95. 問題文: Connection オブジェクトが有効で、自動コミットモードが false に設定されているとします。以下の JDBC コードの断片を実行した後、変```java
    数 result の内容に関する説明として最も適切なものはどれですか。（Sales 部門には 10 人、Engineering 部門には 15 人の従業員が存在し、SQL の実行は成功するものとします）

        String sql = "UPDATE employees SET salary = salary \* 1.1 WHERE department = ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
        ps.setString(1, "Sales");
        ps.addBatch();

            ps.setString(1, "Engineering");
            ps.addBatch();

            int[] result = ps.executeBatch();

        }
        // ... catch, commit/rollback 処理

```

```

A. result は、バッチで実行されたステートメントの総数である 2 を格納する int 型変数となる。
B. result は、更新された行の総数である 25 を格納する int 型変数となる。
C. result は、{10, 15}という内容を持つ int の配列となる。
D. result は、処理が成功したことを示す true を格納する boolean の配列となる。

```

```

正解: C
解説:
A. result は、バッチで実行されたステートメントの総数である 2 を格納する int 型変数となる。 - executeBatch()メソッドの戻り値は、int 型の単一の変数ではなく int の配列です。また、配列の内容は実行されたコマンドの数ではなく、各コマンドによって影響を受けた行数です。
B. result は、更新された行の総数である 25 を格納する int 型変数となる。 - executeBatch()は、影響を受けた行の合計数を返すわけではありません。バッチ内の各コマンドがそれぞれ何行に影響を与えたかを要素とする配列を返します。
C. result は、{10, 15}という内容を持つ int の配列となる。 - 正解です。executeBatch()メソッドは、バッチに追加された順序で各コマンドを実行し、それぞれのコマンドによって更新された行数を要素とする int 配列を返します。最初のコマンド（Sales 部門）で 10 行、2 番目のコマンド（Engineering 部門）で 15 行が更新されるため、{10, 15}という内容の配列が result に格納されます。
D. result は、処理が成功したことを示す true を格納する boolean の配列となる。 - executeBatch()メソッドの戻り値の型は int[]です。Statement の execute()メソッドは boolean を返しますが、これは結果が ResultSet かどうかを示すものであり、バッチ処理の戻り値とは異なります。

````

96. 問題文: @Task アノテーションを、以下の ScheduledTask クラスのように一つのクラスに複数回適用できるようにしたいと考えています。この要件を```java
    満たすために必要なアノテーション定義の組み合わせとして、正しいものはどれですか。

        @Task(priority = "High")
        @Task(priority = "Normal")
        public class ScheduledTask {
        // class members
        }

````

```

A.
@interface Task { String priority(); }
@interface Tasks { Task[] value(); }

B.
import java.lang.annotation.Repeatable;
@Repeatable(Tasks.class)
@interface Task { String priority(); }
@interface Tasks { Task value(); }

C.
import java.lang.annotation.Repeatable;
@Repeatable
@interface Task { String priority(); }

D.
import java.lang.annotation.Repeatable;
@Repeatable(Tasks.class)
@interface Task { String priority(); }
@interface Tasks { Task[] value(); }

```

```

正解: D
解説:
A. この選択肢では、@Task アノテーション自体に@Repeatable メタアノテーションが付与されていません。アノテーションを繰り返し可能にするためには、このメタアノテーションでコンテナアノテーションを指定することが必須です。
B. この選択肢では、コンテナアノテーション@Tasks の value メソッドの戻り値が Task 型になっています。繰り返し可能アノテーションの仕様では、コンテナアノテーションは、繰り返されるアノテーションの配列型を返す value メソッドを持つ必要があります。したがって、戻り値は Task[]でなければなりません。
C. この選択肢では、@Repeatable メタアノテーションに引数が指定されていません。@Repeatable を使用する際は、そのアノテーションを格納するコンテナアノテーションの.class リテラルを引数として渡す必要があります。
D. 正解です。アノテーションを繰り返し可能にするためには、2 つの要素が必要です。まず、繰り返したいアノテーション（@Task）に@Repeatable メタアノテーションを付け、その引数にコンテナとなるアノテーション（Tasks.class）を指定します。次に、そのコンテナアノテーション（@Tasks）を定義し、その中で繰り返したいアノテーションの配列型（Task[]）を返す value という名前のメソッドを宣言します。この選択肢は、これらの要件を正しく満たしています。

```

97. 問題文: Product レコードのリストがあり、これを部署ごとにグループ化し、各部署の製品数を数える Stream API のコードがあります。以下のコード
    を実行した結果、コンソールに出力されるものはどれですか。（注：マップ内のエントリの順序は実行によって異なる場合があります）

```java
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class Main {
record Product(String name, String category) {}

    public static void main(String[] args) {
        List<Product> products = List.of(
            new Product("Laptop", "Electronics"),
            new Product("Mouse", "Electronics"),
            new Product("Desk", "Furniture"),
            new Product("Chair", "Furniture"),
            new Product("Keyboard", "Electronics")
        );

        Map<String, Long> countByCategory = products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.counting()
            ));

        System.out.println(countByCategory);
    }

}

```

```
A. {Furniture=2, Electronics=3}
B. {Furniture=[Desk, Chair], Electronics=[Laptop, Mouse, Keyboard]}
C. {Furniture=3, Electronics=2}
D. コンパイルエラーが発生する

```

```

正解: A
解説:
A. {Furniture=2, Electronics=3} - 正解です。Collectors.groupingBy は、第一引数に指定された分類関数（Product::category）に基づいてストリームの要素をグループ化します。第二引数の下流コレクタ Collectors.counting()は、各グループの要素数を計算します。"Electronics"カテゴリには 3 つの製品が、"Furniture"カテゴリには 2 つの製品があるため、このマップが生成されます。
B. {Furniture=[Desk, Chair], Electronics=[Laptop, Mouse, Keyboard]} - この結果は、Collectors.groupingBy に分類関数のみを渡し、下流コレクタを省略した場合（Collectors.groupingBy(Product::category)）に得られます。その場合、値は List<Product>になりますが、このコードでは Collectors.counting()が指定されているため、値は Long 型になります。
C. {Furniture=3, Electronics=2} - この選択肢は、各カテゴリの製品数を逆に数えています。"Electronics"カテゴリの製品は 3 つ、"Furniture"カテゴリの製品は 2 つです。
D. コンパイルエラーが発生する - このコードは Stream API の Collectors.groupingBy メソッドを正しく使用しており、構文上の誤りはありません。レコードのメソッド参照（Product::category）の使用も有効です。したがって、コンパイルは成功します。

```

98. 問題文: 以下のコードを実行した際に出力される結果として正しいものはどれですか。

```java

import java.util.NavigableSet;
import java.util.TreeSet;

public class Main {
    public static void main(String[] args) {
        NavigableSet<Integer> scores = new TreeSet<>();
        scores.add(78);
        scores.add(85);
        scores.add(92);
        scores.add(64);

        System.out.print(scores.floor(85) + ", ");
        System.out.print(scores.lower(85));
    }

}

```

```

A. 85, 85
B. 78, 78
C. 92, 78
D. 85, 78

```

```

正解: D
解説:
A. 85, 85 - floor(e)と lower(e)メソッドは、引数 e がセット内に存在する場合、異なる値を返します。floor(e)は e 自身を返しますが、lower(e)は e よりも小さい値を返すため、この選択肢は正しくありません。
B. 78, 78 - floor(85)メソッドは、85 以下の最大の要素を探します。セット内に 85 が存在するため、このメソッドは 85 を返します。したがって、この選択肢は floor メソッドの解釈が誤っています。
C. 92, 78 - floor(85)メソッドは 85 以下の要素を探すため、85 より大きい 92 を返すことはありません。92 は higher(85)や ceiling(86)などで返される値です。
D. 85, 78 - 正解です。TreeSet は NavigableSet を実装しており、要素は自動的にソートされます(64, 78, 85, 92)。floor(85)は 85 以下の最大の要素を返すため、セットに含まれる 85 自身が返されます。lower(85)は 85 より小さい最大の要素を返すため、78 が返されます。

```

99. 問題文: 以下の Product レコードと main メソッドが定義されています。コードを実行した際に出力される結果として正しいものはどれですか。

```java

import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class Main {
record Product(String category, int price) {}

    public static void main(String[] args) {
        List<Product> products = List.of(
            new Product("Electronics", 1200),
            new Product("Books", 30),
            new Product("Electronics", 800),
            new Product("Books", 20),
            new Product("Clothing", 50)
        );

        Map<String, Long> result = products.stream()
            .collect(Collectors.groupingBy(
                Product::category,
                Collectors.counting()
            ));
        System.out.println(result);
    }

}
```

```
A. {Electronics=2, Books=2, Clothing=1}
B. {Electronics=2000, Books=50, Clothing=50}
C. {Electronics=[Product[category=Electronics, price=1200], Product[category=Electronics, price=800]], Books=[Product[category=Books, price=30], Product[category=Books, price=20]], Clothing=[Product[category=Clothing, price=50]]}
D. コンパイルエラーが発生する。
```

```
正解: A
解説:
A. {Electronics=2, Books=2, Clothing=1} - 正解です。Collectors.groupingBy は、第一引数で指定された分類関数（ここでは Product::category）に基づいてストリームの要素をグループ化します。第二引数のダウンストリームコレクター Collectors.counting()は、各グループに含まれる要素の数を数えます。したがって、各カテゴリとそのカテゴリに属する製品数の Map が生成されます。（Map のキーの順序は実行によって変わることがあります。）
B. {Electronics=2000, Books=50, Clothing=50} - この出力は、各カテゴリの price の合計を計算した場合の結果です。これは、ダウンストリームコレクターとして Collectors.summingInt(Product::price)を使用した場合に得られますが、問題のコードでは Collectors.counting()が使用されています。
C. {Electronics=[...], Books=[...], Clothing=[...]} - この出力は、groupingBy メソッドでダウンストリームコレクターを省略した場合（デフォルトで Collectors.toList()が適用される）の結果です。問題のコードでは Collectors.counting()が明示的に指定されているため、各カテゴリの製品リストではなく、製品数が値となります。
D. コンパイルエラーが発生する。 - このコードは、Java 17 で有効な record、メソッド参照、および Stream API の collect と groupingBy を正しく使用しています。構文上の誤りはないため、コンパイルエラーは発生しません。

```

100.    問題文: 以下のコードを実行した際の結果に関する説明として、最も適切なものはどれですか。

```java

        import java.util.List;
        import java.util.ArrayList;
        import java.util.stream.IntStream;

public class Main {
public static void main(String[] args) {
List<Integer> list = new ArrayList<>();
IntStream.range(0, 10000)
.parallel()
.forEach(list::add);
System.out.println(list.size());
}
}

```

```

A. 実行するたびに常に 10000 が出力される。
B. ArrayList がスレッドセーフでないため、コンパイルエラーが発生する。
C. 実行時に ConcurrentModificationException がスローされることが保証される。
D. 競合状態が発生するため、10000 と出力される保証はなく、実行ごとに結果が異なる可能性がある。```

```

```

正解: D
解説:
A. 実行するたびに常に 10000 が出力される。 - parallel()ストリームは処理を複数のスレッドに分割して実行します。ArrayList はスレッドセーフではないため、複数のスレッドから同時に add 操作を行うと、一部の操作が失われる可能性があります。そのため、サイズが常に 10000 になるという保証はありません。
B. ArrayList がスレッドセーフでないため、コンパイルエラーが発生する。 - このコードは構文的に正しいため、コンパイルは成功します。スレッドセーフティの問題は実行時に発生するものであり、コンパイラはこのような潜在的な問題を検出しません。
C. 実行時に ConcurrentModificationException がスローされることが保証される。 - 競合状態によって ConcurrentModificationException や ArrayIndexOutOfBoundsException などの例外がスローされる可能性はありますが、必ずスローされるとは限りません。例外が発生せずに、単にリストのサイズが期待値より小さくなるという結果で終了することもあります。
D. 競合状態が発生するため、10000 と出力される保証はなく、実行ごとに結果が異なる可能性がある。 - 正解です。ArrayList は同期化されていないコレクションです。並列ストリーム(parallel())とステートフルな中間操作（この場合は終端操作 forEach で外部の list の状態を変更している）を組み合わせると、複数のスレッドが同時に list にアクセスして変更しようとするため、競合状態（レースコンディション）が発生します。その結果、リストの最終的なサイズは予測不可能となり、10000 に満たない値になるか、あるいは実行時例外で失敗する可能性があります。

```

