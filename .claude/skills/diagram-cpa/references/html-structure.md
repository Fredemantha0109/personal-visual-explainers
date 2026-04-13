# HTML構造ガイド

## 基本テンプレート

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="robots" content="noindex, nofollow">
  <title>【タイトル】 | 知識ストック</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700;900&display=swap" rel="stylesheet">
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            cpa: {
              bg: '#F8F9FB',
              surface: '#FFFFFF',
              border: '#E5E7EB',
              text: '#111827',
              muted: '#6B7280',
              dim: '#9CA3AF',
            }
          },
          fontFamily: {
            sans: ['"Noto Sans JP"', '"Hiragino Sans"', 'sans-serif'],
          }
        }
      }
    }
  </script>
  <style>
    @media print {
      .no-print { display: none !important; }
      body { background: white !important; }
      .rounded-xl { break-inside: avoid; }
    }
  </style>
</head>
<body class="bg-cpa-bg text-cpa-text antialiased leading-relaxed">

  <!-- ① ドメインバー（グラデーション帯）: ドメインに応じて色を変える → 下のドメインカラー表参照 -->
  <div class="h-1.5 bg-gradient-to-r from-blue-600 via-blue-500 to-indigo-400"></div>

  <!-- ② ヘッダーエリア（ドメインカラー薄背景）: ① ヘッダー セクションのコンテンツをここに入れる -->
  <header class="bg-gradient-to-b from-blue-50/60 to-transparent border-b border-blue-100/70">
    <div class="max-w-3xl mx-auto px-5 py-8">
      <!-- ① ヘッダーのコンテンツ（ドメインバッジ・タイトル・作成日・鮮度バッジ）をここに -->
    </div>
  </header>

  <!-- ③ 本文エリア -->
  <main class="max-w-3xl mx-auto px-5 py-8 pb-16">
    <!-- 核心ひとこと以降のコンテンツ -->
  </main>

  <!-- 鮮度インジケーター JS（html-structure.md の「鮮度インジケーター」参照） -->
  <script>...</script>
  <script src="https://unpkg.com/lucide@latest"></script>
  <script>lucide.createIcons();</script>
</body>
</html>
```

---

## ドメインカラーの使い分け

ドメインカラーは以下の4箇所で一貫して使う:
1. **ドメインバー**（グラデーション帯）
2. **ヘッダー背景**（薄グラデーション）
3. **ドメインバッジ**（小ラベル）
4. **押さえる3点の番号バブル**

| ドメイン | ①ドメインバー | ②ヘッダー背景 | ③バッジ | ④番号バブル |
|---------|-------------|-------------|--------|-----------|
| AI・テック | `bg-gradient-to-r from-blue-600 via-blue-500 to-indigo-400` | `from-blue-50/60` / `border-blue-100/70` | `text-blue-700 bg-blue-100` | `bg-blue-600` |
| 会計・監査 | `bg-gradient-to-r from-emerald-600 via-emerald-500 to-teal-400` | `from-emerald-50/60` / `border-emerald-100/70` | `text-emerald-700 bg-emerald-100` | `bg-emerald-600` |
| 法律・規制 | `bg-gradient-to-r from-violet-600 via-violet-500 to-purple-400` | `from-violet-50/60` / `border-violet-100/70` | `text-violet-700 bg-violet-100` | `bg-violet-600` |
| 経営・マネジメント | `bg-gradient-to-r from-amber-600 via-amber-500 to-orange-400` | `from-amber-50/60` / `border-amber-100/70` | `text-amber-700 bg-amber-100` | `bg-amber-600` |
| 複合 | `bg-gradient-to-r from-slate-700 via-slate-600 to-slate-500` | `from-slate-50/60` / `border-slate-100/70` | `text-slate-700 bg-slate-100` | `bg-slate-600` |

また、**核心ひとことのグラデーション背景**にもドメインカラーを使う（② 核心ひとこと セクション参照）。

---

## メタデータコメント

各図解HTMLの **`<body>` タグ直後** に必ず挿入する。インデックスページ生成時の読み取り元。

```html
<!-- DIAGRAM-META
title: J-SOXとは
domain: 法律・規制
tags: 内部統制,COSO,ITGCとITAC,会社法監査,金商法監査
created: 2026-03-10
reviews: 0
-->
```

| フィールド | 値の例 | ルール |
|-----------|--------|--------|
| `title` | J-SOXとは | HTMLの `<h1>` と同じ文言 |
| `domain` | AI・テック ／ 会計・監査 ／ 法律・規制 ／ 経営・マネジメント | 4択から1つ選ぶ |
| `tags` | 内部統制,COSO,J-SOX | カンマ区切り・スペースなし。図解の「関連キーワード」から転記 |
| `created` | 2026-03-10 | ISO 8601 形式 |
| `last-reviewed` | 2026-03-10 | ISO 8601 形式。`created` と同じ日付で初期化。「また調べた」のたびに当日の日付に更新する |
| `reviews` | 0 / 1 / 2 … | 「また調べた」が発動するたびに +1 する整数 |

---

## 鮮度インジケーター

「最終確認からの経過日数」を、ページを開くたびに動的に計算して表示するバッジ。

### バッジ要素（ヘッダー内のドメインバッジの隣に挿入）

```html
<!-- 鮮度インジケーター -->
<span
  id="freshness-badge"
  data-last-reviewed="【YYYY-MM-DD】"
  class="inline-flex items-center gap-1 text-xs font-medium px-2.5 py-1 rounded-full border"
