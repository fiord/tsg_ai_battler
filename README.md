# tsg_auto_chess
進化して生き残れ。

### ルール説明
DOTA AUTO CHESSはご存知でしょうか？これを題材にしたゲームです。
15x15の盤面上で行います。最初、盤面上には各プレイヤーのキングが両端（相手のキングは奥側、自分のキングは手前側）の中央のマスにいます。また、所持金が5goldあります。

毎ターンの初めにお金が貰えるので、やりくりしてユニットを購入したり自分のレベルを上げたりして、相手のキングのHPを0にしたら勝利です。同時にHPが0になった場合は引き分けです。1000ターン行って勝敗がつかなかった場合、キングの残りHPが多いほうが勝利。HPが同じなら引き分けとします。なお、時間切れとキングの死亡が同ターンにて行われた際も引き分けとします。

### ユニットについて
ユニットには以下のパラメータが存在します。
* HP…ユニットの体力。ユニットによって初期値は違いますが、0になると死亡し、ゲームから消滅します。evolveにより強化することが可能です。
* ATK…ユニットの攻撃力。ユニットによって初期値が違います。毎ターン攻撃によりATKのダメージを相手に与えます。evolveにより強化が可能です。
* TYPE…ユニットの属性です。ASSASSIN、WARRIOR、MAGEの3種類です。
  * ASSASSIN、WARRIOR、MAGEは3すくみになっていて、ASSASSIN→WARRIORへの攻撃はダメージが半減し、ASSASSIN→MAGEへの攻撃は2倍になります(後は察せ)
  * また、属性毎に特徴があります。詳細は後述。 
    * ASSASSIN…単純に攻撃力が高い
    * WARRIOR…単純にHPが高い
    * MAGE…攻撃力もHPもそれほど高くありませんが、攻撃範囲が広いです。
* ID…敵味方も含め全体を通してユニットはIDが付与されています。このIDを用いて指定を行います。IDを間違えると動かなくなる可能性が高いので注意(即反則負けになることは無いですが、無効な操作とされて無視されます)。キングのIDは0か1です。

### 各ターンにできること
以下のいずれかが出来ます。
* reset…売られているユニットのリセットが出来ます。2goldを消費します。
* buy…売られているユニットを購入することが出来ます。ユニット毎に価格が決まっています。
* move…ユニットを倉庫から盤面に出したり、逆に盤面から倉庫に入れることが出来ます。
* evolve…ユニットを進化させてステータスを上げることが出来ます。
* levelup…自分のレベルを上げることが出来ます。

また、それとは別に毎ターン盤面上の全ユニットに対して移動を行うことが出来ます。

### 各操作について
#### reset
同時に売られているユニットは3台で、buy(購入した駒の枠のみ)かgacha(全替え)しない限りターン経過でも販売内容は変化しません。

なので、「今売られているユニットは全部要らないな…」と思ったらgachaをしましょう。2goldを消費して販売内容を更新します。その際に「入荷されるユニットの属性を指定すること」が出来ます。(販売内容については後述)

#### buy
goldを消費してユニットを購入します。購入したユニットは倉庫(後述)に移動します。

購入すると販売されている3枠のうち1枠が空きます。ここには次ターン、新しいユニットが追加されます。

#### move
ユニットを倉庫から盤面に出したり、逆に盤面から倉庫に入れることが出来ます。キングは倉庫に入れることが出来ません。

倉庫に入れることでユニットの退避&evolveが可能になるので、積極的に使いましょう。

なお、ユニットを盤面に置く際は「キングからのチェビシェフ距離が3以内の何も無いマス」が条件です。また、盤面に出てきたユニットはそのターン移動及び攻撃が出来ません。ご注意ください。

#### evolve
AUTO CHESSにおける強力なシステムの一つ。倉庫内にある同属性のユニットを3体選び、それらを消費して1番ランクの高いユニットのランク+1のランクを持つユニットを1体得ることが出来ます。

evolveで得られるユニットのHP、ATKは素材にしたユニットのパラメータの平均値の2倍です。

evolveで得られたユニットは倉庫に入ります。IDは新しく振られます。

#### levelup
4goldを消費することで自分のレベルが1上がります。レベルが上がるメリットは「盤面上におけるユニット数が増加する」に尽きます。

盤面上における自ユニット数(キングを除く)=2+レベル(初めは1です)

です。強力なので覚えておきましょう。

### goldについて
1ターン目は5goldを持っています。

