# 模範解答パターン

コピペして使えるHTMLブロック集。

---

## 鮮度インジケーター（実装サンプル）

新規図解作成時のヘッダー完成形イメージ。ドメインバッジと鮮度バッジが横並びになる。

```html
<!-- ヘッダー内 — Step 3 で生成するHTML（抜粋） -->
<div class="text-center mb-8 md:mb-10">
  <div class="flex flex-wrap items-center justify-center gap-2 mb-6">
    <!-- ドメインバッジ -->
    <div class="inline-flex items-center gap-2 bg-violet-500/10 text-violet-700 px-4 py-1.5 rounded-full text-sm font-medium">
      <i data-lucide="scale" class="w-4 h-4"></i>
      法律・規制
    </div>
    <!-- 鮮度バッジ（JS が動的にスタイルと文字を書き込む） -->
    <span
      id="freshness-badge"
      data-last-reviewed="2026-03-10"
      class="inline-flex items-center gap-1 text-xs font-medium px-2.5 py-1 rounded-full border"
    ></span>
  </div>
  <h1 class="text-3xl md:text-4xl font-black text-gray-900 mb-4">J-SOXとは</h1>
  <!-- 以下 省略 -->
</div>

<!-- ... 本文 ... -->

  <!-- </body> 直前 -->
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
    badge.className = 'inline-flex items-center gap-1 text-xs font-medium px-2.5 py-1 rounded-full border ' + cls;
    badge.textContent = label;
  })();
  </script>
  <script src="https://unpkg.com/lucide@latest"></script>
  <script>lucide.createIcons();</script>
</body>
```

ブラウザで見たとき、鮮度バッジはこのように表示される:

| 経過日数 | 表示例 |
|---------|--------|
| 作成当日 | 🟢 `確認済 · 0日前` |
| 45日後 | 🟡 `そろそろ確認 · 45日前` |
| 120日後 | 🔴 `要復習 · 120日前` |

---

## 再確認履歴バッジ（「また調べた」ワークフロー用）

HTMLパターン・配置ルールは `html-structure.md` の「再確認履歴バッジ」セクション参照。

---

---

## インデックスページテンプレート

`output/index.html` の完成形。AIが各図解のメタデータを読み取ったあと、この構造に当てはめて生成する。

**重要**: 各カードの `<a>` 要素に `data-tags="【全タグのカンマ区切り】"` を必ず付与する。これによりタグフィルターが機能する。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="robots" content="noindex, nofollow">
  <title>知識ストック | インデックス</title>
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
              bg: '#F8F9FB', surface: '#FFFFFF', border: '#E5E7EB',
              text: '#111827', muted: '#6B7280', dim: '#9CA3AF',
            }
          },
          fontFamily: { sans: ['"Noto Sans JP"', '"Hiragino Sans"', 'sans-serif'] }
        }
      }
    }
  </script>