></span>
```

`data-last-reviewed` の値 = 作成時は `created` と同じ日付。「また調べた」が発動するたびに当日の日付に更新する。

### JS スニペット（`lucide.createIcons()` の直前に挿入）

```html
<script>
(function () {
  var badge = document.getElementById('freshness-badge');
  if (!badge) return;
  var last = new Date(badge.dataset.lastReviewed);
  var days = Math.floor((new Date() - last) / 86400000);
  var label, cls;
  if (days <= 30) {
    label = '確認済 · ' + days + '日前';
    cls = 'text-emerald-700 bg-emerald-50 border-emerald-200';
  } else if (days <= 90) {
    label = 'そろそろ確認 · ' + days + '日前';
    cls = 'text-amber-700 bg-amber-50 border-amber-200';
  } else {
    label = '要復習 · ' + days + '日前';
    cls = 'text-red-700 bg-red-50 border-red-200';
  }
  badge.className = badge.className.replace('border', '').trim() + ' ' + cls;
  badge.innerHTML =
    '<svg class="w-3 h-3" data-lucide="clock"></svg>' + label;
  if (window.lucide) window.lucide.createIcons({ nodes: [badge] });
})();
</script>
```

### 表示仕様

| 経過日数 | ラベル | 色 |
|---------|--------|----|
| 0〜30日 | 確認済 · N日前 | 緑 |
| 31〜90日 | そろそろ確認 · N日前 | 黄 |
| 91日以上 | 要復習 · N日前 | 赤 |

### 配置ルール

ヘッダーのドメインバッジ行に並べる。以下はドメインバッジとの共存イメージ:

```html
<!-- ヘッダー内 -->
<div class="flex flex-wrap items-center justify-center gap-2 mb-6">
  <!-- ドメインバッジ（既存） -->
  <div class="inline-flex items-center gap-2 bg-blue-500/10 text-blue-700 px-4 py-1.5 rounded-full text-sm font-medium">
    <i data-lucide="cpu" class="w-4 h-4"></i>
    AI・テック
  </div>
  <!-- 鮮度バッジ（新規追加） -->
  <span
    id="freshness-badge"
    data-last-reviewed="2026-03-10"
    class="inline-flex items-center gap-1 text-xs font-medium px-2.5 py-1 rounded-full border"
  ></span>
</div>
```

---

## 再確認履歴バッジ

ヘッダーブロック（タイトル・作成日）の**直後**に配置する。`<!-- 再確認履歴 -->` コメントを目印にして挿入・更新する。

### 初回挿入（再確認1回目）

```html
<!-- 再確認履歴 -->
<div class="mb-8 bg-amber-50 border border-amber-200 rounded-xl p-4">
  <div class="flex items-center gap-2 mb-3">
    <i data-lucide="refresh-cw" class="w-4 h-4 text-amber-600"></i>
    <span class="text-sm font-bold text-amber-700">再確認履歴</span>
    <span class="ml-auto text-xs font-bold text-amber-600 bg-amber-100 px-2 py-0.5 rounded-full">通算 1 回</span>
  </div>
  <ul class="space-y-1.5 text-xs text-amber-800">
    <li class="flex items-start gap-2">
      <span class="text-amber-400 flex-shrink-0 mt-0.5">▸</span>
      <span><span class="font-medium">YYYY-MM-DD</span> — 【思い出せなかった箇所。なければ「記録なし」】</span>
    </li>
  </ul>
