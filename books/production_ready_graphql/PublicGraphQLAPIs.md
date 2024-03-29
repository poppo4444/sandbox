## Public GraphQL APIs

APIは大きく3つのカテゴリーに分けることができます:
• プライベートAPI（組織内部向け）
• パートナーAPI（限定的なパートナーと共有）
• パブリックAPI（ほぼ誰でもアクセス可能な完全公開API）
興味深いことに、GraphQLは主にプライベートAPIやパートナーAPIで非常に人気があり、パブリックAPIでは少し人気が落ちます。この本を書いている時点では、Facebookも公開のGraphQL APIを持っておらず、より多くの人々がそれを内部で使用しています。私が知っている限りでは、ShopifyとGitHubが現在最も大きな公開GraphQL APIを持っているでしょう。なぜ公開のGraphQL APIがそんなに少ないのでしょうか？その理由はいくつかあると思います。

### Is GraphQL a Good Choice for Public APIs

注：
あなたのAPI設計に最も影響を与えるべきは、対象となるオーディエンスです。
NetflixのAPIについて話すDaniel Jacobsonによる

確かに、オープンまたは公開APIを実装する際に、GraphQLが良い考えなのかどうか疑問を投げかけることはできます。まず、それがどこから来たのかを見ると、Facebookはまだ公開のGraphQL APIを持っておらず、GraphQLが引き起こす問題よりも他の問題に対応するために設計されました。私がいつも取り上げる例は、FacebookのGraphQLクライアントRelayです。RelayはGraphQLサーバーの設計について多くの前提を置いていたため、私たちがRelayを使用しているクライアントをサポートしたいと思った場合、APIは特定の方法で設計しなければなりませんでした。デザインに関する章で見たように、これらのことの多くは一般的に良い考え方です（入力とペイロードのタイプ、接続）が、クライアントが特定のサーバー実装にそんなに結びついていたという事実は、API提供者が特定のタイプのクライアントを考慮に入れなければならないという意味です。
パブリックAPIの提供者として、すべてのクライアントがRelayを使用するとは限らないため、多くのクライアントがRelay固有のフィールドに強制されます。一時期、スキーマのRelay専用バージョンを作成したり、ルートに特別なRelayフィールドを作成したりすることを考えたこともあります。公開API提供者にとってはかなりの障壁です！その後、新しい「Relay Modern」実装がこれらの前提をベストプラクティスに変えてきました。これにより、サーバー側でRelayのすべての推奨事項を必ずしも実装しなくても、クライアントはRelayを使用することができます。これらの推奨事項の多くは、一般的なGraphQLのベストプラクティスとして移行してきました。
しかし、それはGraphQLがパブリックAPIにとって素晴らしい代替手段になることを意味するわけではありません。GraphQLは、大量のクライアントに対する可能性を公開する強力な方法になることができます。成功するパブリックAPIは、多くの場合、大量の可能なクライアントを意味します。API提供者が多くのさまざまなユースケースを処理することは非常に難しくなり、これらのユースケースを処理することが困難であれば、クライアントにとって負担になり、非常に一般的で「全員向け」のAPIにつながります。GraphQLはこのような状況を処理するのが非常に得意です。基本的に、タイプシステム、リゾルバーパターン、クエリ言語を組み合わせることで、提供者は既存のクライアントに追加コストをかけずに自分たちが望むだけの機能とバリエーションを定義することができます（通常大規模なウェブフックやHTTPエンドポイントペイロードを考えてみてください）。また、GraphQLはAPIエンジニアにとって良い、そして大抵の場合はシンプルなメンタルモデルとアーキテクチャを提供します。
問題は、パブリックAPIがよくわからない、またはすべてのクライアントの使用例を予測することを望まないために、一般的になる傾向があることです。パブリックAPIは通常、クライアントが非常に一般的なインターフェースを通じて新しい使用例を発明する可能性を許す方式で一般的です。もちろん、それはしばしば「全体に合わせる」問題につながるので、パブリックGraphQL APIは、クライアントが私たちのAPIプラットフォームを基にソリューションを構築するために十分な汎用性を提供し、一方でRESTが受けている「全体に合わせる」罠に陥らないというバランスを取るように努める必要があります。結局のところ、REST論文の著者でもあるRoy Fieldingさえも、そのトレードオフを認識しています:

注：
ただし、一定のインターフェースは効率を低下させます。なぜなら、情報はアプリケーションのニーズに特化した形ではなく、標準化された形で転送されるからです。RESTインターフェースは大規模なハイパーメディアデータ転送に効率的になるように設計されており、Webの一般的な場合に最適化されていますが、その結果、他の形式のアーキテクチャーインタラクションには最適ではありません。

GraphQLはより微妙に、そしてよりクライアント特有のものになる可能性がありますが、これは我々が最初にスキーマをこのような方法で設計しなければならないことを意味します。何十万ものクライアントが扱えるフィールドを一つずつ作成することは不可能なので、答えはどこか中間にあるはずです。第2章で取り上げたように、より具体的なフィールドを目指すことはよくある良い考えです。多機能で魅力的な非常に一般的なフィールドは、おそらく避けるべきです。重要なのは、クライアントの声を聞き、新しいユースケースを既存のものを過剰に使うよりも新しいフィールドとして表現することを躊躇しないことです。