</head>
<body class="bg-cpa-bg text-cpa-text antialiased leading-relaxed">
  <div class="h-1.5 bg-gradient-to-r from-slate-700 via-slate-600 to-slate-500"></div>

  <main class="max-w-3xl mx-auto px-5 py-10">

    <!-- ヘッダー -->
    <div class="mb-8">
      <h1 class="text-2xl font-black text-cpa-text mb-1">知識ストック</h1>
      <!-- 統計バー: AIが実際の数値に置き換える -->
      <div class="flex items-center gap-4 text-xs text-cpa-dim">
        <span class="flex items-center gap-1">
          <i data-lucide="file-text" class="w-3.5 h-3.5"></i>
          全 【N】 件
        </span>
        <span class="flex items-center gap-1">
          <i data-lucide="refresh-cw" class="w-3.5 h-3.5"></i>
          再確認あり 【N】 件
        </span>
        <span class="flex items-center gap-1">
          <i data-lucide="calendar" class="w-3.5 h-3.5"></i>
          最終更新: 【YYYY-MM-DD】
        </span>
      </div>
    </div>

    <!-- タグフィルターバー -->
    <div class="mb-8">
      <div class="flex items-center gap-2 mb-3">
        <i data-lucide="tag" class="w-3.5 h-3.5 text-cpa-dim"></i>
        <span class="text-xs font-bold text-cpa-muted">タグで絞り込む</span>
        <span id="filter-count" class="text-[10px] text-cpa-dim"></span>
      </div>
      <div id="tag-filter-bar" class="flex flex-wrap gap-1.5">
        <!-- タグボタンはJSが自動生成 -->
      </div>
    </div>

    <!-- ドメイン別セクション（存在するドメインのみ出力） -->

    <!-- === AI・テック セクション === -->
    <section class="mb-10" data-domain-section>
      <div class="flex items-center gap-2 mb-4">
        <div class="w-1 h-5 rounded-full bg-blue-500"></div>
        <h2 class="text-sm font-bold text-blue-700">AI・テック</h2>
        <span class="text-xs text-cpa-dim ml-1" data-domain-count>【N】件</span>
      </div>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-3" data-card-grid>
        <!-- カード1枚の例 -->
        <!-- data-tags には DIAGRAM-META の tags 全てを入れる（表示は最大5個） -->
        <a href="./【スラッグ】.html"
           class="diagram-card block bg-cpa-surface border border-cpa-border rounded-xl p-4 hover:border-blue-300 hover:shadow-sm transition-all"
           data-tags="【タグ1,タグ2,タグ3… 全て】">
          <div class="flex items-start justify-between gap-2 mb-2">
            <span class="font-bold text-cpa-text text-sm leading-snug">【タイトル】</span>
            <!-- 再確認バッジ: reviews >= 1 のときだけ表示 -->
            <span class="flex-shrink-0 text-[10px] font-bold text-amber-600 bg-amber-50 border border-amber-200 px-1.5 py-0.5 rounded-full flex items-center gap-1">
              <i data-lucide="refresh-cw" class="w-2.5 h-2.5"></i>
              【N】回
            </span>
          </div>
          <span
            class="freshness-chip inline-flex items-center gap-1 text-[10px] font-medium px-2 py-0.5 rounded-full border mb-2"
            data-last-reviewed="【last-reviewed の値】"
          ></span>
          <div class="text-[11px] text-cpa-dim mb-2.5">作成: 【YYYY-MM-DD】</div>
          <!-- 表示タグ: 最大5個 -->
          <div class="flex flex-wrap gap-1">
            <span class="text-[10px] bg-slate-100 text-slate-500 px-2 py-0.5 rounded-full">【タグ1】</span>
            <span class="text-[10px] bg-slate-100 text-slate-500 px-2 py-0.5 rounded-full">【タグ2】</span>
          </div>
        </a>
      </div>
    </section>

    <!-- === 会計・監査 セクション === -->
    <section class="mb-10" data-domain-section>
      <div class="flex items-center gap-2 mb-4">
        <div class="w-1 h-5 rounded-full bg-emerald-500"></div>
        <h2 class="text-sm font-bold text-emerald-700">会計・監査</h2>
        <span class="text-xs text-cpa-dim ml-1" data-domain-count>【N】件</span>
      </div>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-3" data-card-grid>
        <a href="./【スラッグ】.html"
           class="diagram-card block bg-cpa-surface border border-cpa-border rounded-xl p-4 hover:border-emerald-300 hover:shadow-sm transition-all"
           data-tags="【タグ1,タグ2…】">
          <div class="flex items-start justify-between gap-2 mb-2">
            <span class="font-bold text-cpa-text text-sm leading-snug">【タイトル】</span>
          </div>
          <span class="freshness-chip inline-flex items-center gap-1 text-[10px] font-medium px-2 py-0.5 rounded-full border mb-2" data-last-reviewed="【YYYY-MM-DD】"></span>
          <div class="text-[11px] text-cpa-dim mb-2.5">作成: 【YYYY-MM-DD】</div>
          <div class="flex flex-wrap gap-1">
            <span class="text-[10px] bg-slate-100 text-slate-500 px-2 py-0.5 rounded-full">【タグ】</span>
          </div>
        </a>
      </div>
    </section>

    <!-- === 法律・規制 セクション === -->
    <section class="mb-10" data-domain-section>
      <div class="flex items-center gap-2 mb-4">
        <div class="w-1 h-5 rounded-full bg-violet-500"></div>
        <h2 class="text-sm font-bold text-violet-700">法律・規制</h2>
        <span class="text-xs text-cpa-dim ml-1" data-domain-count>【N】件</span>
      </div>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-3" data-card-grid>
        <a href="./【スラッグ】.html"
           class="diagram-card block bg-cpa-surface border border-cpa-border rounded-xl p-4 hover:border-violet-300 hover:shadow-sm transition-all"
           data-tags="【タグ1,タグ2…】">
          <div class="flex items-start justify-between gap-2 mb-2">
            <span class="font-bold text-cpa-text text-sm leading-snug">【タイトル】</span>
          </div>
          <span class="freshness-chip inline-flex items-center gap-1 text-[10px] font-medium px-2 py-0.5 rounded-full border mb-2" data-last-reviewed="【YYYY-MM-DD】"></span>
          <div class="text-[11px] text-cpa-dim mb-2.5">作成: 【YYYY-MM-DD】</div>
          <div class="flex flex-wrap gap-1">
            <span class="text-[10px] bg-slate-100 text-slate-500 px-2 py-0.5 rounded-full">【タグ】</span>
          </div>
        </a>
      </div>
    </section>

    <!-- === 経営・マネジメント セクション === -->
    <section class="mb-10" data-domain-section>
      <div class="flex items-center gap-2 mb-4">
        <div class="w-1 h-5 rounded-full bg-amber-500"></div>
        <h2 class="text-sm font-bold text-amber-700">経営・マネジメント</h2>
        <span class="text-xs text-cpa-dim ml-1" data-domain-count>【N】件</span>
      </div>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-3" data-card-grid>
        <a href="./【スラッグ】.html"
           class="diagram-card block bg-cpa-surface border border-cpa-border rounded-xl p-4 hover:border-amber-300 hover:shadow-sm transition-all"
           data-tags="【タグ1,タグ2…】">
          <div class="flex items-start justify-between gap-2 mb-2">
            <span class="font-bold text-cpa-text text-sm leading-snug">【タイトル】</span>
          </div>
          <span class="freshness-chip inline-flex items-center gap-1 text-[10px] font-medium px-2 py-0.5 rounded-full border mb-2" data-last-reviewed="【YYYY-MM-DD】"></span>
          <div class="text-[11px] text-cpa-dim mb-2.5">作成: 【YYYY-MM-DD】</div>
          <div class="flex flex-wrap gap-1">
            <span class="text-[10px] bg-slate-100 text-slate-500 px-2 py-0.5 rounded-full">【タグ】</span>
          </div>
        </a>
      </div>
    </section>

    <!-- 図解が0件のとき -->
    <!--
    <div class="text-center py-16 text-cpa-dim">
      <i data-lucide="file-plus" class="w-10 h-10 mx-auto mb-3 opacity-40"></i>
      <p class="text-sm">まだ図解がありません。「〇〇を図解して」と話しかけてください。</p>
    </div>
    -->

  </main>

  <!-- 鮮度チップ処理 -->
  <script>
  (function () {
    document.querySelectorAll('.freshness-chip').forEach(function (chip) {
      var last = new Date(chip.dataset.lastReviewed);
      var days = Math.floor((new Date() - last) / 86400000);
      var label, cls;
      if (days <= 30) {
        label = days + '日前';
        cls = 'text-emerald-700 bg-emerald-50 border-emerald-200';
      } else if (days <= 90) {
        label = days + '日前';
        cls = 'text-amber-700 bg-amber-50 border-amber-200';
      } else {
        label = days + '日前 · 要復習';
        cls = 'text-red-700 bg-red-50 border-red-200';
      }
      chip.className = 'freshness-chip inline-flex items-center gap-1 text-[10px] font-medium px-2 py-0.5 rounded-full border mb-2 ' + cls;
      chip.textContent = label;
    });
  })();
  </script>

  <!-- タグフィルター -->
  <script>
  (function () {
    var cards = document.querySelectorAll('.diagram-card');
    var filterBar = document.getElementById('tag-filter-bar');
    var filterCount = document.getElementById('filter-count');
    var activeTag = null;

    // 全タグ収集（重複排除・登場順）
    var allTags = [];
    var tagSet = {};
    cards.forEach(function (card) {
      var tags = (card.dataset.tags || '').split(',').map(function (t) { return t.trim(); }).filter(Boolean);
      tags.forEach(function (tag) {
        if (!tagSet[tag]) { tagSet[tag] = 0; }
        tagSet[tag]++;
        if (allTags.indexOf(tag) === -1) allTags.push(tag);
      });
    });

    // フィルターバーにボタンを生成
    function makeBtn(label, tag) {
      var btn = document.createElement('button');
      btn.type = 'button';
      btn.dataset.tag = tag || '';
      btn.className = 'filter-btn text-[11px] font-medium px-2.5 py-1 rounded-full border transition-colors ' +
        (tag === activeTag
          ? 'bg-slate-700 text-white border-slate-700'
          : 'bg-cpa-surface border-cpa-border text-cpa-muted hover:border-slate-400 hover:text-cpa-text');
      btn.textContent = label;
      btn.addEventListener('click', function () { applyFilter(tag); });
      return btn;
    }

    function renderBar() {
      filterBar.innerHTML = '';
      filterBar.appendChild(makeBtn('全て', null));
      allTags.forEach(function (tag) { filterBar.appendChild(makeBtn(tag, tag)); });
    }

    function applyFilter(tag) {
      activeTag = tag;
      renderBar();
      var visibleCount = 0;
      cards.forEach(function (card) {
        var tags = (card.dataset.tags || '').split(',').map(function (t) { return t.trim(); });
        var show = !tag || tags.indexOf(tag) !== -1;
        card.style.display = show ? '' : 'none';
        if (show) visibleCount++;
      });
      document.querySelectorAll('[data-domain-section]').forEach(function (section) {
        var grid = section.querySelector('[data-card-grid]');
        var visible = grid ? Array.from(grid.querySelectorAll('.diagram-card')).filter(function (c) {
          return c.style.display !== 'none';
        }).length : 0;
        section.style.display = visible > 0 ? '' : 'none';
        var countEl = section.querySelector('[data-domain-count]');
        if (countEl) countEl.textContent = visible + '件';
      });
      filterCount.textContent = tag ? visibleCount + '件表示中' : '';
    }

    renderBar();
  })();
  </script>

  <script src="https://unpkg.com/lucide@latest"></script>
  <script>lucide.createIcons();</script>