</div>
<!-- /再確認履歴 -->
```

### 2回目以降（履歴に1行追加＋カウント更新）

`通算 N 回` の数字を更新し、`<ul>` に `<li>` を1行追記する。

```html
<!-- 再確認履歴 -->
<div class="mb-8 bg-amber-50 border border-amber-200 rounded-xl p-4">
  <div class="flex items-center gap-2 mb-3">
    <i data-lucide="refresh-cw" class="w-4 h-4 text-amber-600"></i>
    <span class="text-sm font-bold text-amber-700">再確認履歴</span>
    <span class="ml-auto text-xs font-bold text-amber-600 bg-amber-100 px-2 py-0.5 rounded-full">通算 2 回</span>
  </div>
  <ul class="space-y-1.5 text-xs text-amber-800">
    <li class="flex items-start gap-2">
      <span class="text-amber-400 flex-shrink-0 mt-0.5">▸</span>
      <span><span class="font-medium">YYYY-MM-DD（1回目）</span> — 【記録内容】</span>
    </li>
    <li class="flex items-start gap-2">
      <span class="text-amber-400 flex-shrink-0 mt-0.5">▸</span>
      <span><span class="font-medium">YYYY-MM-DD（2回目）</span> — 【記録内容】</span>
    </li>
  </ul>
</div>
<!-- /再確認履歴 -->
```

### 配置ルール

- **位置**: ヘッダー（タイトル・ドメインバッジ・作成日）の直後、「核心ひとこと」の直前
- **初回は非表示**: 図解を最初に作成した時点ではこのブロックを入れない。「また調べた」が発動したとき初めて挿入する
- **既存バッジの更新**: `<!-- 再確認履歴 -->` から `<!-- /再確認履歴 -->` までを丸ごと置き換えて更新する

---

## セクション構造

### ① ヘッダー（タイトルエリア）

```html
<div class="mb-10">
  <!-- ドメインバッジ -->
  <div class="inline-flex items-center gap-2 bg-blue-50 text-blue-700 px-3 py-1 rounded-full text-xs font-medium mb-4">
    <i data-lucide="cpu" class="w-3.5 h-3.5"></i>
    AI・テック
  </div>
  <!-- タイトル -->
  <h1 class="text-3xl font-black text-cpa-text mb-2">【タイトル】</h1>
  <!-- 更新日 -->
  <p class="text-xs text-cpa-dim">作成: 2026-03-10</p>
</div>
```

### ② 核心ひとこと

ドメインカラーのグラデーション背景でページの「顔」として際立たせる。装飾用の大きな引用符を背景に配置する。

```html
<div class="relative bg-gradient-to-br from-blue-50 to-indigo-50/60
            border border-blue-200 rounded-2xl px-8 py-7 mb-8 overflow-hidden">
  <!-- 装飾引用符（背景） -->
  <div class="absolute -top-3 -left-1 text-9xl font-black text-blue-200/50
              leading-none select-none pointer-events-none" aria-hidden="true">"</div>
  <!-- 役割バッジ -->
  <div class="relative flex items-center gap-2.5 mb-4">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-blue-700 bg-blue-100 px-2.5 py-1 rounded-full">
      <i data-lucide="zap" class="w-3 h-3"></i>
      核心ひとこと
    </span>
    <div class="flex-1 h-px bg-blue-200/60"></div>
  </div>
  <!-- 本文: ドメインに応じて text-blue-900 / text-emerald-900 / text-violet-900 / text-amber-900 -->
  <p class="relative text-xl md:text-2xl font-black text-blue-900 leading-snug">
    【1〜2文の本質的な定義をここに書く】
  </p>
