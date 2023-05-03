# 利用方法
インポート方法は[こちら](https://github.com/geekfujiwara/PowerAppsTemplates)に記載があります。
インポート後キャンバス棚卸アプリはデータソースとは接続の必要はなく独立して利用することができます。
![image](https://user-images.githubusercontent.com/96101315/236043097-3206681f-d2ba-41f7-8006-68be3ec5ae5f.png)

## App.OnStart
データソースに接続していないため、アプリの起動時に一度だけ実行されるApp.OnStart でデータソースを作成しています。
![image](https://user-images.githubusercontent.com/96101315/236046391-bd8143fd-0a52-4a75-8472-b26a56600b59.png)

### 実行
App.OnStart は開発画面では何度でも手動で実行することができます。
もし、初期化したくなった時にはこちらの操作で行うことができます。
![image](https://user-images.githubusercontent.com/96101315/236046896-a7071fc0-647c-4f56-864f-ad880e4d9b14.png)

### コードの説明
コードを説明します。こちらはご自身のデータソースに置き換えることで、データを記録できるサービスに変更することができます。

#### お知らせ
![image](https://user-images.githubusercontent.com/96101315/236048635-83c5f650-fca6-439a-b1e1-c903a0ed6b2d.png)

トップ画面に表示しているお知らせのデータソースを作成しています。
```JavaScript
// お知らせの内容
Set(_NotifyMessage, "5月10日は終日メンテナンスです。");
```

ご自身のデータソースに変更する場合。最新のお知らせ一件をとってきて表示するのはいかがでしょうか。
例えば、`First(データソース名).Title`のような形式でSharePointリストのタイトルのお知らせ情報を撮ってくる仕組みとしても良いでしょう。

#### 機能の設定
![image](https://user-images.githubusercontent.com/96101315/236048780-66b0f5b7-8ad3-449d-b36c-9a0d436b7853.png)
Gallery形式で表示している機能一覧をコレクションとして格納しています。`Name` は機能名です。
機能ごとにアクセス権を設定するために`IsPlannerRole`または`IsApproverRole`で制御しています。次の`// ユーザーの設定`にて権限を持つグループを設定しています。
```JavaScript
//機能の設定
ClearCollect(_PlanFunctions, 
    {Name:"棚卸計画の作成", IsPlannerRole:true},
    {Name:"棚卸計画の参照", IsPlannerRole:false},
    {Name:"棚卸計画の削除", IsPlannerRole:true}
);

ClearCollect(_CountFunctions, 
    {Name:"棚卸の実施", IsApproverRole:false}, 
    {Name:"棚卸の参照", IsApproverRole:false}
);
```
#### ユーザーの設定
ユーザーの設定では権限を持つグループを分けています。いわゆるセキュリティグループを作成しています。
```JavaScript
//ユーザーの設定
ClearCollect(_Operators, User());

//棚卸計画を作成、削除可能なユーザーリスト
ClearCollect(_Planners, User());

//承認可能なユーザーリスト
ClearCollect(_Approvers, User());
```
もし、こちらをデータソースとしてSharePoint リストに変更するのであれば`セキュリティグループ`というような名称のリストを作成して、リストのタイトルに`Operators`, `Planners`, `Approvers` として保存します。
![image](https://user-images.githubusercontent.com/96101315/236051655-c8ec1eb4-d488-4330-9cad-2798f4934245.png)

2つ目のリストとして、例えば`アクセス可能ユーザー`というSharePoint リストを作成して、列を2つ追加します。
`参照`列として先ほどの`セキュリティグループ`リストを設定します。

もう一つ、`ユーザー`列を追加してそれぞれのセキュリティグループごとのユーザーを追加します。
これにてグループを作成することができます。完成形は以下のようなリストを想定しています。
![image](https://user-images.githubusercontent.com/96101315/236052210-6cd5078a-3e16-4566-8891-44bc0f5ca29b.png)

[!TIP]
Titleは必須入力をはずしてもいいでしょう。

```JavaScript
// メニューの設定
ClearCollect(MenuItems,
{No:1, Name:"ドラフト"},
{No:2, Name:"申請済"},
{No:5, Name: "完了"});
 
// メニュー数のカウント
Set(MenuItemCount, CountRows(MenuItems));
```
```JavaScript
//テーマの設定
Set(_GeekThemeMainColor, RGBA(6, 138, 216, 1));
Set(_GeekThemeBackgroundColor, RGBA(237, 237, 237, 1));
```
```JavaScript
// サンプルデータの生成
ClearCollect(_Aisles, 
    {No:1000, Name:"第一通路"}, 
    {No:2000, Name:"第二通路"}, 
    {No:3000, Name:"第三通路"}
);
```
```JavaScript
//品目マスターの設定
ClearCollect(_ItemMaster, 
    {No:"A0001", Name:"製品A0001", Unit:"個", Cost:8000}, 
    {No:"A0002", Name:"製品A0002", Unit:"個", Cost:10000}, 
    {No:"A0002", Name:"製品A0002", Unit:"個", Cost:10000}, 
    {No:"A0003", Name:"製品A0003", Unit:"個", Cost:12000},
    {No:"B0001", Name:"製品B0001", Unit:"個", Cost:8500}, 
    {No:"B0002", Name:"製品B0002", Unit:"個", Cost:15000}, 
    {No:"B0003", Name:"製品B0003", Unit:"個", Cost:17000}
);

```
```JavaScript
// 棚卸計画の初期化
Collect(_ItemCountingPlan, 
        {
            PlanName:"", 
            PlanDate:Today(),
            AisleNo: 1000,
            Operator: User()
        }
    );
Clear(_ItemCountingPlan);

// 棚卸結果の初期化
Collect(_ItemCountingResult, 
        {
            PlanName:"", 
            PlanDate:Today(),
            DoneDate: Today(),
            AisleNo: 1000,
            Operator: User(),
            ItemNo:"",
            Amount:0,
            Status:"提出済"
        }
    );
Clear(_ItemCountingResult);
```