</body>
</html>
```

### カードの生成ルール

| 条件 | 処理 |
|------|------|
| `reviews` が 0 | 再確認バッジを表示しない |
| `reviews` が 1 以上 | 「N回」バッジを表示する |
| `data-tags` 属性 | DIAGRAM-META の **全タグ**を入れる（フィルター用）。表示は最大5個 |
| ドメインにカードが 0 件 | そのセクション全体を出力しない |
| `output/index.html` が存在しない | 新規作成する |
| `output/index.html` が存在する | 丸ごと上書きする |

---

## 全体構造図パターン

### A. 横並びフロー（プロセス・手順向け）

```html
<div class="bg-cpa-surface border border-cpa-border rounded-xl p-6 mb-8">
  <div class="flex items-center gap-2.5 mb-5">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-slate-600 bg-slate-100 px-2.5 py-1 rounded-full">
      <i data-lucide="layout" class="w-3 h-3"></i>
      全体構造
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>
  <div class="flex flex-col md:flex-row items-stretch gap-2">
    <!-- Step 1 -->
    <div class="flex-1 bg-blue-50 border border-blue-200 rounded-xl p-4 text-center">
      <div class="w-7 h-7 rounded-full bg-blue-500 text-white text-xs font-black flex items-center justify-center mx-auto mb-2">1</div>
      <i data-lucide="file-text" class="w-5 h-5 text-blue-600 mx-auto mb-1.5"></i>
      <div class="font-bold text-blue-800 text-sm">【ステップ名】</div>
      <div class="text-xs text-blue-600/70 mt-1">【一言説明】</div>
    </div>
    <!-- 矢印 -->
    <div class="flex items-center justify-center">
      <i data-lucide="chevron-right" class="w-5 h-5 text-cpa-dim hidden md:block"></i>
      <i data-lucide="chevron-down" class="w-5 h-5 text-cpa-dim md:hidden"></i>
    </div>
    <!-- Step 2（同様に繰り返す） -->
    <div class="flex-1 bg-emerald-50 border border-emerald-200 rounded-xl p-4 text-center">
      <div class="w-7 h-7 rounded-full bg-emerald-500 text-white text-xs font-black flex items-center justify-center mx-auto mb-2">2</div>
      <i data-lucide="shield-check" class="w-5 h-5 text-emerald-600 mx-auto mb-1.5"></i>
      <div class="font-bold text-emerald-800 text-sm">【ステップ名】</div>
      <div class="text-xs text-emerald-600/70 mt-1">【一言説明】</div>
    </div>
  </div>
