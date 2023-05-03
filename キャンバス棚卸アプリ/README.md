# 利用方法
インポート方法は[こちら](https://github.com/geekfujiwara/PowerAppsTemplates)に記載があります。
インポート後キャンバス棚卸アプリはデータソースとは接続の必要はなく独立して利用することができます。
![image](https://user-images.githubusercontent.com/96101315/236043097-3206681f-d2ba-41f7-8006-68be3ec5ae5f.png)

## App.OnStart
データソースに接続していないため、アプリの起動時に一度だけ実行されるApp.OnStart でデータソースを作成しています。
内容を説明します。

```JavaScript
// お知らせの内容
Set(_NotifyMessage, "5月10日は終日メンテナンスです。");
```

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
```JavaScript

//ユーザーの設定
ClearCollect(_Operators, User());

//棚卸計画を作成、削除可能なユーザーリスト
ClearCollect(_Planners, User());

//承認可能なユーザーリスト
ClearCollect(_Approvers, User());
```
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