毎ターンの初め(入力が与えられる前)に以下のルールに従ってgoldが与えられます。
* 無条件に1gold
* 既にgoldを10gold以上持っている場合は10goldにつき1gold(上限5goldまで貰えます)
* 前ターンに敵ユニットを撃破している場合、1ユニットにつき1gold

### ユニットのステータスと販売価格について
自分のレベルが高くなると販売されるユニットのステータスも上昇します。乱数の影響でステータスが高いとユニットの販売価格も高くなります。
ここで、random(-1,1)は[-1, 1]の値が等確率で出ることに気をつけてください。なお、HPとATKの値は四捨五入され整数になります。
* ASSASSIN
  * HP…17+3\*(自分のレベル)+5\*random(-1, 1)
  * ATK…17+3\*(自分のレベル)+5\*random(-1, 1)
  * 攻撃範囲…チェビシェフ距離が1の範囲。要は隣接する8マスです。
* WARRIOR
  * HP…43+7\*(自分のレベル)+10\*random(-1, 1)
  * ATK…9+(自分のレベル)+3\*random(-1, 1)
  * 攻撃範囲…チェビシェフ距離が1の範囲。
* MAGE
  * HP…12+3\*(自分のレベル)+5\*random(-1, 1)
  * ATK…3+2\*(自分のレベル)+3\*random(-1, 1)
  * 攻撃範囲…チェビシェフ距離が3の範囲
* KING(販売はされません。また、属性相性がありません)
  * HP…100
  * ATK…20
  * 攻撃範囲…チェビシェフ距離が3の範囲

販売価格は2つのrandomの和+3を四捨五入した値(1~5gold)です。

### ユニットの移動について
各ユニットのID

### ゲームの流れについて
各ターン以下の流れに沿って繰り返してゲームを行います。動作は全てプレイヤー1→プレイヤー2の順に行います。

1. goldの付与(goldについて参照)
2. 入出力(フォーマットについては後述)
3. reset, buy, move, evolve, levelupの動作(前述)
4. ユニットの移動
5. ユニットの攻撃
6. 死亡ユニットの除外
7. 勝敗判定

#### ユニットの移動
全ユニットを通して毎ターンチェビシェフ距離が1の何も無いマスへ移動します。IDが小さい順に行動し、既に他のユニットが移動先にいる場合移動を諦めます(移動を行いません)。
また、盤面外へ出ようとするユニットも行動を諦めます。

#### ユニットの攻撃
ユニットは攻撃範囲内に敵ユニットがいた場合、攻撃を行います。
攻撃範囲内の全敵ユニットに「攻撃ユニットのATK*ユニット相性による係数」のダメージを与えます。

#### 死亡ユニットの除外
HPが0以下になったユニットを除外します。

#### 勝敗判定
キングユニットが死亡して除外されていた場合、ゲームに敗北します。また、残り時間が0以下の場合もゲームに敗北します。
詳細な勝敗ルールは冒頭で述べています。

### 入出力・制約・提出方法など
#### 制約・提出方法
合計して思考時間は3分以内。オーバーした場合は敗北となります。同ターンに思考時間をオーバーした場合は引き分けとなります。思考時間は「毎ターンこちらがinputを与えてからoutputを受け取るまでの累積時間」です。つまり、こちらでゲームの処理を行っている間にAIの探索を行える可能性があります。探索中にこちらが入力を与えた際の保証は一切しないので注意。
CPUやメモリについてはそのPCのスペックに依存するので本番実行環境をhakatashiに聞いてください。基本何をしてもOKです。

ソースコードとコメント（使用言語とコンパイルコマンドがメイン）を(hakatashiと相談してどこか)に投げてください。こちらで本番時にコンパイル・実行を行います。
ただし、ゲームの趣旨に合わない(ゲームプログラム本体にハッキングをしかけている等のチート行為を行っていると思われる行為を主に指します)コードが見られた場合、失格とします。