</div>
```

---

### B. 左右比較（AとBの違い向け）

```html
<div class="bg-cpa-surface border border-cpa-border rounded-xl p-6 mb-8">
  <div class="flex items-center gap-2.5 mb-5">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-slate-600 bg-slate-100 px-2.5 py-1 rounded-full">
      <i data-lucide="layout" class="w-3 h-3"></i>
      全体構造
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>
  <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
    <!-- 左: A -->
    <div class="border-2 border-blue-300 rounded-xl p-5">
      <div class="font-black text-blue-700 text-base mb-3 flex items-center gap-2">
        <i data-lucide="layers" class="w-4 h-4"></i>
        【概念A】
      </div>
      <ul class="space-y-2 text-sm text-cpa-text">
        <li class="flex items-start gap-2"><i data-lucide="dot" class="w-4 h-4 text-blue-400 flex-shrink-0 mt-0.5"></i>【特徴1】</li>
        <li class="flex items-start gap-2"><i data-lucide="dot" class="w-4 h-4 text-blue-400 flex-shrink-0 mt-0.5"></i>【特徴2】</li>
      </ul>
    </div>
    <!-- 右: B -->
    <div class="border-2 border-violet-300 rounded-xl p-5">
      <div class="font-black text-violet-700 text-base mb-3 flex items-center gap-2">
        <i data-lucide="layers" class="w-4 h-4"></i>
        【概念B】
      </div>
      <ul class="space-y-2 text-sm text-cpa-text">
        <li class="flex items-start gap-2"><i data-lucide="dot" class="w-4 h-4 text-violet-400 flex-shrink-0 mt-0.5"></i>【特徴1】</li>
        <li class="flex items-start gap-2"><i data-lucide="dot" class="w-4 h-4 text-violet-400 flex-shrink-0 mt-0.5"></i>【特徴2】</li>
      </ul>
    </div>
  </div>
  <!-- 中心の違いポイント -->
  <div class="mt-4 text-center text-xs text-cpa-muted flex items-center gap-2 justify-center">
    <i data-lucide="arrow-left-right" class="w-4 h-4"></i>
    最大の違い: 【一言で】
  </div>