</div>
```

ドメインごとの色クラスの対応:

| ドメイン | 外枠 `border` | 背景 `from-` | 引用符 | バッジ | 本文色 |
|---------|-------------|------------|-------|-------|-------|
| AI・テック | `border-blue-200` | `from-blue-50 to-indigo-50/60` | `text-blue-200/50` | `text-blue-700 bg-blue-100` | `text-blue-900` |
| 会計・監査 | `border-emerald-200` | `from-emerald-50 to-teal-50/60` | `text-emerald-200/50` | `text-emerald-700 bg-emerald-100` | `text-emerald-900` |
| 法律・規制 | `border-violet-200` | `from-violet-50 to-purple-50/60` | `text-violet-200/50` | `text-violet-700 bg-violet-100` | `text-violet-900` |
| 経営・マネジメント | `border-amber-200` | `from-amber-50 to-orange-50/60` | `text-amber-200/50` | `text-amber-700 bg-amber-100` | `text-amber-900` |

### ③ 全体構造図

フロー・階層・比較のいずれかを使う（`references/exemplar.md` 参照）。

```html
<div class="bg-cpa-surface border border-cpa-border rounded-xl p-6 mb-8">
  <!-- 役割バッジ型ヘッダー -->
  <div class="flex items-center gap-2.5 mb-5">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-slate-600 bg-slate-100 px-2.5 py-1 rounded-full">
      <i data-lucide="layout" class="w-3 h-3"></i>
      全体構造
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>
  <!-- フロー図 or 階層図 or 比較表をここに -->
</div>
```

### ④ 押さえる3点

縦ラインで3点を繋ぐタイムライン型。番号バブルにドメインカラーを使う。

```html
<div class="mb-8">
  <!-- 役割バッジ型ヘッダー -->
  <div class="flex items-center gap-2.5 mb-6">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-blue-700 bg-blue-100 px-2.5 py-1 rounded-full">
      <i data-lucide="list-checks" class="w-3 h-3"></i>
      押さえる3点
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>

  <!-- タイムライン型レイアウト -->
  <div class="relative pl-10">
    <!-- 縦ライン（番号バブルの中心を通る） -->
    <div class="absolute left-3.5 top-4 bottom-4 w-px bg-blue-200"></div>

    <div class="space-y-5">
      <!-- ポイント1: border-l-4 で左アクセント（最重要=濃い・2番=中・3番=薄い） -->
      <div class="relative">
        <div class="absolute -left-10 w-7 h-7 rounded-full bg-blue-600 text-white
                    text-xs font-black flex items-center justify-center shadow-sm">1</div>
        <div class="bg-cpa-surface border border-cpa-border border-l-4 border-l-blue-500 rounded-xl p-4">
          <!-- タイトルはドメインカラー(blue-700)で色付け: 縦スキャン時の視認性UP -->
          <div class="font-black text-blue-700 mb-1.5">【ポイントのタイトル】</div>
          <p class="text-sm text-cpa-muted leading-relaxed">【説明】</p>
        </div>
      </div>

      <!-- ポイント2 -->
      <div class="relative">
        <div class="absolute -left-10 w-7 h-7 rounded-full bg-blue-600 text-white
                    text-xs font-black flex items-center justify-center shadow-sm">2</div>
        <div class="bg-cpa-surface border border-cpa-border border-l-4 border-l-blue-300 rounded-xl p-4">
          <div class="font-black text-blue-700 mb-1.5">【ポイントのタイトル】</div>
          <p class="text-sm text-cpa-muted leading-relaxed">【説明】</p>
        </div>
      </div>

      <!-- ポイント3 -->
      <div class="relative">
        <div class="absolute -left-10 w-7 h-7 rounded-full bg-blue-600 text-white
                    text-xs font-black flex items-center justify-center shadow-sm">3</div>
        <div class="bg-cpa-surface border border-cpa-border border-l-4 border-l-blue-200 rounded-xl p-4">
          <div class="font-black text-blue-700 mb-1.5">【ポイントのタイトル】</div>
          <p class="text-sm text-cpa-muted leading-relaxed">【説明】</p>
        </div>
      </div>
    </div>
  </div>