#### 入力
以下のフォーマットで入力が毎ターン与えられます。用いるのはTYPEを除き自然数(int型に収まることが保証されます)です。
```
turn
time_left
level_a level_b
gold_a gold_b
unitSellID_1 unitSellTYPE_1 unitSellHP_1 unitSellATK_1 unitSellVal_1
unitSellID_2 unitSellTYPE_2 unitSellHP_2 unitSellATK_2 unitSellVal_2
unitSellID_3 unitSellTYPE_3 unitSellHP_3 unitSellATK_3 unitSellVal_3
N
unitID_1 unitTYPE_1 unitHP_1 unitATK_1 unitX_1 unitY_1
unitID_2 unitTYPE_2 unitHP_2 unitATK_2 unitX_2 unitY_2
...
unitID_N unitTYPE_N unitHP_N unitATK_N unitX_N unitY_N
M
unitID_1 unitTYPE_1 unitHP_1 unitATK_1 unitX_1 unitY_1
unitID_2 unitTYPE_2 unitHP_2 unitATK_2 unitX_2 unitY_2
...
unitID_M unitTYPE_M unitHP_M unitATK_M unitX_M unitY_M
```
turnは現在のターン数です。
time_leftは残りの思考時間です。
level_aは自分のレベル、level_bは相手のレベルです。
gold_aは自分のgold、gold_bは相手のgoldです。
続く3行に販売されているunitの情報が出ます。TYPEは"WARRIOR"、"ASSASSIN"、"MAGE"、"KING"のどれかです。unitSellValは販売goldです。
次の行に自分のユニットの個数Nが与えられ、N行に渡り各行1ユニットの情報が与えられます。
次の行に自分のユニットの個数Mが与えられ、M行に渡り各行1ユニットの情報が与えられます。

なお、1ターン目においては例えば以下のような入力が与えられます。
```
0
180000
1 1
5 5
2 WARRIOR 50 10 3
3 MAGE 15 5 3
4 ASSASSIN 20 20 3
1
0 KING 100 20 7 0
1
1 KING 100 20 7 14
```

座標X,Yは0-indexで、(-1, -1)は自分の倉庫にあることを示します。自分のキングは最初必ずX=7,Y=0にいます(相手のキングはX=7,Y=14にいます)。

#### 出力
最初の1ターン目にプレイヤーの名前を1行で出力してください。他プレイヤーと判別がつくようにご協力お願いします。既に他プレイヤーの提出と名称が重なってしまっていた場合、こちらの判断で該当プログラム部分の変更を行う可能性があります。行う場合には通知しますが、予めご了承ください。
毎ターン以下のフォーマットに従って出力してください
```
command
N
ID_1 X_1 Y_1
ID_2 X_2 Y_2
...
ID_N X_N Y_N
```
* command…reset, buy, move, evolve, levelupの操作です。何もしない場合、「nop」もしくは左のどの操作にも該当しない文字列を出力してください。
    * reset…`reset TYPE_A TYPE_B TYPE_C`とリセット先3体の属性を指定してください。"ASSASSIN"、"WARRIOR"、"MAGE"のどれにも該当しない場合や出力されていない場合、ランダムに選択されます。goldが足りない場合は行われません。
    * buy…`buy ID`とIDを指定してください。IDが正しくない場合やgoldが足りない場合は行われません。
    * move…`move ID X Y`とIDと移動先を指定してください。移動先が倉庫のときは`-1 -1`としてください。盤面のユニット数上限をオーバーしたり、不正なIDでや座標を指定した場合は実行されません。
    * evolve…`evolve ID_1 ID_2 ID_3`と素材になる3体のユニットのIDを指定してください。IDのユニットが自分の倉庫に存在しない場合evolveは行われません。
    * levelup…`levelup`でlevelupします。goldが足りないときは行われません。

その後移動を行うユニットの数N(これは自ユニットの数である必要はありません)を出力し、N行に渡ってユニットIDと移動先の座標を空白区切りで出力してください。移動先とのチェビシェフ距離が1でなかったり、移動先が盤面外部の場合、移動先に既に他のユニットが存在する場合は移動しません。

### ゲームエンジン(main.rb)の使い方
サンプルAI同士でしか検証していません。サンプルAIが長期戦になるとバグる(ごめん見ている余裕がちょっと今無い)ので、バグらない試合でしか検証できてないのであれですが…

```ruby
ruby main.rb "{コマンド1}" "{コマンド2}"
```
で実行します。要は実行コマンドを""で括って空白区切りにしてargvに与えてあげてください。inputとoutputが無限に出力されます。

### 作問者兼ゲームマスターのfiordよりコメント
以上がTSG AUTO CHESSのルールです。絶対に1週間でやるレベルでは無いですが、個人的に「折角事前にAI公募を行うので見栄えが良さそうなものでやりたい」という思いがあり、ルールを書いていたら200行を超えて「やっちまった…」という現状があります。ごめんなさい。キレていいです。

ゲームルールについて質問があればslackで。ゲームバランスは見れていませんが(普通にMAGE強そうだけど、相性が強すぎるので)、時間も無いのでTSG LIVE3までに調整はしない予定です。
重要なゲームルールの欠陥があった場合、修正を行います。その場合はslackのaiチャンネルで通知します。