</div>
```

---

### C. 入れ子ブロック（階層・包含関係向け）

```html
<div class="bg-cpa-surface border border-cpa-border rounded-xl p-6 mb-8">
  <div class="flex items-center gap-2.5 mb-5">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-slate-600 bg-slate-100 px-2.5 py-1 rounded-full">
      <i data-lucide="layout" class="w-3 h-3"></i>
      全体構造
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>
  <!-- 外側の大きな枠 -->
  <div class="border-2 border-slate-300 rounded-2xl p-5 bg-slate-50">
    <div class="text-xs font-bold text-slate-500 mb-4">【上位概念】</div>
    <!-- 内側 -->
    <div class="grid grid-cols-2 gap-3">
      <div class="border border-blue-200 rounded-xl p-4 bg-white">
        <div class="text-xs font-bold text-blue-600 mb-1">【構成要素A】</div>
        <p class="text-xs text-cpa-muted">【説明】</p>
      </div>
      <div class="border border-emerald-200 rounded-xl p-4 bg-white">
        <div class="text-xs font-bold text-emerald-600 mb-1">【構成要素B】</div>
        <p class="text-xs text-cpa-muted">【説明】</p>
      </div>
    </div>
  </div>
</div>
```

---

### D. 数値カード（規模感・データ向け）

```html
<div class="grid grid-cols-3 gap-4 mb-8">
  <div class="bg-cpa-surface border border-cpa-border rounded-xl p-5 text-center">
    <div class="text-3xl font-black text-blue-600 mb-1">【数値】</div>
    <div class="text-xs text-cpa-muted">【説明】</div>
  </div>
  <div class="bg-cpa-surface border border-cpa-border rounded-xl p-5 text-center">
    <div class="text-3xl font-black text-emerald-600 mb-1">【数値】</div>
    <div class="text-xs text-cpa-muted">【説明】</div>
  </div>
  <div class="bg-cpa-surface border border-cpa-border rounded-xl p-5 text-center">
    <div class="text-3xl font-black text-amber-600 mb-1">【数値】</div>
    <div class="text-xs text-cpa-muted">【説明】</div>
  </div>
