**TODO** 表記ブレを修正する

# JSON Datatype Schema Language Types

JSON-DSで定義されプロセッサの環境から利用可能でなければならないJSON-DSデータ型を定義する。++また、プロセッサはここで定義される以外の独自の型を定義しても良い。++

## JSON-DSが定義するJSONの型について

JSON-DSではJSON valuesが何らかの型に属するという考え方に基づいて型システムを導入する。

+ JSON String valueはstring型
+ JSON Number valueはnumber型
+ JSON Boolean valueはboolean型
+ JSON Null valueはnull型
+ JSON Object valueはObject型ではなく、Record型
+ JSON Array valueはArray型
+ そのほかに共用型とany型と
+ そして、null許容型と非null許容型が存在する

また、JSONに存在しないprimitive valueではないString objectやNumber Objectなどは扱わないものとする。


## **TODO** The Subtype and Type Equivalence Relations

### The Subtype Relations

JSON-DSは基本的に部分型を扱わないため部分型は存在しない。そのため部分多相なレコード型や配列型もない。また、StringType,NumberType,BooleanTypeはnominal typesの部分型であるがこれは仕様を説明する上での便宜的なものであり仕様に含まれるものではない。

### Type Equivalence Relations

<article>
<header>注意：</header>
JSON-DSはes4に着想を得て設計されていますが、プリミティブ値の型がNominalTypeの部分型のClassTypeのインスタンスであるes4とは違い、JSON-DSでは識別子string,number,booleanといったプリミティブ値の型は直接にNominalTypeの部分型（StringType、NumberType、BooleanType）のインスタンスとなります。
その理由はes4には存在するClass objectがJSONやes5,6に存在せず、さらに言語によってはクラスをオブジェクトとして扱えない言語も存在するので言語中立性のためにclass objectという概念は導入しません。

このことはJSON-DSではオブジェクトをnominalな部分型として定義することは出来ないという事実を自明します。オブジェクトの型はstructuralな代数的データ型としてのみ定義できます。
</article>


## Meta-objects

JSON-DSの型システムには型に対する型があり、それをmeta-objectsと呼ぶ。JSON-DSプロセッサの環境に定義された各型を表す識別子（その一部はキーワードかも知れない）は何らかのmeta-objectのインスタンスとなる。

以下の通り各JSON valuesは型に属し、型はそれぞれのmeta-objectsとして扱われる。

1. 各JSON valuesはある型のインスタンスであり、
2. 型は何れかのmeta-objectsのインスタンスである。

++のインスタンスである++の部分は++に属する++と読み替えることが出来る。

これらのmeta-objectのインスタンスはJSON-DS仕様によってビルトインか、または新たに定義することで利用できる。

## JSON-DSが定義する型とMeta-objectsの種類

### NullType object

#### null型

```
type stringOrNullType = (string | null);
```

nullを表す型とそのmeta-object。

See EcmaScript 2015 6.1.2 - The Null Type.

- - - -

### NullableType object

#### nullable型

```
// type stringOrNullType = (string | null);
type stringOrNullType = string?;// 最後の ? に注目
```

nullを許容する型。

- - - -

### NonNullableType object

#### non-nullable型

```
type FooRecord = {
	"foo": fooType
} ! ;// 最後の ! に注目
```

nullを許容しない型。

- - - -

### AnyType object

#### *型

```
// フィールドvalueの型がany type
type Box = {
	"value": *
};
```

すべての型を許容する型（*はanyと読む）とそのmeta-object。

- - - -

## Nominal-types

string型、number型、boolean型に対応するStringType、NumberType、BooleanTypeの各種meta-objectsはNominalTypeの部分型である。NominalTypeに属するmeta-objectはプリミティブ値の型を表す。

- nominal-typeはすべてnon-nullable

nominal-typesはここに挙げる型以外にも定義してもよい。しかし、オプションの型以外のすべての標準の型はサポート**されなければならない**。

### BooleanType object

#### boolean型

```
type checked = boolean;
```

`true`か`false`を表す型とそのmeta-object。++一般的な代数的データ型と違いnominal-typeである。++

See EcmaScript 2015 6.1.3 - The Boolean Type.

### StringType object

#### string型

```
type Name = string;
```

文字列を表す型とそのmeta-object。

See EcmaScript 2015 6.1.4 - The String Type.

### NumberType object

#### number型

```
type Point = {
	"x": number,
    "y": number
};
```

64bit精度の浮動小数点数を表す型とそのmeta-object。IEEE 754-2008標準に準拠している必要はないが**してもよい。**

See EcmaScript 2015 6.1.6 - The Number Type.

### Machine-types

Machine-typesは他のプログラミング言語との親和性のために追加されるオプションの型とそのmeta-objectsである。これらのmeta-objectsのすべてはNominalTypeの部分型として定義されなければ**ならない。**

