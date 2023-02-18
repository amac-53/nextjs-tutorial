# How Next.js Works
- React は build の方法やアプリケーションの構築の選択肢が多い(unoptionated)
- Nextでは，アプリの構築に関してフレームワークを提供する（開発とアプリケーションのプロセスをより高速にする）

- 開発段階では，TypeScript, ESLint を組み込んだり, Fast Refresh などで開発体験を向上
- プロダクションの段階では，エンドユーザが使用しやすいように最適化を行う（make it performant and accessible）
- 開発とプロダクションコードは目的が違うので，置き換えるために色々と手順が必要（compile, bundle, minify, code split）


## Development and Production Environments
### The Next.js Compiler 
- 実際，Next.js はコート変換とインフラを膨大に扱う
- Next のコンパイラは Rust と SWC（compliation, minification, bundling などを扱うプラットフォーム）で書かれているため可能となっている


## Compiling
- compiling は 特定の言語を取り込んで別の言語またはその言語の別のバージョンを出力することを指している
- Next.js では，開発段階でコードを書き換えたときと，プロダクション用のアプリを準備するビルド段階の一部として起こる


## Minifyng
- 開発段階では，人間が読みやすいようにコードを書いているが，アプリケーションを動かすには邪魔なだけなので，これを削除することでサイズを下げてパファーマンスを上げている
- Next.js では，JS と CSS は自動的にプロダクション用に minified される


## Bundling
- ファイルの依存関係を解決し，マージするプロセスを指す．これによりリクエストの数をへらすことができるため（HTTP/1.1の原則），ユーザがサイトに訪れたときに高速に処理可能


## Code splitting
- Code-splitting は，アプリのバンドルを，エントリーポイントごとに必要とされる小さな chunk ごとに分割するプロセスを指す
- そのページに必要なコードのみを返すことで，最初の読み込み時間を短くすることに狙いがある
- Next.js は，ビルトインでこれがサポートされていて，ビルド時には，`pages`ディレクトリに入っているファイルごとにバンドルされる
- 厳密には，複数のページで同じコードを共有している場合は重複をさけるために別のバンドルを用意していたり，初期ロード後，Next.js は他のページのコードの事前読み込みを始めたいり，`Dynamic imports`という初期ロードに使用されるコードを人為的に決める方法もある．


## Build Time vs Runtime
- Build time は，アプリをプロダクションコードに変換する一連の step を指す
- ビルド時に，Next.js はファイルをプロダクション用に変換するが，そのときに含まれるファイルは以下のものがある：
  - 静的ページを生成するための HTML ファイル
  - **server 上でページをレンダリングするための JS ファイル**
  - client とインタラクティブにやり取りするための JS ファイル
  - CSS files
- Runtime は，アプリケーションがビルドされ，デプロイされた後に，ユーザがリクエストを投げ，レスポンスが返ってくるまでの時間を指す


## Client and Server
- Client はユーザがやり取りできるブラウザを指しており，アプリケーションコードに対して，リクエストを送ってレスポンスを受け取る
- Server は，アプリケーションコードを置いているデータセンターの中のコンピュータを指し，client からリクエストを受け取り，何かしらの計算をして，適切なレスポンスを返す


## Rendering
- **React で書いたコードをブラウザごとの HTML 表現に変換する過程を rendering と呼んでいる**
- rendering は，client でも server でも起こり，build time 時に事前に起こったり， runtime 時の全てのリクエスト時に起こる
- Next.js では，３つのレンダリング（Server Side Rendering, Static Site Generation, Client Side Rendering）が可能
  
### Pre-Rendering
- SSR と SSG では，外部データの挿入や React component を HTML に変換することが，client 側に返ってくる前に起こるので，どちらも Pre rendering に言及している

### Client-Side Rendering vs. Pre-Rendering
- 通常の React アプリでは，ブラウザはサーバー側から空の HTML shell と JS の指示書を受け取り，client 側で UI の構築を行う（CSR）
- Next.js でも特定のコンポーネントで`useEffect()`や`useSWR`hooks を使用すると CSR が可能
- 対照的に，Next.js ではデフォルトで，全てのページで事前レンダリング（server 側で HTML を事前に生成すること）を行う
- 実際は，完全に CSR のアプリでは，レンダリングが終わるまでユーザは空のページが見えることになる
- ２種類の事前レンダリングがある

### Server-Side Rendering
- SSR では，リクエストごとに HTML ページを生成することになる．また，生成された HTML, JSON, JS といったページをインタラクティブにするものは client に送られる
- client 側では，HTML は高速でインタラクティブではないページを見せるために使用され，React は JSON や JS をコンポーネントをインタラクティブにするために使用される．このプロセスは，hydration と呼ばれる
- `React server components`は React 18 と Next 12 で導入された完全にサーバ側でレンダリングする機能で，client ではレンダリングに JS を必要としない（これにより．開発者は結果のみを返せばよいので，バンドルサイズがヘリ結果的にさらなる高速化につながっている）

### Static Site Generation
- SSG もサーバー側で HTML を生成しているが，SSR と異なり，runtime にはサーバーを必要としない．コンテンツは デプロイ時の build time にのみ生成され，生成された HTML は CDN にストアされてリクエストごとに再利用される
- Next.js では，`getStaticProps`を使うことで静的にページ生成が可能
- `Incremental Static Regeneration`を使用することで，一度ビルドしたサイトを作り変えたり，更新したりできる（何かしら変更があったとしても全体を再ビルドする必要がない）


## CDNs and the Edge
- ネットはコンピュータのリンクやと思っといて
- **Next.js では，作ったアプリは origin servers, Content Delivery Networks(CDNs), the Edge に分配される**

### Origin Servers
- これは，自分で作ったアプリのコードのオリジナルを置いているサーバーを指す（他のサーバーと区別するために`origin`と付けている）
- origin server にリクエストが来ると，何かしら計算をしてからその結果を CDN に渡す

### Content Delivery Network
- CDN は静的ファイル（HTML や画像ファイル）を保存していて，世界中に点在している．リクエストが来ると，ユーザに最も近い CDN がキャッシュした結果を返す
- このおかげで，origin はリクエストごとに毎回計算を行う必要がなく（キャッシュがあればそれを返せばいい話），負荷が低減される．しかも地理的にユーザに近い CDN がレスポンスを返すので高速
- Next.js では，事前にレンダリングするので，CDN は静的な結果を保持するのに最適

### The Edge
- ネットワークの淵を一般化した概念で，CDNs もネットワークの淵で静的コンテンツを持っているので the Edge の一部と考えられる
- CDNs と同じで，Edge server も世界各地に点在している．しかし，静的コンテンツを保存するだけでなく，少しのコードを保存できるという点で異なる
- つまり，**cashing と code exection はユーザのそばの the Edge で可能だということがわかる**
- 従来，client, server 側で処理されていたものを the Edge に送ると，送るコードの量は減るからアプリのパフォーマンスは良くなるし，ユーザのリクエストの一部がわざわざ origin server までいかんくていいからレイテンシを減らすことにもつながる