</div>
```

---

### E. 条文引用＋実務解釈対比（会計基準・法律向け）

会計基準・法律の原文と実務上の意味を上下に対比させるパターン。**会計・監査 / 法律・規制ドメイン**で条文・基準を扱うとき優先して使う。

```html
<div class="bg-cpa-surface border border-cpa-border rounded-xl p-6 mb-8">
  <div class="flex items-center gap-2.5 mb-5">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-slate-600 bg-slate-100 px-2.5 py-1 rounded-full">
      <i data-lucide="layout" class="w-3 h-3"></i>
      全体構造
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>

  <!-- 条文引用ブロック -->
  <div class="bg-slate-50 border border-slate-200 rounded-xl p-4 mb-3">
    <div class="flex items-center gap-2 mb-2">
      <i data-lucide="file-text" class="w-3.5 h-3.5 text-slate-500"></i>
      <span class="text-[11px] font-bold text-slate-500 font-mono">【出典: 基準名・条番号】</span>
    </div>
    <p class="text-sm text-slate-700 leading-relaxed font-mono border-l-2 border-slate-300 pl-3">
      「【条文・基準の原文をそのまま引用】」
    </p>
  </div>

  <!-- 変換矢印 -->
  <div class="flex items-center justify-center gap-2 mb-3">
    <div class="h-px flex-1 bg-cpa-border"></div>
    <div class="flex items-center gap-1.5 bg-emerald-100 text-emerald-700 text-[11px] font-bold px-3 py-1 rounded-full">
      <i data-lucide="arrow-down" class="w-3 h-3"></i>
      実務では
    </div>
    <div class="h-px flex-1 bg-cpa-border"></div>
  </div>

  <!-- 実務解釈ブロック -->
  <div class="bg-emerald-50/60 border border-emerald-200 rounded-xl p-4">
    <div class="flex items-center gap-2 mb-3">
      <i data-lucide="briefcase" class="w-3.5 h-3.5 text-emerald-600"></i>
      <span class="text-[11px] font-bold text-emerald-700">実務上の意味</span>
    </div>
    <ul class="space-y-2">
      <li class="flex items-start gap-2 text-sm text-cpa-text">
        <i data-lucide="check-circle" class="w-4 h-4 text-emerald-500 flex-shrink-0 mt-0.5"></i>
        <span>【実務的な意味・何をするか】</span>
      </li>
      <li class="flex items-start gap-2 text-sm text-cpa-text">
        <i data-lucide="check-circle" class="w-4 h-4 text-emerald-500 flex-shrink-0 mt-0.5"></i>
        <span>【いつ・どの場面で適用されるか】</span>
      </li>
    </ul>
  </div>
</div>
```

---

### F. 改正ビフォーアフタータイムライン（法改正・新基準適用向け）

法改正や会計基準の新旧対比を時系列で見せるパターン。「何がどう変わったか」が一目でわかる。**法律・規制 / 会計・監査ドメイン**の改正・適用テーマで使う。

```html
<div class="bg-cpa-surface border border-cpa-border rounded-xl p-6 mb-8">
  <div class="flex items-center gap-2.5 mb-6">
    <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                 text-slate-600 bg-slate-100 px-2.5 py-1 rounded-full">
      <i data-lucide="layout" class="w-3 h-3"></i>
      全体構造
    </span>
    <div class="flex-1 h-px bg-cpa-border"></div>
  </div>

  <div class="flex flex-col md:flex-row items-stretch gap-3">

    <!-- 旧（改正前）ブロック -->
    <div class="flex-1 bg-slate-50 border-2 border-slate-200 rounded-xl p-5">
      <div class="flex items-center gap-2 mb-1">
        <span class="text-[10px] font-bold text-slate-400 bg-slate-100 px-2 py-0.5 rounded-full">〜【YYYY年】改正前</span>
      </div>
      <div class="font-black text-slate-600 text-sm mb-3">【旧基準・旧規制の名称】</div>
      <ul class="space-y-2 text-xs text-slate-600">
        <li class="flex items-start gap-1.5">
          <i data-lucide="minus" class="w-3.5 h-3.5 text-slate-400 flex-shrink-0 mt-0.5"></i>
          【旧制度の特徴・扱い】
        </li>
        <li class="flex items-start gap-1.5">
          <i data-lucide="minus" class="w-3.5 h-3.5 text-slate-400 flex-shrink-0 mt-0.5"></i>
          【旧制度の課題・問題点】
        </li>
      </ul>
    </div>

    <!-- 改正ポイント（中央） -->
    <div class="flex flex-col items-center justify-center gap-1 px-2">
      <i data-lucide="arrow-right" class="w-5 h-5 text-violet-400 hidden md:block"></i>
      <i data-lucide="arrow-down" class="w-5 h-5 text-violet-400 md:hidden"></i>
      <span class="text-[10px] font-bold text-violet-600 bg-violet-50 border border-violet-200 px-2 py-1 rounded-lg text-center leading-snug">
        【YYYY年】<br>改正
      </span>
      <i data-lucide="arrow-right" class="w-5 h-5 text-violet-400 hidden md:block"></i>
      <i data-lucide="arrow-down" class="w-5 h-5 text-violet-400 md:hidden"></i>
    </div>

    <!-- 新（改正後）ブロック -->
    <div class="flex-1 bg-violet-50/60 border-2 border-violet-300 rounded-xl p-5">
      <div class="flex items-center gap-2 mb-1">
        <span class="text-[10px] font-bold text-violet-600 bg-violet-100 px-2 py-0.5 rounded-full">【YYYY年】〜 現行</span>
      </div>
      <div class="font-black text-violet-700 text-sm mb-3">【新基準・新規制の名称】</div>
      <ul class="space-y-2 text-xs text-violet-800">
        <li class="flex items-start gap-1.5">
          <i data-lucide="plus" class="w-3.5 h-3.5 text-violet-500 flex-shrink-0 mt-0.5"></i>
          【新制度の変更点・追加事項】
        </li>
        <li class="flex items-start gap-1.5">
          <i data-lucide="plus" class="w-3.5 h-3.5 text-violet-500 flex-shrink-0 mt-0.5"></i>
          【実務への影響】
        </li>
      </ul>
    </div>

  </div>

  <!-- 改正の核心メモ -->
  <div class="mt-4 flex items-start gap-2 bg-amber-50 border border-amber-200 rounded-lg px-4 py-2.5">
    <i data-lucide="alert-circle" class="w-4 h-4 text-amber-600 flex-shrink-0 mt-0.5"></i>
    <p class="text-xs text-amber-800"><span class="font-bold">改正の核心:</span> 【なぜ改正されたか・何が本質的に変わったか】</p>
  </div>
