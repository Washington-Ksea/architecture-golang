# Clean Architecuter

## オブジェクト指向プログラミング

+ カプセル化 - プライベートなデータメンバーとクラスのパブリックなメンバー関数
+ 継承 - スコープ内の変数と関数のグループを再度宣言したもの
+ ポリモーフィズム - 依存関係逆転により、依存関係を絶対的に制御する能力 

　関節的な制御の以降に起立を貸すものである

## 関数型プログラミング

　代入に規律を与えている

# SOLID

## 目的

以下の性質を持つ中間レベル(モジュールのレベル)のソフトウェア構造を作ること

+ 変更に強いこと
+ 理解しやすいこと
+ コンポーネントを基盤として、多くのソフトウェアシステムで利用できること
 

## Single Responsibility Principle (SRP) - 単一責任の原則

　個々のモジュールを変更する理由がたった一つでけになるように、ソフトウェアシステムの構造が、使用する即死の社会的構造に大きな影響を受ける。言い換えると、モジュールはたった一つのアクター(変更を望む人たちをひとまとめにしたグループ)に対して責務を負うべきである。

```go
//一つのアクター(a)に対して責務を負う
type aInterface interface {
    Amethod1() string
    Amethod2() int
    ...
}

```

## Open-Closed Principle (OCP) - オープン・クローズドの原則

ソフトウェアを変更しやすくするため、既存のコードお変更よりも新しいコードの追加によって、システムの振る舞いを変更できるように設計すべきである。言い換えると、ソフトウェアの振る舞いは、既存の青果物を変更せず拡張できるようにすべきである。

 ```go

//aInterfaceを拡張したA (コードを変更せずオーバライド)

type A struct {
    a aInterface //SRPより
}

func (f *A)  Amethod1() string {
    //something
    f.a.Amethod1()
}

func (f *A) AmethodOther() string {
    //something
}
 ```

## Liskov Substitution Principle (LSP) - リスコフの置換原理

 交換可能なパーツを使ってソフトウェアシステムを構築するなら、個々のパーツが交換可能となるような契約に従わなければならないということ。
 OCPの例のAmethodは、交換可能であった。

## Interface Segregation Principle (ISP) - インターフェイス分離の還俗
 
 ソフトウェアを設計する際には、使っていないものへの依存を回避すべきだという原則

```go

//Bmethodは、Cで使用する必用はなく、Cmethodは、Bで使用する必用がないため、依存を回避している。
type A interface {
    Amethod1() string
    Amethod2() float64
}

type B interface {
    A
    Bmethod()
}
type C interface {
    A
    Cmethod(carg int)
}

type Binpl struct {
    param1 string
}

func (b Binpl) Amethod1() string {
    return b.param1
}

func (b Binpl) Amethod2() float64 {
    return 1.0 
}

type Cinpl struct {
    param1 string
    Cparam int
}

func (c Cinpl) Cmethod(carg int){
    c.Cparam = carg
}

func (c Cinpl) Amethod1() string {
    return c.param1
}

func (c Cinpl) Amethod2() float64 {
    return 2.0
}

```

## Dependency Inversion Principle (DIP) - 依存関係逆転の原則

上位レベルの方針のコードは、下位レベルの詳細のコードに依存すべきではなく、逆に詳細側が方針に依存すべきであるという原則

In Go,  
　+ 実装仕様なしで機能の情報を与えるinterfaceが必用です。
　+ パッケージに依存関係が必用な場合、その依存関係をパラメータとして使用する必用がある。

# Creation Design Pattern

## Factory method

Factory methodパターンにおいて、実装したクラスの詳細が未知でありながら、オブジェクト作成を可能にするhelper methodが定義されます。

 ```go

func NewImplHelperMethod(inplType, param1 string) A {
    switch(inplType) {
        case "B":
            return Binpl{param1,}
        case "C"
            return Cinpl{param1,}
        default:
            return nil
    }
}
 ```

## Abstract Factory

DIPにおいて依存したくないのは、システム内の変化しやすい具象要素である。そのため、変化しやすい具象クラスを参照せず、その代わりに抽象インターフェースを参照する。具象クラスを使用せず、異なる関係性/依存性をグループ化したFactory。

```go

//Factory of factories construct

type A1 interface{}
type A2 interface{}

type AbstractFactory interface {
    CreateA1() A1
    CreateA2() A2
}

type BFactory struct{}

func (f BFactory) CreateA1() A1 {
    return new(B1)
}

func (f BFactory) CreateA2() A2 {
    return new(B2)
}

type CFactory struct{}

func (f CFactory) CreateA1() A1 {
    return new(C1)
}

func (f CFactory) CreateA2() A2 {
    return new(C2)
}

type B1 struct{}
type B2 struct{}
type C1 struct{}
type C2 struct{}


func GetFactory(implType string) AbstractFactory {
    var factory AbstractFactory
    switch implType {
        case "B":
            factory = BFactory()
        case "C":
            factory = CFactory()
    }
    return factory
}

```

```go 

//client can use the abstract factory as follows
BFactory := GetFactory("B")
b1 := BFactory.CreateA1()
b2 := BFactory.CreateA2()

```

# Structural design patterns

オブジェクトやデザインのカッ形成において明確な境界を引くためのパターン。

## Adaptor

Adaptor オブジェクト　- Adapteeクラスのインスタンスで、ラップしたインスタンスの仕事を実行する。

```go

type Adaptee struct{}

func (a *Adaptee) ExistingMethod() {
    //something
}

type Adapter struct {
    adaptee *Adaptee
}

func NewAdapter() *Adapter {
    return &Adapter{new(Adaptee)}
}

func (a *Adapter) ExpectedMethod() {
    //something
    a.adaptee.ExistingMethod()
}

```

```go

adapter := NewAdapter()
adapter.ExpectedMethod()

```

## Bridge

+ 独立性の変更可能でさる実装から抽象性を切り離す。
+ 2つの分離ツリーによりインターフェイス層と実装層を分離する。

```go

type Abstraction struct{
    impl Implementer
}

func (r Abstraction) MethodOther() {
    r.impl.MethodA()
}

type Implementer interface {
    MethodA()
}

type ImplA interface{
    MethodA()
}

func (s ImplA) MethodA() {
    //Something
}

type ImplB interface{
    MethodA()
}

func (s ImplB) MethodA() {
    //Something
}

type SpecializedAbstraction struct {
    Abstraction
}

func (r SpacializedAbstraction) MethodOther() {
    //Something
    r.impl.MethodA()
}


```

```go

res1 := Abstraction{ImplB{}}
res1.MethodA()

res2 = SpecializedAbstraction{Abstraction{ImplA{}}}
res2.Method()
```

## Composite

```go

type ComponentInterface interface {
    MethodA()
    AddChild(ComponentInterface)
}

type Composite struct {
    children []ComponentInterface
}

func (c *Composite) MethodA() {
    if len(c.children) == 0 {
        //leaf
        return 
    }

    //composite

    for _, child := range c.children {
        child.MethodA()
    }
}

func (c *Composite) AddChild(child ComponentInterface) {
    c.children = append(c.children, child)
}

```

```go

func test() {
    var parent ComponentInterface
    parent = &Composite{}
    parent.MethodA() //only leaf

    var child Composite
    parent.AddChid(&Child)
    parent.MethodA() //one composite, one leaf
}

```

## Decoreter

```go

type Function func() 

//the decorator function
func LogDecorator(fn Functin) Functin {
    return func(params string) {
        Log.Println("decorator")
        func()
    }
}

```

