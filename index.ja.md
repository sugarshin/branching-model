---
layout: default
permalink: /
---

# Semantic branching model

## Version: 1.0.0-alpha-1

<img src="img/branching-model.svg" alt="Branching model" width="110%">

## License

Made available under the
[Creative Commons Attribution-ShareAlike license](https://creativecommons.org/licenses/by-sa/4.0/).

## Authors

- [Roberto Di Remigio](https://totaltrash.xyz)
- [Radovan Bast](http://bast.fr)

## セマンティックブランチモデルを使用するプロジェクトの前提条件

* [semantic versioning](http://semver.org) を使用します
* fork-and-pull-request (or fork-and-merge-request) ワークフローに厳密に従います

## Goals

- メジャー、マイナー、またはパッチリリースのために個別に開発ラインがある
- 各ブランチの意味と効果をコントリビューターに伝える
- Communicate to maintainers the release visibility intent for each patch
- その修正が次のメジャーバージョン、マイナーバージョン、またはパッチバージョンに影響を与えるかどうかを、コントリビューターとメンテナーの両方に対して明確かつ簡単にする

## Why not just using "[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)"?

- [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/) モデルの定義では、 `master` ブランチのすべてのコミットは新しいリリースですが、過去のリリースにパッチをあてると、 `master` ブランチにないリリースコミットが発生します
- ターゲットのメジャーバージョンまたはマイナーバージョンに基づいてプルリクエストを区別するためのプロトコルを提供していません。したがって、メンテナーにとって、 API を破壊する PR を受け入れた後、 API を維持する PR を受け入れることは難しい場合があります。コントリビューターにとって、 release visibility intent を伝えるのは難しいかもしれません

## Branch semantics

1. `master` は次の **メジャー** リリースに向けた変更を収集します
3. `release/X.Y` ブランチは、次の **マイナー** リリースに向けた変更を収集します
3. `release/X.Y.Z` ブランチは、次の **パッチ** リリースに向けた変更を収集します
4. 新しい feature は、 `master` または `release/X.Y` ブランチに向けられます
5. パッチリリースブランチ `release/X.Y.Z` は新しい機能を受け取ることはなく、バグ修正のみを受け取ります

## Lifetime of branches

- 新しい `release` ブランチが作成されると、以前のバージョンのサポートが削除される可能性があります
- 以前のバージョンのバグ修正は、適切な PR が対処した場合にのみリリースされます

## Release preparation

リリースプロセスは、feature フリーズ、リリースキャンディデイトの収束、リリースのタグ付けのステップで構成されます

### Feature freeze and branch creation

feature フリーズは、新しいリリースブランチが作成される瞬間です。 `master` から `release/X.Y` が作成されるか、 `release/X.Y` から `release/X.Y.Z` が作成されたときです。リリースブランチの作成は、ルートブランチが次のメジャーリリースまたはマイナーリリースに向けてチェンジセットを受け取るまで、遅れる場合があります。

### Converging a release candidate

多くの場合、 feature フリーズからリリースコミットに至るまでにいくつかの作業を行う必要があります。この作業は通常、バグ修正、cosmetics、ドキュメント、およびテストで構成されます。

### Tagging a release

特定のコミットにリリースバージョンのタグを付けると、実際のリリースが作成されます。タグの作成者が含まれているため、注釈付きタグを使用することをお勧めします。

## Bugfixes

- バグフィックスは、意図に応じて、 `release/X.Y.Z` 、 `release/X.Y` 、または `master` に向けることができます
- `release/X.Y.Z` に向けられたバグフィックスは、新しいタグを作成することによって通知されるパッチバージョンをバンプする必要があります
- 特定の `release/X.Y.Z` の重要なバグフィックスは、必要に応じて、 _merging_ によって `release/X.Y` に移植され、さらに `master` に移植されます
- `master` の重要なバグ修正は、必要に応じて、_cherry picking_ によって `release/X.Y` および `release/X.Y.Z` に移植できます

## Source branch

PR のために新しいブランチを作成するときは、常に変更の対象となるブランチから開始する必要があります。新しいブランチの命名は、 feature/issue ブランチの命名規則に従う必要があります。

i.e. the feature branch in the fork should _not_ be named identical to the target branch in the upstream repository (see [Do Not Issue Pull Requests From Your Master Branch](https://blog.jasonmeridth.com/posts/do-not-issue-pull-requests-from-your-master-branch/)).

Explanation:

- 統合する前に、無関係なコミットを PR の上にスタックしないでください (see also [this blogpost](http://blog.jasonmeridth.com/posts/do-not-issue-pull-requests-from-your-master-branch/))
- The submitted changes may get rebased and/or squashed by the maintainer and this could create divergence and merge commits on the source branch which could complicate future pull requests.
- サブミットされた変更は、メンテナーによって rebase および/また は squash される可能性があり、これにより、ソースブランチに相違が生じてコミットがマージされ、将来の PR が複雑になる可能性があります

Example:

- Issue 137 を修正する `release/1.0.Z` に向けて PR を作成するには、リポジトリを fork し、 `release/1.0.Z` ブランチから `release/1.0.Z-issue-137` ブランチを作成し、 upstream の `release/1.0.Z` ブランチに向けて PR を作成します。 `release/1.0.Z-issue-137` ブランチは、 PR の approve 後に削除できます

## Target branch

- **Bugfix PRs** は、関連する `release/X.Y.Z` アップストリームブランチを対象としています
- **API を保持する機能のPR** は、 `master` （次のメジャーリリースが予定されている場合）または対応するリリースブランチ（その後、次のマイナーリリースが予定されている場合）にむけてから、マスターに port できます
- **API を破壊する機能のPR** は、 `master` アップストリームブランチに向けられます

## Frequently-asked questions

### リリースブランチは必要ですか？ master のリリースにタグを付けることはできませんか？

リリースされたバージョンにパッチを適用したくない場合は、 `master` でバージョンタグを付けるだけで十分です。プロジェクトがリリースバージョンへのパッチを受け取ったら、ブランチを作成する必要があります。

### semver を使用していませんが、このモデルは意味がありますか？

このモデルはおそらく意味がありますが、おそらく2つのレベルのリリースブランチは必要ありませんが、各リリースをサポートするために必要なレベルは1つだけです。

### fork-and-pull-request ワークフローを使用していませんが、このモデルは意味がありますか？

このモデルは、単一のリポジトリ内でも使用できます。 この write-protect な `master` とリリースブランチについては、 feature または bugfix ブランチから `master` またはリリースブランチへのマージを受け入れる前にコードレビューを使用します。

### 多くのブランチが含まれることはありませんか？

Possibly - but branches are cheap. If you do not like to have a branch for each single release, you can delay the creation of a corresponding release branch until the moment when a past release is about to receive a patch which you would like to isolate from other changes which have the past release as parent.