</div>
```

---

## 完成HTMLの骨格（AIコピペ用）

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
              bg: '#F8F9FB', surface: '#FFFFFF', border: '#E5E7EB',
              text: '#111827', muted: '#6B7280', dim: '#9CA3AF',
            }
          },
          fontFamily: { sans: ['"Noto Sans JP"', '"Hiragino Sans"', 'sans-serif'] }
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

  <!-- ドメインバー: AI/テック=blue-600 / 会計=emerald-600 / 法律=violet-600 / 経営=amber-600 -->
  <div class="h-1.5 bg-blue-600"></div>

  <main class="max-w-3xl mx-auto px-5 py-10">

    <!-- ① ヘッダー -->
    <div class="mb-10">
      <div class="inline-flex items-center gap-2 bg-blue-50 text-blue-700 px-3 py-1 rounded-full text-xs font-medium mb-4">
        <i data-lucide="cpu" class="w-3.5 h-3.5"></i>
        【ドメイン名】
      </div>
      <h1 class="text-3xl font-black text-cpa-text mb-2">【タイトル】</h1>
      <p class="text-xs text-cpa-dim">作成: 【YYYY-MM-DD】</p>
    </div>

    <!-- ② 核心ひとこと -->
    <div class="bg-white border-l-4 border-blue-500 rounded-r-xl px-6 py-5 mb-8 shadow-sm">
      <div class="text-xs text-blue-600 font-medium mb-2 flex items-center gap-1.5">
        <i data-lucide="zap" class="w-3.5 h-3.5"></i>
        核心ひとこと
      </div>
      <p class="text-lg font-bold text-cpa-text leading-snug">
        【1〜2文の本質的な定義】
      </p>
    </div>

    <!-- ③ 全体構造図 -->
    <!-- ↑ A〜Dのパターンから選んで配置 -->

    <!-- ④ 押さえる3点 -->
    <div class="mb-8">
      <h2 class="text-sm font-bold text-cpa-muted uppercase tracking-wider mb-4 flex items-center gap-2">
        <i data-lucide="list-checks" class="w-4 h-4"></i>
        押さえる3点
      </h2>
      <div class="space-y-3">
        <!-- border-l-4 で重要度の序列を示す: 1番=濃い / 2番=中 / 3番=薄い -->
        <div class="flex items-start gap-4 bg-white border border-cpa-border border-l-4 border-l-blue-500 rounded-xl p-4">
          <div class="w-8 h-8 rounded-full bg-blue-500 text-white text-sm font-black flex items-center justify-center flex-shrink-0">1</div>
          <div>
            <!-- タイトルはドメインカラー(blue-700)固定: 縦スキャン時の視認性UP -->
            <div class="font-black text-blue-700 mb-1.5">【ポイント1タイトル】</div>
            <p class="text-sm text-cpa-muted leading-relaxed">【説明】</p>
          </div>
        </div>
        <div class="flex items-start gap-4 bg-white border border-cpa-border border-l-4 border-l-blue-300 rounded-xl p-4">
          <div class="w-8 h-8 rounded-full bg-emerald-500 text-white text-sm font-black flex items-center justify-center flex-shrink-0">2</div>
          <div>
            <div class="font-black text-blue-700 mb-1.5">【ポイント2タイトル】</div>
            <p class="text-sm text-cpa-muted leading-relaxed">【説明】</p>
          </div>
        </div>
        <div class="flex items-start gap-4 bg-white border border-cpa-border border-l-4 border-l-blue-200 rounded-xl p-4">
          <div class="w-8 h-8 rounded-full bg-amber-500 text-white text-sm font-black flex items-center justify-center flex-shrink-0">3</div>
          <div>
            <div class="font-black text-blue-700 mb-1.5">【ポイント3タイトル】</div>
            <p class="text-sm text-cpa-muted leading-relaxed">【説明】</p>
          </div>
        </div>
      </div>
    </div>

    <!-- ⑤ 実務場面: bg-emerald-50/40 border-emerald-200 固定 -->
    <div class="bg-emerald-50/40 border border-emerald-200 rounded-xl p-6 mb-8">
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
          <div class="w-5 h-5 rounded bg-violet-100 flex items-center justify-center flex-shrink-0 mt-0.5">
            <i data-lucide="check" class="w-3 h-3 text-violet-600"></i>
          </div>
          <div>
            <span class="text-xs font-bold text-violet-700 bg-violet-50 border border-violet-200 px-2 py-0.5 rounded mr-2">上場準備</span>
            <span class="text-sm text-cpa-text">【場面の説明】</span>
          </div>
        </div>
        <div class="flex items-start gap-3">
          <div class="w-5 h-5 rounded bg-blue-100 flex items-center justify-center flex-shrink-0 mt-0.5">
            <i data-lucide="check" class="w-3 h-3 text-blue-600"></i>
          </div>
          <div>
            <span class="text-xs font-bold text-blue-700 bg-blue-50 border border-blue-200 px-2 py-0.5 rounded mr-2">監査法人対応</span>
            <span class="text-sm text-cpa-text">【場面の説明】</span>
          </div>
        </div>
      </div>
    </div>

    <!-- ⑥ 混乱しやすいポイント: bg-amber-50/40 border-amber-200 固定 -->
    <div class="bg-amber-50/40 border border-amber-200 rounded-xl p-6 mb-8">
      <div class="flex items-center gap-2.5 mb-5">
        <span class="inline-flex items-center gap-1.5 text-[11px] font-bold
                     text-amber-700 bg-amber-100 px-2.5 py-1 rounded-full">
          <i data-lucide="alert-triangle" class="w-3 h-3"></i>
          混乱しやすいポイント
        </span>
        <div class="flex-1 h-px bg-amber-200/60"></div>
      </div>
      <div class="space-y-4">
        <div class="border border-amber-200 bg-amber-50/50 rounded-xl p-4">
          <div class="flex items-center gap-2 mb-3">
            <i data-lucide="help-circle" class="w-4 h-4 text-amber-600"></i>
            <span class="text-sm font-bold text-amber-800">【混乱しがちな点】</span>
          </div>
          <div class="grid grid-cols-2 gap-3 text-sm">
            <div class="bg-white rounded-lg p-3 border border-red-200">
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

    <!-- ⑦ 関連キーワード -->
    <!-- 既存図解がある場合はリンク付きバッジ、未図解は通常バッジ -->
    <div class="mb-6">
      <h2 class="text-sm font-bold text-cpa-muted uppercase tracking-wider mb-3 flex items-center gap-2">
        <i data-lucide="link" class="w-4 h-4"></i>
        関連キーワード
      </h2>
      <div class="flex flex-wrap gap-2">
        <!-- 既存図解あり: リンク付きバッジ（図解先ドメインの色に合わせる） -->
        <a href="./【スラッグ】.html"
           class="inline-flex items-center gap-1.5 bg-white border border-blue-200
                  text-blue-600 text-xs px-3 py-1.5 rounded-full
                  hover:bg-blue-50 hover:border-blue-400 transition-colors">
          <i data-lucide="file-text" class="w-3 h-3"></i>
          【キーワード（既存図解あり）】
        </a>
        <!-- 未図解: 通常バッジ -->
        <span class="bg-white border border-cpa-border text-cpa-muted text-xs px-3 py-1.5 rounded-full">【キーワード（未図解）】</span>
      </div>
    </div>

    <!-- ⑧ フッター: インデックスへ戻る（全図解の標準フッター） -->
    <div class="border-t border-cpa-border pt-6">
      <a href="./index.html"
         class="inline-flex items-center gap-2 text-sm text-cpa-muted hover:text-cpa-text transition-colors group">
        <i data-lucide="arrow-left" class="w-4 h-4 group-hover:-translate-x-0.5 transition-transform"></i>
        知識ストック一覧に戻る
      </a>
    </div>

  </main>

  <script src="https://unpkg.com/lucide@latest"></script>
  <script>lucide.createIcons();</script>
</body>
</html>
```
