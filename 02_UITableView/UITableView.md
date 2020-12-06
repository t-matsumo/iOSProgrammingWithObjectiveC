---
marp: true
paginate: true
---

# UITableViewの使い方
※ドキュメントの内容から最低限必要な部分を要約したもの
※UITableViewを何のために使用するのかは知っている前提

---

# UITableViewの表示に必要なクラスとプロトコル

- UITableview
リスト全体を表すView
表示するセルの管理も行う
下記の２つのプロトコルを実装したクラスをdelegateとして使用して情報を表示する
※常にリスト全体を表示しているのではなく仮想スクロールになっている

- UITableViewDataSource
「何行表示するのか？」、「それぞれの行にはどのセルを表示するのか？」を制御するためのプロトコル

- UITableViewDelegate
「それぞれの行の高さは何pxか？」を制御するためのプロトコル

---

# UITableViewにセルを登録する
iOSで用意されているテンプレートのセルではなく自前のセルを使用するときは、
セルをTableViewに登録する必要がある
```objc
@interface UITableView

// identifierに指定した文字列を用いてセルのインスタンスを取得する
- (void)registerNib:(UINib *)nib forCellReuseIdentifier:(NSString *)identifier;

@end
```

---

# UITableViewに登録したセルのインスタンスを取得する
```objc
@interface UITableView
// 後で説明するtableView:cellForRowAtIndexPath内でセルのインスタンスを取得する場合に使用する
// 引数のidentifierにはセルを登録したときと同じ文字列を指定する
// ※tableView:cellForRowAtIndexPath以外の場所から読んではいけないメソッド
- (__kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier forIndexPath:(NSIndexPath *)indexPath;

// tableView:cellForRowAtIndexPath以外でセルのインスタンスを取得する場合に使用する
// 引数のidentifierにはセルを登録したときと同じ文字列を指定する
- (__kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier;
@end
```

---

# UITableViewのdelegate
UITableViewだけでは情報を表示できない
表示するために必要な情報をdelegateを経由して伝える必要がある
```objc
@interface UITableView

// UITableViewDataSource
//「何行表示するのか？」、「それぞれの行にはどのセルを表示するのか？」
@property(nonatomic, weak, nullable) id<UITableViewDataSource> dataSource;

// UITableViewDelegate
//「それぞれの行の高さは何pxか？」
@property(nonatomic, weak, nullable) id<UITableViewDelegate> delegate;

@end
```

---

# UITableViewDataSourceで実装するべきメソッド
Delegateなので必要なときに勝手に呼び出される
下記のコメントに書いた内容を開発者が実装する必要がある
```objc
@protocol UITableViewDataSource
// 引数で渡ってくるsection番目のセクションの行数を返す
// ※特別なことをしない限りセクション数は1で固定なので、TableView全体の行数を返せば良い
- (NSInteger)tableView:(UITableView *)tableView
 numberOfRowsInSection:(NSInteger)section;

// 引数で渡ってくるindexPathに対応した行のセルを返す
// セルに情報を入れるのもこのメソッドで行う
// ※indexPath.rowが行数
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath;
@end
```

---

# UITableViewDelegateで実装するべきメソッド
Delegateなので必要なときに勝手に呼び出される
下記のコメントに書いた内容を開発者が実装する必要がある
```objc
@protocol UITableViewDelegate

// 引数で渡ってくるindexPathに対応した行の高さを返す
- (CGFloat)tableView:(UITableView *)tableView
heightForRowAtIndexPath:(NSIndexPath *)indexPath;

@end
```

---

# TableViewを更新する方法
WebAPIの呼び出し完了時にデータを表示するときは
下記メソッドを呼び出してTableViewを更新する必要がある
```objc
@interface UITableView

- (void)reloadData;

@end
```