</div>
```

番号バブルの色（`bg-blue-600`）はドメインカラーに合わせて変える（ドメインカラー表参照）。

**左ボーダーアクセント**: 番号ごとに濃度を段階変化させて重要度の序列を示す。ドメイン別の対応は以下の通り。

| 番号 | AI・テック | 会計・監査 | 法律・規制 | 経営・マネジメント |
|-----|-----------|-----------|-----------|---------|
| 1番（最重要） | `border-l-blue-500` | `border-l-emerald-500` | `border-l-violet-500` | `border-l-amber-500` |
| 2番 | `border-l-blue-300` | `border-l-emerald-300` | `border-l-violet-300` | `border-l-amber-300` |
| 3番 | `border-l-blue-200` | `border-l-emerald-200` | `border-l-violet-200` | `border-l-amber-200` |

**タイトル文の色**: `font-black text-blue-700`（AI・テック）固定。他ドメインも同様にドメインカラーの `700` トーンを使う。

| ドメイン | タイトル色 |
|---------|---------|
| AI・テック | `text-blue-700` |
| 会計・監査 | `text-emerald-700` |
| 法律・規制 | `text-violet-700` |
| 経営・マネジメント | `text-amber-700` |

### ⑤ 実務場面

背景色は `bg-emerald-50/40 border-emerald-200`（薄緑）固定。スクロール時に「仕事文脈のエリア」と即座に識別できる。

```html
<div class="bg-emerald-50/40 border border-emerald-200 rounded-xl p-6 mb-8">
  <!-- 役割バッジ型ヘッダー -->
  <div class="flex items-center gap-2.5 mb-5">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-emerald-700 bg-emerald-100 px-2.5 py-1 rounded-full">
      <i data-lucide="briefcase" class="w-3 h-3"></i>
      実務場面
    </span>
    <div class="flex-1 h-px bg-emerald-200/60"></div>
  </div>
  <div class="space-y-3">
    <div class="flex items-start gap-3">
      <div class="w-5 h-5 rounded bg-emerald-100 flex items-center justify-center flex-shrink-0 mt-0.5">
        <i data-lucide="check" class="w-3 h-3 text-emerald-600"></i>
      </div>
      <div>
        <span class="text-xs font-medium text-emerald-700 bg-emerald-50 px-2 py-0.5 rounded mr-2">上場準備</span>
        <span class="text-sm text-cpa-text">【この文脈での登場場面】</span>
      </div>
    </div>
    <!-- 他の実務場面も同様に -->
  </div>
</div>
```

実務場面タグのカラー対応:

| タグ | クラス |
|-----|--------|
| 上場準備 | `text-violet-700 bg-violet-50` |
| 監査法人対応 | `text-blue-700 bg-blue-50` |
| 内部統制 | `text-amber-700 bg-amber-50` |
| PMI / 組織再編 | `text-rose-700 bg-rose-50` |
| 会計アドバイザー | `text-emerald-700 bg-emerald-50` |
| 管理業務改善 | `text-slate-700 bg-slate-100` |

### ⑥ 混乱しやすいポイント

背景色は `bg-amber-50/40 border-amber-200`（薄アンバー）固定。スクロール時に「要注意エリア」と即座に識別できる。

```html
<div class="bg-amber-50/40 border border-amber-200 rounded-xl p-6 mb-8">
  <!-- 役割バッジ型ヘッダー（注意系なので amber 固定） -->
  <div class="flex items-center gap-2.5 mb-5">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-amber-700 bg-amber-100 px-2.5 py-1 rounded-full">
      <i data-lucide="alert-triangle" class="w-3 h-3"></i>
      混乱しやすいポイント
    </span>
    <div class="flex-1 h-px bg-amber-200/60"></div>
  </div>
  <div class="space-y-4">
    <!-- 混乱ポイント1 -->
    <div class="border border-amber-200 bg-amber-50/50 rounded-xl p-4">
      <div class="flex items-center gap-2 mb-2">
        <i data-lucide="help-circle" class="w-4 h-4 text-amber-600"></i>
        <span class="text-sm font-bold text-amber-800">【混乱しがちな点】</span>
      </div>
      <div class="grid grid-cols-2 gap-3 text-sm">
        <div class="bg-white rounded-lg p-3 border border-amber-200">
          <div class="text-xs text-red-500 font-medium mb-1">× よくある誤解</div>
          <p class="text-cpa-text">【誤解の内容】</p>
        </div>
        <div class="bg-white rounded-lg p-3 border border-emerald-200">
          <div class="text-xs text-emerald-600 font-medium mb-1">○ 正しい理解</div>
          <p class="text-cpa-text">【正しい内容】</p>
        </div>
      </div>
    </div>
  </div>
