# Create Your First App

- スクラッチからアプリを完成させようとすると，
  - Webpack 等でバンドリングを行った後，Babel 等の compiler で変換する必要
  - code splitting でプロダクション用に最適化
  - SEO と パフォーマンスの観点から静的に複数ページを事前レンダリングしておきたい（SSR, CSR を行いたい）
  - React アプリと data store を連携させるためにサーバーサイドのコードを書く必要がある
- フレームワークはこれらの要素を解決可能だが，一定のレベルの抽象度を持つ．（便利でないほどエンジニアとしてのスキルが必要で，経験にはなるが．．）
- Next.js では上記の問題を解決し，ビルド時に成功の谷に導ける
- Next.js はエンジニアの経験とビルトインの機能を多く持つ
  - page-based ルーチング機能（dynamic routes もサポート）
  - SSG, SSR の両方の事前レンダリングをページごとにサポート
  - 高速なページ読み込みを可能にするための自動 code splitting
  - 最適化された prefetching による CSR
  - ビルトインの CSS と Sass，CSS-in-JS library のサポートもある
  - Fast Refresh による開発環境サポート
  - サーバーレス機能に付随する API エンドポイントを構築する API routes
- このチュートリアルではブログを作る

# Create a Next App
- Node 10.13以降のものを使い，Windows なら Git for Windows をダウンロードして付属の Git Bash を使う or WSL を使うことをお勧めする（linux コマンドを使うので）
- create-next-app に `--example` をつけることで，[このテンプレート](https://github.com/vercel/next-learn/tree/master/basics/learn-starter)が使えるようになる


## Navigate Between Pages
- file system routing を使用して新しいページを作成
- Link コンポーネントを使って，Client-side のページ間ナビゲーションを可能にする
- code splitting と prefetching に関する build-in について学ぶ
- paging に関しては，**コンポーネントの名前はなんでもいいが，default export で export する必要がある**
- このページングシステムは HTML や PHP と似ており，代わりに JSX と React コンポーネントで書いているという感じ
- Nextjs では，`<a>`タグの代わりに`next/link`コンポーネントの`<Link>`を使い，navigation の動きを制御するために props も使用可能
- Next.js 12.2 以前では，`<Link>`を`<a>`でラップする必要がある
- **Client-side navigation は JS を使用しているので，ブラウザのデフォルトのナビゲーションよりも高速に動作する**
    - devtools で`<html>`の background プロパティを黄色に変える
    - link をクリックして，ページ間を行き来する
    - page 遷移よりも黄色が保持されているということがわかる
- 以上のことから，ブラウザは**ページ全体をロードしておらず**，client-side navigation が機能していることがわかる（かわりに`a`タグを使うと full refresh を行うのでこのような結果は得られない）
- Code splitting を自動で行うので，数百ページをもつアプリケーションの場合，最初のホームページにアクセスしたときに他のページを最初は読み込まないので速いはず
- リクエストを投げたページのコードのみをロードするということは，他と独立しており，そのページがエラーを吐いたとしても残りのアプリは動くということを意味する
- さらに言うと，プロダクションでは`Link`はブラウザの viewport に現れるので，Next はリンクされたページを自動的に prefecth しに行くため，所望のページのコードは高速


## Assets, Metadata, and CSS
- 追加したページにはスタイルが当てられていないので，当てに行く（Sass と CSS はビルトインのサポートがある）
- `<title>`などのメタデータや静的なものの扱いも行う
- pages と同じようにルートからアクセス可能な public ディレクトリに置く（Google Site Verification である`robots.txt` にも有効？）
- 通常の HTML では，img タグで書くが，これには画像を自分でレスポンシブにしたり，viewport に入った時しかロードされないなどの不便な側面がある．しかし，Next.js では簡単に扱える`Image`コンポーネントを提供している（リサイズ，最適化などのをデフォルトで提供している）
- ブラウザがサポートしていればの話だが，WebP format などの最近のフォーマットが使用可能（スマホなどの小さい viewport に大きい画像を送らずに済むなどのいい側面がある）
- 自動画像最適化は，CMS などの外部ソースにホスティングされていたとしても，最適化される
  
## Metadata 
- `pages/index.js`を見ると，`<head>`ではなく，`<Head>`が使われているのがわかる
- ここを追加して色々と設定可能
- 一般に，新たな機能追加を行うために，外部の JS を埋め込むが，`script`タグを使用すると，**いつ埋め込もうとしているのか**は明確でないし，render-blocking が発生して page content のローディングに遅延が生じるとそれこそパフォーマンスに悪影響なので，`next/script`の`<Script>`を使おうね
- `strategy`は，いつロードするかを記述する．`lazyOnload`はブラウザのアイドル時間にロードすることを指定
- `onLoad`は，ローディングが終わった直後に走るコードのこと．ここでは，ロードが無事に終了したことをコンソールに示している

## CSS
- CSS modules はコンポーネント単位で CSS が当てられる（ここではこの方法を採用）
- クラス名が異なるファイル間で被っても大丈夫な設計になっている
- Sass は，`.css`と`.scss`を import 可能
- Tailwind CSS のような PostCSS libraries が使用可能
- **styled-jsx, styled-components, emotion のような CSS-in-JS libraries も使用可能**
- CSS modules を使うなら，拡張子は`.module.css`でないといけない
- 使うなら，styles みたいな名前で import $\rightarrow$ styles.container を className として割り当て
- dev tools をみると，class名が`layout_container_{randomstring}`となっていて重複の心配がないことがわかる
- また，**code splitting の観点でも CSS modules を使うことでバンドルサイズが小さくなるので寄与する**
- 全てのページにスタイルを効かせたい場合，global CSS を使用するといい
- **`pages/_app.js`に import することで global CSS files（全体に当てられる CSS file） を追加できる**
- global CSS files はどんな場所にどんな名前で置いていてもいい（今回は以下のようにする）
  - トップレベルの styles ディレクトリに，global.css を作成（動かなかったら，npm run dev）
- **いろんなコンポーネントを通して使える utility classes**
- **アプリケーションを通して，utilitu class を使うことができ，`global.css`内でも utility class を使うことが可能（utility classes は method よりも selector を書く方に言及している）?**
- `Image`では priority 属性があると preload するらしい

## Styling Tips
### clsx library
- clsx は class 名を toggle（切り替え） できるライブラリで，`npm install clsx`でインストールするだけでそのまま使える（詳細は調べてね）

### PostCSS
- Tailwind などの PosCSS を使うなら，`postcss.config.js`というトップレベルファイルを作る必要がある
- その後`npm install -D tailwindcss autoprefixer postcss`で，postcss.config.cssにゴニョゴニョ書く．さらに tailwind.config.js にも content option を追記しておくことを推奨

### Sass
- `.module.scss` or `.module.sass` で Sass を使えるが，もちろん install は必要（`npm install -D sass`） 


## Pre-rendering and Data Fetching
- ブログのコンテンツを追加していく（ブログのコンテンツは file system に store するが，Headless CMS などの外部コンテンツも使える）

- デフォルトで，Next.js は事前レンダリングを行っている（client-side JSの代わりにサーバ側で事前に HTML ファイルを生成している）ため，パフォーマンス，SEO の観点でも良い
- 生成された HTML は，ページに関連する最小限の JS code と結びついており，ロードされたときに完全にインタラクティブにする（**hydration と呼ぶ**）
- chrome の JS をオフにすることで確認できる（そのままの React だとアプリが表示されないことに気づくはず）
- もし`<Link />`みたいなインタラクティブなコンポーネントを持っているなら，JS を load した後に active になるはず
- pre-rendering には2種類（SSG, SSR）あり，**違いはいつ HTML を生成するか**
  - SSG は，ビルド時に HTML を生成し，リクエストの度に再利用可能
  - SSR は，リクエストごとに HTML を生成する
- 開発時は，各ページはリクエストごとに事前レンダリングされ，プロダクション時には，build time に一度だけ SG が起こり，全てのリクエストでは起こらない（デフォルトでは）
- 実際には，ほとんどのページは SSG を使用し，一部に SSR を使用するという hybrid Next.js App も可能
- では使い分けはというと，基本は SSG で，事前レンダリングが可能か（ユーザのリクエストの前にページが用意できるか）を自分で吟味して，可能なら SSG を利用する．もし不可能（データをリクエストごとに変化させるとか）なら，SSR を利用し，めちゃめちゃ頻繁にデータを更新しておく必要があるのなら CSR を使うべき

## SG
- 外部データを使用するとき（外部 API を fetch する or DB に問い合わせとか）なら，Static Generation with Data を使用する
  - 具体的には，データを fetch するページに`getStaticProps`という非同期関数を書いて，コンポーネントに読み込ませることで Next.js が「これはなにかしらのデータ依存があるから，先に解決する必要があるな」と認識してくれるらしい（この関数はプロダクション環境では build time ，開発時にはリクエストごとに走る）（また，受け取る側のコンポーネントは`Home(props)`みたいにすることで，`getStaticProps`から return されるオブジェクトの props key のデータを受け取ることが可能）
- 今回のブログの投稿内容は，アプリのディレクトリに markdown file として入っているので外部データを fetch する必要はないが，読みこむ必要はある
  - markdown の最上部に title と date とかいうメタデータがあるのは，YAML Front Matter と呼ばれていて，`gray-matter`というライブラリでパースされる（当然インストールする必要あり）
  - lib というディレクトリをルート直下に作成し，file からデータをパースする utility function を作成する（title, data, file name を取得して投稿の URL として使用したり，index page に日付順にソートしたデータをリストアップするなど）
  - この utility file を置くディレクトリ名は割り当てられていないが，慣習的には，`lib`とか`utils`を使う
- データがパースされたら，次は`getStaticProps`で取ってくる作業
- 外部とデータをやり取りする際は，`getSortedPropsData()`みたいな utility function 内で自由にやり取り可能で，その中で`await fetch`を使ったり，`databaseClient.query('SELECT posts ...')`みたいなことも可能（**というのも getStaticProps はサーバ側でのみ動き，browser 側にバンドルされるデータに含まれないので，クライアント側に送信されない**）
- まとめると，開発時は`getStaticProps`はリクエスト毎，プロダクション時は，build time に動く．`getStaticPaths`の fallback key を使うことでさらに動作を良くすることができる（もちろん build time なので，HTTP headers や query parameters などのリクエスト時に利用可能なデータは扱えない）
- **`getStaticProps`はページから export されるので，ページファイルでなければ export できない**

## SSR
- **もしリクエスト時にデータを fetch したければ**，SSR を使う必要がある．具体的には，`getStaticProps`の代わりに`getServerSideProps`を export する必要がある
- 今回のブログにはいらないので使わないが，実際は，`getServerProps(context)`という形で用い，context にリクエスト時のパラメータを含む．このため，リクエストごとにサーバで計算を行う必要があり，外部設定なしでは CDN にキャッシュできないので遅い（**なので，リクエスト時にデータを fetch する必要があるときにのみ用いる**）

## CSR
- データを事前レンダリングする必要がないときは，SG w/o Data + CSR を取ればいい
- user のダッシュボードページなどの**ユーザ特化のページ生成で役に立つ**（というのは，ダッシュボードはプライベートでSEOには関係がなく，ページの事前レンダリングも必要ないため）
- Next.js はデータ fetch 用の SWR という React hook を作成しており，CSR ではこれを使うことを超推奨している（cash, revalidation, tracking, インターバルをおいて再 fetchなどをサポートしているため）


## Dynamic Routes
- ブログデータを埋め込む index page を作成したが，肝心のブログが未作成（ブログページごとの URL が欲しい）
- 前回は，page content が欲しかったので`getStaticProps`を用いてデータを fetch したが，今回は page path が欲しい
- 外部データに依存するページのパスを静的に生成できる Next の機能として dynamic URLs が利用できる
- 今回生成する静的なパスは`/posts/ssg-ssr`,`/posts/pre-rendering`とする
- 具体的には．`[id].js`というページを`page/posts`直下に作成する（`[]`があると dynamic routes になる）
- **他のページとほとんど同じように書けばいいが，`export async function getStaticPaths()`を書く必要がある点が異なる（この関数では id の可能性がある値をリストで返す）**
- また，その後に`getStaticProps({params})`を実行する必要があり，この`params`は id を含む
- `/lib/posts.js`に posts 直下にある`.md`を除いた名前（id）を取ってくるような utility 関数を追加する（**ここで注意すべきは，絶対に`[{params: {id: 'ssg-ssr'}}, {}]`みたいな object の配列を return すること**）この形式でないと fail する
- `remark remark-html` install することで，markdown を使用できる
- markdown を html に変えるために，`await remark().use(html).process(matterResult.content)`で取ってくる（同期的にデータを取得するために await を使用しており，これを使う getPostDataも await にする必要がでてくる）

### Dynamic Routes Details
- 外部からデータを fetch する際に，今回は `getStaticProps`，`getStaticPaths`を使用したが，`getAllPostIds`でも取ってこれる
- `getStaticPathsは，開発時はすべてのリクエストに対して動作するが，プロダクション環境では，build time に動く
- `getStaticPaths`内で`fallback: false`のとき，どのパスも返されなかったとき404ページが返される
  - **`true`なら，build time に`getStaticPaths`から返されるパスは HTML にレンダリングされる一方，返されなかった場合は，そのパスに対する最初のリクエストページの "fallback" version が返る（404は表示しない）**（バックグラウンドでは，リクエストされたパスに対して静的にページを生成し，build 時にレンダリングされた他のページと同様にリクエスト時にそのまま返す）
  - `blocking`なら，新たな path が`getStaticProps`によるサーバーサイドレンダリングがおき，将来起きうるリクエストのためにキャッシュされる（つまり1度だけしかレンダリングされない）
- 全部のパスを網羅したければ，`...`をブラケット内に加えればいい（例えば，`pages/posts/[...id].js`とすれば，`/posts/a`, `/posts/a/b`なども抑えられ，`params.id`は`['a', 'b', 'c']`みたいになる）
- router にアクセスしたければ，`next/router`から`useRouter` hook を import すればいい
- 404ページをカスタムするには，`pages/404.js`を作ればいい（これも build 時に生成される）


## API Routes
- 今回のブログには必要ないが，Node.js のサーバーレス関数として API endpoint を作成することが可能となる機能
- `pages/api`ディレクトリを作成し，次のようなフォーマットにしたがって関数を作成することで，`Lambdas`のようなサーバーレス関数がデプロイ可能（`export default function handler(req, res){}`，ここで`req`は HTTP request, `res`は HTTP server reponse）
- 例えば，`http://localhost:3000/api/hello`にアクセスすると，"`{"text": "Hello"}`が見える

### Details
- `getStaticProps`, `getStaticPaths`から fetch しないこと．直接サーバサイドのコードを書くか，ヘルパ関数をよぶこと．（なぜなら，`getStaticProps`, `getStaticPaths`もサーバ側で動き，クライアント側では動かない（というかクライアント側に送られない）ため）
- API routes の使用例として，form の入力を扱うケースがある（form 用のページを作成し，API Route で POST request が送られたとすると，直接それを database に保存するコードを書くことが可能．というのも上述したように client bundle に含まれないため安全だから）
- SG は headless CMS からデータを fetch するときに便利だが，現段階では draft で，ページで preview したいときには適さない（このようなケースでは build time でなく，request ごとに rendering してほしいはず）
- API Routes を使用する Preview Mode を Next は提供している

## Deploying Your Next App
- ここでは，Next.js によって作成されたプラットフォームである Vercel にデプロイする方法を学ぶ