Machine-typesが利用可能なシナリオはJSONプロセッサがJSON number valueとこれらmachine-types valueを明確に区別できる場合のみである。++そのためには何らかの方法でJSONプロセッサが拡張されていなければならない。++

例えばJSONの構文に以下のようなルールでリテラルを追加する。

+ *`1`* and *`3i`* are both *int*
+ *`2147483648`*, *`0x20`*, and *`35u`* are all *uint*
+ *`37.5`*, *`7e4`*, *`8d`*, and *`8589934592`* are all *double*
+ *`17.5m`* is *decimal* (the m is for **“money”**)

Machine-typesのサポートは必須**ではない。**

#### ByteType object

##### byte型

**TODO** 符号付きでいいか？
符号付き8bit整数を表す型とそのmeta-object。

#### IntType object

##### int型

符号付き32bit整数を表す型とそのmeta-object

#### UintType object

##### uint型

符号無し32bit整数を表す型とそのmeta-object

#### DoubleType object

##### double型

64bit精度の浮動小数点数を表す型とそのmeta-object。++IEEE 754-2008 Binary64であることが望ましい。++

#### DecimalType object

##### decimal型

128bit精度の浮動小数点数を表す型とそのmeta-object。++IEEE 754-2008で定義される丸めモードhalf even、34 桁のDecimal128 形式であることが望ましいがそうで**なくてもよい。**++

#### AnyNumberType object

##### AnyNumber型

以下と同義
```
type AnyNumber = (byte | int | unit | double | decimal | number);
```

#### FloatNumberType object

##### FloatNumber型

以下と同義
```
type FloatNumber = (double | decimal);
```

- - -

以下はWebAssemblyとの親和性のために用意されたmachine-typesである。

### Int8Type object
#### int8型

### Int16Type object
#### int16型

### Int32Type object
#### int32型

### Int64Type object
#### int64型

### Float32Type object
#### float32型

### Float64Type object
#### float64型

### Uint8Type object
#### uint8型

### Uint16Type object
#### uint16型

### Uint32Type object
#### uint32型

### Uint64Type object
#### uint64型


- - - -

## structural-types

JSONで記述可能なオブジェクトの型とそのmeta-objectがstructural-typesとそのmeta-objectである。

- **TODO** structural-typesはすべてnullable

### RecordType object

#### record型

```
type RecordType = {
	"field0": fooType,
	"field1": barType,
	"field3": bazType
};
```

ラベル付きのフィールドを持つ一般的なレコード型でJSON Objectを表す型とそのmeta-object。

### ArrayType object

#### array型

インデックスを持つ一般的な配列型でJSON Arrayを表す型とそのmeta-object。

#### Fixed-length ArrayType object

```
type FixedLengthOne = [fooType];
type FixedLengthThreeFoo = [fooType, fooType, fooType];
type FixedLengthThreeKinds = [fooType, barType, bazType];
```

長さが固定のArrayType meta-object。

#### Variable-length ArrayType object

```
type VariableLengthZeroOrMore = [...VariableLengthElementType];
type VariableLengthOneOrMore = [fooType, ...fooType];
type VariableLengthTwoOrMore = [fooType, barType, ...VariableLengthElementType];
```

長さが可変のArrayType meta-object。

### UnionType object

#### union型

```
type UnionType = (fooType | barType);
```

代数的データ型における共用型を表す型とそのmeta-object。

- - -

## JSON-DS Specification Types

仕様型は仕様を記述する上でこの仕様書に現れる便宜上の型であり、仕様型そのものがこの仕様に含まれるわけではない。

### Nominal 仕様型

JSON string value, JSON number value, JSON boolean valueの型とそのmeta-objectを説明するために存在する型でこれらのmeta-objectのすべては便宜上Nominal 仕様型の部分型として扱われる。

### Undefined 仕様型

**TODO** Undefined 仕様型はTypeDefinition構文がhoistableの場合、仕様書以外にもエラーメッセージ等で出てくるがどう扱うべきか？**TypeDefinition構文がhoistableであるかはまだ決定事項ではない**。

**FIXME** 表現がおかしい
識別子がバインドされているが、++値が未定義の場合++を表す仕様型。

### Reference 仕様型

**Reference**は名前のバインディングを解決する。前方参照されたその時点ではUndefined 仕様型を参照している。

### NonNullableAny 仕様型

Null許容型++ではない++any-typeを表す仕様型。

以下の記述を表すために各実装内部で利用される事を想定した型
```
type AnyBadNullable = *!;
```

## Annex

### A. examples

```
type Person = {
	"first": string,
    "last":  string,
    "age": number,
    // または、利用可能であれば
    //"age": uint,
};

type JSON = Person!;
```