# microservices-appeared-issue

マイクロサービスアーキテクチャ実現時に発生した組織と文化におけるアーキテクチャ課題について。

## 前提条件
あまり一般的に語られないマイクロサービスの課題について記載する。

## フロントエンドとバックエンドが一体化したチーム
後述の分離したチームと異なり、初期導入[学習コストやコミュニケーションコスト]がかかるが、あまり大きな問題は発生しなかった。

チーム解体前で上手く行っていたというのも要因として大きかったのかも知れない。

## フロントエンドとバックエンドが分離したチーム

### フロントエンドの実装の比重が大幅に大きくなる。
DDDは事前に実施し設計済であった。

→　SOA時代に良く言われていた「再利用可能なサービスの粒度」の設計が出来ていない可能性が高い。

→　移行プロジェクトの場合、ストレートにREST APIにコンバージョンするとこうなる確立が上がると想定される。

　　→　移行のタイミングで類似する機能や、クエリーを分析しカテゴライズするなどの再設計が必要になる認識。
  
　　→　DDD実施済で、Hibernetesや重量ORMを使用し適切に設計されている場合、恐らく上記のリスクや検討は減ることが想定される。

→　BFFが必要なのではないかという議論が時間が経ってから発生する。

→　OOUXやマイクロフロントエンドの必要性の発生や検討につながる。

### バックエンドサービスの仕様と実装が乖離する問題
ソースコードの修正にREST API仕様のドキュメントもしくはMock/fackサーバーの修正が追い付かない。もしくは忘れる。

→　ソースコードのコメントしてREST APIドキュメントを書くタイプは、恐らく乖離しまくる。

→　Javaで言うところのServlet FilterやInterceptorなどの追加実装でHTTPリクエストからREST API仕様を自動生成するアプローチが最も優れている。

　→　上記でドキュメントの生産性の向上と、テストコードの実行時（夜間自動実行などを含む）に同時にREST APIドキュメントを自動生成し、同時にgitに反映させてしまう。

### QAチームの発足の議論
上記の延長として、システムとしての品質[バグや実装モレ]が低下する。
※実装モレは移行プロジェクト固有の問題かもしれない。

→　上記の結果として、通常の開発チームが機能開発というDevとバグ改修というOpsを運用出来るキャパシティを超える。

　　→　この段階で一旦、広義のDevOpsが崩壊する。
  
　　→　コンテナやインフラやCI/CDパイプラインをメンテナンスするミッションのチーム[SREチームなど]がいる場合、そこが最後の砦となる。

→　そして、QAチームもしくはQA担当が生まれてしまう。

　　→　発足されたQAチームがE2Eテストを手動[ブラウザの打鍵]でやっていたら、完全にオワコン。
  
　　→　生産性と能力の低いテスターが生まれる。
  
　　→　testcaffeやcypress使ってテストコード作成して、自動化しないとこのチームの意義がなくなる。
  
　　　　→　但し、「E2Eテストコードは壊れやすい＝画面に変更が入りやすい」ためどこまでやるかは難しい。

☆「2枚のピザの法則　× 2 ＝　16名」位の規模ではQAチームは作らない方がよいというのが当方の認識。

　　外部サービス系の会社などの開発規模が大きい場合は、また別の話となる。
  
　　QAチームを発足するということは、「工程で品質を作りこめていない」ということになり、後工程に品質を移譲する開発プロセスになりがち。

### マイクロサービスではテストピラミッドに従うべきではない
モノリスなアプリケーションではテストピラミッドが当てはまるが、マイクロサービスではテストピラミッドは当てはまらないと考える。

上記の意見は、プロダクトオーナーと議論し意見を求めたが同様の見解となった。

マイクロサービスに限らず、アーキテクチャとしてはSPAになり、ビジネス価値もUX側の比重が上がったためか、

結合テスト以降のテストの比重が自然に上がったり、E2Eテストの重要性が増すという側面が生まれる。

実際にSpotifyではこれを「ハチの巣テスト方針」として以下URLのように説明している。

https://labs.spotify.com/2018/01/11/testing-of-microservices/?fbclid=IwAR1zSrEISneX55vtdK4nGeF8FM-sntdZdb7GREIO1WOaPHef99kBcJ-syTg

以上