まとめると、パブリックAPIの提供者として、私たちがクライアントの全てのニーズを予測したり、それらに対応したりすることは困難です。そのために役立つ方法の一つとしてGraphQLがあります。これは、提供者が多様な能力とバリエーションを定義し、クライアント側に余計なコストをかけずに対応することが可能です。しかし、我々は一方で、「全体に合わせる」トラップに陥ることを避けなければなりません。ここで重要となるのは、提供する機能が私たちのAPIプラットフォーム上でクライアントがソリューションを開発できる汎用性を持つこと、そして、RESTが批判されてきた「全体に合わせる」問題と同じ罠にはまらないことです。

### Lack of Conventions

公開APIとしてよく機能するのが規約で、実はHTTPはかなり良い標準です。我々がGraphQLを公開APIとして使用するとき、多くの場合、HTTP仕様にもっと依存したAPIが提供するものの多くを手放すことになります。

GraphQL APIでは多くのことが再発明されなければなりません。エラー、キャッシング、レート制限、タイムアウト、その他多くのことが現在、GraphQL APIプロバイダによってさまざまな方法で処理されています。これらの一部はベストプラクティスがまだ出現しているためですが、もう一部はGraphQLの仕様がこれについて何も言及していないためです。これは多くの面で賢明です。現在、GraphQLクライアントはあるプロバイダがこれらの要素をどのように処理するかを学ばなければなりません。GraphQLサーバーはデータ内のエラーを使っているのか、それともGraphQLエラーを使っているのか？クライアントがレート制限に達したとき、GraphQL APIは429を返すのか、それともカスタムエラーがエンコードされているのか？キャッシュヒントは？クライアントが新しいGraphQL APIとやり取りするたびに、これらすべてのことをどのように処理する必要があるかを探したり、ドキュメンテーションを通じて学んだりしなければなりません。これをHTTP APIと比較してみてください。そこでは、ほとんどの場合、あなたがHTTPクライアントを向けると適切な動作が期待できます。

もちろん、これらの多くは典型的なHTTP APIにとっても困難であり、そのために自分のロジックを実装する前に可能な限り多くのHTTPコンセプトを使用することを常にアドバイスされるわけです。公開されているGraphQL APIがHTTP上で429 TOO MANY REQUESTSを返すべきなのか、それとも以下のようなものを返すべきなのか？

```json
{
  "errors": [{
    "message": "Rate Limited",
    "code": "RATE_LIMITED",
  }]
}
```

429の場合、ほとんどのHTTPライブラリはこれらのリクエストを完全に処理します。他方、我々はクライアントに我々のAPIのための全く新しい「プロトコル」を学ぶことを要求しています。つまり、可能なすべてのエラーと、この特定のAPIでそれがどのように表現されるかを学ぶことです。これはなぜgRPCのようなもので生成されたクライアントが非常に人気があるのかを説明しています。それは、企業がユーザーに一般的なクライアントでこれらのシナリオを処理しようとするのではなく、自分達の概念をクライアントに生成できるからです。

私のアドバイスは、GraphQL APIが可能な限り規約に依存することです。クライアントは新しいGraphQL APIに直面するたびに新しい規約を学ばなければならないべきではありません。特に我々がGraphQLを技術として成功させたいと思うならば。

### With Great Power comes Great Responsibility

他に考慮すべき点があります：GraphQLがクライアントの手に力を与えてくれることが大好きです。しかし、これも心配の種です：この力と共に、責任もクライアントの手にあるべきですが、公開APIの場合、それはほとんどの時点でそうではありません。例えばモニタリングを取り上げてみましょう。GraphQLでは、モニタリング性能と回帰を見つける最も簡単な方法はおそらくクライアントサイドにあります。しかし、公開APIのプロバイダとして、我々はパフォーマンスの回帰に対して行動を起こす前にクライアントからのサポート要求が来るのを待つことはありません。第5章で触れたように、これはすべての照会の可能性がどのように振舞っているかを理解しようとする非常に複雑なモニタリングソリューションにつながります。それは既知の、内部的なクエリーの少数のセットで処理する方がはるかに簡単です。

GraphQLはあなたがクライアントを制御しているときに美しく機能します。なぜなら、多くのロジックがこのレイヤに移されるからです。公開APIとして、その制御を多く失ってしまいますが、それは大幅に複雑さを増加させます。それはあなたがそのクライアントを知っているとき、または彼らと話をすることができるとき（彼らが部屋を越えている場合）にもっと簡単です。しかし、彼らが何十万人もいて、あなたが良好なコミュニケーションチャネルを持っていないとき、物事はひどくなることがあります。

全体的に見て、GraphQLのスイートスポットは現在もまだずれたニーズを持つ既知のクライアント群を支えることにあります。それは公開APIの設定でも素晴らしい利点を提供できますが、これは現時点ではまだ解決されていない問題であることを認識しておくことが重要です。私はGraphQLを使ったオープンAPIをサポートするのを手間がかからなくするような、もっと多くの規約とツールが出てくることを期待しています。

### Summary

結論として、GitHubとShopifyの両方が示しているように、GraphQLは公開APIとして良い選択である可能性があります。しかし、この選択は、我々がスキーマを設計する際に非常に誘惑的な「ワンサイズフィットオール」の罠に陥らないように注意を払う必要があることを意味します。ジェネリックと特定の間に適切なバランスを見つけることが鍵ですが、既存のクライアントに影響を与えないのであれば、ユースケースを達成するための複数の方法を提供することを恐れないでください。

最後に、規約はまだ固定されているわけではありません。より多くのプロバイダが公開API用にGraphQLに向かうにつれて、クライアントがGraphQL APIから一貫性を期待できるようにすることが重要です。