</div>
```

### ⑦ 関連キーワード

キーワードは2種類のバッジで出し分ける:
- **既存図解あり** → リンク付きバッジ（青枠・hover効果あり）
- **未図解** → 通常バッジ（グレー・テキストのみ）

```html
<div class="mb-8">
  <!-- 役割バッジ型ヘッダー -->
  <div class="flex items-center gap-2.5 mb-4">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-slate-600 bg-slate-100 px-2.5 py-1 rounded-full">
      <i data-lucide="link" class="w-3 h-3"></i>
      関連キーワード
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>
  <div class="flex flex-wrap gap-2">
    <!-- 既存図解あり: リンク付きバッジ -->
    <a href="./【スラッグ】.html"
       class="inline-flex items-center gap-1.5 bg-cpa-surface border border-blue-200
              text-blue-600 text-xs px-3 py-1.5 rounded-full
              hover:bg-blue-50 hover:border-blue-400 transition-colors">
      <i data-lucide="file-text" class="w-3 h-3"></i>
      【キーワード（既存図解あり）】
    </a>
    <!-- 未図解: 通常バッジ -->
    <span class="bg-cpa-surface border border-cpa-border text-cpa-muted text-xs px-3 py-1.5 rounded-full">
      【キーワード（未図解）】
    </span>
  </div>
</div>
```

リンク付きバッジのドメイン別カラー対応:

| 図解先のドメイン | border | text | hover bg |
|--------------|--------|------|----------|
| AI・テック | `border-blue-200` | `text-blue-600` | `hover:bg-blue-50 hover:border-blue-400` |
| 会計・監査 | `border-emerald-200` | `text-emerald-600` | `hover:bg-emerald-50 hover:border-emerald-400` |
| 法律・規制 | `border-violet-200` | `text-violet-600` | `hover:bg-violet-50 hover:border-violet-400` |
| 経営・マネジメント | `border-amber-200` | `text-amber-600` | `hover:bg-amber-50 hover:border-amber-400` |

---

### ⑧ フッター（インデックスへ戻るリンク）

関連キーワードの直後、`</main>` の前に必ず配置する。全図解の標準フッター。

```html
<!-- フッター: インデックスへ戻る -->
<div class="border-t border-cpa-border pt-6">
  <a href="./index.html"
     class="inline-flex items-center gap-2 text-sm text-cpa-muted hover:text-cpa-text transition-colors group">
    <i data-lucide="arrow-left" class="w-4 h-4 group-hover:-translate-x-0.5 transition-transform"></i>
    知識ストック一覧に戻る
  </a>
</div>
```

**配置ルール**:
- `mb-6`（`mb-8` から変更）の関連キーワードブロックの直後に置く
- `<main>` の閉じタグの直前が正しい位置

---

## 全体構造図ステップカードの説明テキスト視認性ルール

フロー図の各ステップカード内の「一言説明」テキストは `text-[11px]` + 各カラーの `600` トーンで統一する。`400` や `500` は背景色との対比が不十分で読めなくなるため使わない。

| ステップ背景 | 正しい説明テキスト色 | NG（薄すぎる） |
|-----------|-----------------|------------|
| `bg-slate-50` | `text-slate-600` | `text-slate-400` |
| `bg-blue-50` | `text-blue-600` | `text-blue-400` / `text-blue-500` |
| `bg-indigo-50` | `text-indigo-600` | `text-indigo-400` |
| `bg-sky-50` | `text-sky-600` | `text-sky-400` |
| `bg-emerald-50` | `text-emerald-600` | `text-emerald-400` |
| `bg-violet-50` | `text-violet-600` | `text-violet-400` |
| `bg-amber-50` | `text-amber-600` | `text-amber-400` |

---

## Lucide Icons よく使うアイコン

| 用途 | アイコン名 |
|-----|----------|
| AI・テック | `cpu`, `code`, `database`, `server`, `bot` |
| 会計・数値 | `calculator`, `bar-chart-2`, `trending-up`, `coins` |
| 法律・規制 | `shield`, `scale`, `gavel`, `file-check` |
| 経営・組織 | `briefcase`, `building-2`, `users`, `network` |
| プロセス | `arrow-right`, `arrow-down`, `chevron-right`, `git-branch` |
| 注意・チェック | `alert-triangle`, `check-circle`, `x-circle`, `info` |
| 構造 | `layout`, `layers`, `list-checks`, `link` |
| 時間・履歴 | `clock`, `calendar`, `history` |
