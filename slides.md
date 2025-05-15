---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: 簡約的軟體開發思維：用 Functional Programming 重構程式 - 以 Javascript 為例
info: |
  ## 讀書會簡報

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true

# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
---

## 簡約的軟體開發思維

## 用 Functional Programming 重構程式 - 以 Javascript 為例

<br>

### 導讀人：Hannah

### 第 8 章 分層設計(1)

### 第 9 章 分層設計(2)

<div class="abs-br m-6 text-xl">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="slidev-icon-btn">
    <carbon:edit />
  </button>
  <a href="https://github.com/hangineer/Functional-Programming-Slide" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: slide-up
level: 2
---
# 前次回顧 CH6 ~ 7

<img src="https://i.imgur.com/eDcmfjl.png" >



---
transition: slide-up
level: 2
---
# 回顧：CH6 在變動的程式中讓資料保持不變
## 寫入時複製三步驟

1. 產生副本
2. 修改副本
3. 傳回副本
<br>

* JavaScript 的寫入時複製需自行實作，例如用 .slice，寫入時複製會產生副本，再修改副本來取代更動原資料
* 實作不可變的巢狀資料時，需先進行前拷貝，然後更改副本，最後將其回傳

<!-- NOTE:  -->

---
transition: slide-up
level: 2
---

# 回顧：CH7 讓不變性不受外來程式破壞

* 寫入時複製（Copy-on-write）<br>
在程式邏輯可控時，效率高、淺拷貝即可應用
* 防禦型複製（Defensive Copying）<br>
當資料要離開安全區、進入不可預期或不受信任的函式時，能有效保護資料不被改變，是針對跨安全區存取資料時保護資料的一種方法，使用的是深考貝
* 由於「寫入時複製」所需資源較少，所以通常優先使用。只有當需要用到不受信任的程式時，才會做「防禦型複製」


---
## transition: fade-out
---

# 第 8 章 分層設計(1)

### 學習目標：

- 了解「軟體設計」的實用定義
- 了解「分層設計」的概念和用法
- 介紹分層架構的四大原則，主要偏重「原則一」
- 了解如何透過「函式擷取」讓程式碼更直觀

---
transition: slide-up
level: 2
---

<!-- https://sli.dev/guide/animations.html#click-animation -->

# 何謂「軟體設計」(Software Design)

<p class="pt-2" v-click>根據某種「原則」決定程式實作的方式，讓撰寫、維護、測試上更容易 （定義不用背）</p>

<p class="pt-2" v-click>「原則」就是 ...</p>
<h2 v-click>分層設計 Stratified Design</h2>
<!-- NOTE: 定義不用背後：設計完善的程式應讓人感到放心，協助我們度過開發週期中的每個階段，包括構想、程式撰寫、測試、維護 -->

---
transition: slide-up
level: 2
---

# 何謂「分層設計」(Stratified Design)
<p v-click>將程式分隔成多個層 (layers) 的設計方法，換句話說，此種架構會產生多個層</p>
<p v-click>分層設計已有悠久歷史，且由許多人共同發展而成</p>
<p v-click>以操作購物車為例</p>
<img v-after width=750 src="https://i.imgur.com/2I58jFz.jpeg" />

<!-- NOTE: 紅字：每一層的功能 -->

---
transition: slide-up
level: 2
---

# 建立設計直覺

決定適當的分層並不容易，「最佳設計」必須考量很多因素，可以培養「設計的直覺」，讓寫程式時能有個方向

<h3 v-click>分層設計的「輸入」有哪些</h3>
<div v-click class="overflow-x-auto">
  <table>
    <thead>
      <tr>
        <th><b>函式本體</b></th>
        <th><b>層的結構</b></th>
        <th><b>函式簽章</b></th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>程式碼長度</td>
        <td>箭頭長度</td>
        <td>函式名稱</td>
      </tr>
      <tr>
        <td>複雜度</td>
        <td>內聚性</td>
        <td>參數名稱</td>
      </tr>
      <tr>
        <td>細節程度</td>
        <td>細節程度</td>
        <td>參數值</td>
      </tr>
      <tr>
        <td>函式呼叫</td>
        <td></td>
        <td>回傳值</td>
      </tr>
      <tr>
        <td>使用的程式語言元素</td>
      </tr>
    </tbody>
  </table>
</div>

<p v-after>🔺 可把「輸入」當成優化程式的線索</p>
<!-- NOTE:
  有些名詞看不懂沒關係，後面透過範例再慢慢感受
  當遇到優化程式碼的時候，可以從這些線索著手
-->

---
transition: slide-up
level: 2
---

# 建立設計直覺

<h3 v-click>分層設計的「輸出」有哪些</h3>
<div v-click class="overflow-x-auto">
  <table>
    <thead>
      <tr>
        <th><b>程式架構</b></th>
        <th><b>程式實作</b></th>
        <th><b>程式碼變更</b></th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>決定哪裡需要新函式</td>
        <td>修改實作</td>
        <td>決定在何處加入新程式碼</td>
      </tr>
      <tr>
        <td>移動函式的位置</td>
        <td>擷取函式</td>
        <td>選擇適當的細節程度</td>
      </tr>
      <tr>
        <td></td>
        <td>改變資料結構</td>
      </tr>
     </tbody>
  </table>
</div>
<!-- NOTE: 有輸入就會有「輸出」，根據程式碼的輸入，就會產生一系列的輸出，包括：...-->

---
transition: slide-up
level: 2
---

# 分層設計的原則

<!-- 後面會再次詳細提到 -->

<h3 class="mb-3" v-click>🔺原則 1：讓實作更直觀</h3>
<pre v-click class="mb-8">在直觀的程式中，元素的「細節程度」都差不多，若混雜不同的「細節程度」，則可視為有「程式碼異味」
🔹 程式碼異味：可能有潛在問題的程式碼特徵
🔹 本書中的「細節程度」和「抽象層級」可當同義詞看待，後面會再說明
</pre>
<h3 v-click>🔺原則 2：以抽象屏障輔助實作</h3>
<p v-click class="mb-10">可以把某些「層」(layers) 當成「屏障」 (也可叫介面 interface)，屏障可以隱藏實作細節，讓我們更專注在特定層的程式碼運作</p>

<h3 v-click>🔺原則 3：讓下層函式保持簡約不變</h3>
<p v-click class="mb-10">為了提高維護性，低層級（底層）的函式盡量越簡單越好，並用它來直接或間接定義上層函式</p>

<h3 v-click>🔺原則 4：分層只要舒適即可</h3>
<p v-click>勿過度追求完美</p>

<!-- NOTE:
  勿過度追求完美：分層應該讓你事半功倍，而不是讓事情變得更複雜
  本章會先介紹原則一，剩下的下一章會介紹
-->

---
transition: slide-up
level: 2
---

# 原則 1：讓實作更直觀
<p>情境：MegaMart 目前的購物車實作不理想，每次操作購物車都怕影響到其他地方。程式設計師為了解決特定問題，所以寫了以下這段程式碼：</p>

```javascript
// 使用者購買領帶時，贈送一個領帶夾
function freeTieClip(cart) {
  var hasTie = false; // 領帶
  var hasTieClip = false; // 領帶夾
  // 檢查購物車內是否有領帶或領帶夾
  for (var i = 0; i < cart.length; i++) {
    var item = cart[i];
    if (item.name === "tie") hasTie = true;
    if (item.name === "tie clip") hasTieClip = true;
  }

  if (hasTie && !hasTieClip) {
    var tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip); // 加入領帶夾
  }
  return cart;
}
```
<p v-click>這段程式碼有什麼問題？</p>
<p v-click>存在太多細節，在迴圈中做邏輯判斷與副作用，職責混亂，不符合「層」的思考，不直觀也不利於維護</p>

<!-- NOTE:
這段函式做了
  1. 遍歷購物車
  2. 檢查商品內容並作出相對應的決策
-->


---
layout: two-cols
layoutClass: gap-16
---
# 列出需要的購物車操作

<img src="https://i.imgur.com/tplEiD0.png" />
<a href="https://i.imgur.com/tplEiD0.png" target="_blank">圖片連結</a>
::right::
<br><br>
<p>情境：MegaMart 決定為購物車有關的函式做優化，以下是成員們列出的一系列購物車基本操作：</p>
<p>✔️ 的項目代表程式碼已存在</p>
<p>有兩項操作尚未被實作，這是等等的任務</p>

---
transition: slide-up
level: 2
---

# 檢查商品是否存在於購物車內

```javascript {all|5-10}
// 使用者購買領帶，即贈送一個領帶夾
function freeTieClip(cart) {
  var hasTie = false
  var hasTieClip = false;
  for (var i = 0; i < cart.length; i++) {
    var item = cart[i];
    if (item.name === "tie") hasTie = true;
    if (item.name === "tie clip") hasTieClip = true;
  }

  if (hasTie && !hasTieClip) {
    var tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip);
  }

  return cart;
}
```
<p>此 for 迴圈只是在找出特定商品是否存在在購物車裡</p>
<div v-click>一般來說<b>低階的程式碼</b>常成為取代目標
  <p>* 低階：與底層運作有關</p>
</div>


---
layout: two-cols
layoutClass: gap-18
---

# 找出可取代的目標程式碼


<p>Before</p>

```javascript{all|1-3,5-9}
function freeTieClip(cart) {
  var hasTie = false
  var hasTieClip = false;

  for (var i = 0; i < cart.length; i++) {
    var item = cart[i];
    if (item.name === "tie") hasTie = true;
    if (item.name === "tie clip") hasTieClip = true;
  }

  if (hasTie && !hasTieClip) {
    var tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip);
  }

  return cart;
}
```

::right::

<br >

<p>After</p>

```javascript{all|11-18,2-3}
function freeTieClip(cart) {
  var hasTie = isInCart(cart, "tie");
  var hasTieClip = isInCart(cart, "tie clip");
 
  if (hasTie && !hasTieClip) {
    var tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip);
  }
  return cart;
}
 
function isInCart(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return true;
  }
  return false;
}
```
<!-- NOTE: 優化後的程式不僅比較短，且函式中的程式碼皆有類似細節程度 -->

---
transition: slide-up
level: 2
---

# 將函式間的呼叫畫成「呼叫圖」(call graph)
<p>以 freeTieClip() 為例，我們要找出 freeTieClip 呼叫了哪個函式、以及使用了哪些程式語言元素（如：迴圈），把它畫成一張圖</p>

```javascript{1,5-7,12-15}
function freeTieClip(cart) {
  var hasTie = false
  var hasTieClip = false;

  for (var i = 0; i < cart.length; i++) { // for loop
    var item = cart[i]; // array index
    if (item.name === "tie") hasTie = true;
    if (item.name === "tie clip") hasTieClip = true;
  }

  if (hasTie && !hasTieClip) {
    var tieClip = make_item("tie clip", 0); // make_item()
    return add_item(cart, tieClip); // add_item()
  }

  return cart;
}
```
<!-- NOTE: 用另一種方式觀察舊版的 freeTieClip，這函式裡使用了迴圈、 array index、
呼叫了兩個函式 make_item()、add_item() -->

---
transition: slide-up
level: 2
---

<h3>呼叫圖 (call graph)</h3>
<img width=700 height=300 src="https://i.imgur.com/NxcSkVf.jpeg">

<p v-click>被呼叫的元素在下方，故箭頭向下</p>
<p v-click>上層函式只能呼叫下層函式，所以不會出現向上箭頭</p>
<p v-click>freeTieClip() 使用了抽象層級不同的元素：程式語言內建元素 & 自行撰寫的函式</p>

<h4 v-click>所以可以依照「抽象層級」來優化呼叫圖....</h4>

<!-- NOTE: 程式語言內建元素 & 自行撰寫的函式並不在同樣的層級 -->
---
transition: slide-up
level: 2
---

<h3>優化後的呼叫圖</h3>
<img width=700 height=300 src="https://i.imgur.com/F8T9znE.jpeg" />
<p>把不同的抽象層級拆開，程式內建元素是一層，自行撰寫的函式是一層</p>
<h4 v-click>但在原則 1：直觀的實作中，需使用抽象層級相當的元素，現在的呼叫圖有不同層級的元素...</h4>
<h4 v-click>這也會讓函式沒那麼直觀</h4>
---
transition: slide-up
level: 2
---

### 還記得前面優化後的 `freeTieClip()` 嗎?

```javascript
function freeTieClip(cart) {
  var hasTie = isInCart(cart, "tie");
  var hasTieClip = isInCart(cart, "tie clip");
 
  if (hasTie && !hasTieClip) {
    var tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip);
  }
  return cart;
}
 
function isInCart(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return true;
  }
  return false;
}
```
<br />
<h4 v-click>接著，把這段優化後的函式畫成呼叫圖</h4>

---
transition: slide-up
level: 2
---
<p>新版的呼叫圖</p>
<img width=600 height=300 src="https://i.imgur.com/9I8E1lb.jpeg" />
<p>isInCart() 呼叫了兩次，但在呼叫圖中畫一次就好</p>
<p>呼叫圖中皆是抽象層級相當的元素</p>


---
transition: slide-up
level: 2
---

# 呼叫圖 Q & A

* Q1：有必要畫呼叫圖嗎？從程式碼應該就能看出問題了吧？
* A1：畫呼叫圖只是用來確認呼叫關係，並非必要。但隨著程式的複雜性提高，函式越來越多，層的數量也會增加，這時呼叫圖就能提供一個全局觀，對於培養設計直覺非常有幫助
<br />
<br />
* Q2：每次寫程式都要畫呼叫圖嗎？
* A2：只要熟悉分層架構，就可以在腦中建構呼叫圖，所以也並非必要。不過，當要與他人合作時，呼叫圖不失為一個有效溝通的工具
<br />
<br />
* Q3：這些「層」是真實存在嗎？有可能不同人畫出來的層不一樣嗎？
* A3：「分層設計」是一種觀點，許多人透過它來思考如何寫程式，進而找出提升重複使用性、可測試性與可維護性的方法。程式的層並非絕對，當有人的分層方式與你不同時，可了解別人的分層邏輯為何

---
transition: slide-up
level: 2
---

# 在呼叫圖中加入 `remove_item_by_name()`

```javascript
function remove_item_by_name(cart, name) { // 移除一項商品
  var idx = null;
  for (var i = 0; i < cart.length; i++) { // 遍歷購物車中的每一個商品，如果找到商品，就把該 i 存入 idx
    if (cart[i].name === name) idx = i;
  }
  if (idx !== null) return removeItems(cart, idx, 1); // 找到商品，就呼叫 removeItems 移除它
  return cart; // 回傳原始的 cart
}
```

<p v-click>若想要把 remove_item_by_name() 跟既有的呼叫圖合併，要放在哪呢？</p>
<img v-after width=570 height=300 src="https://i.imgur.com/uhbaO7F.jpeg" />
<arrow v-click x1="650" y1="400" x2="580" y2="490" color="green" width="4" arrowSize="1" />

<!-- NOTE: 接下來要把購物車的操作逐一加入呼叫圖中
  答案會用箭頭指出來（底層）
  remove_item_by_name() 是一項通用的購物車操作，因此底層是目前最合適的選擇
  此外，函式名稱也可為分層提供線索
-->


---
transition: slide-up
level: 2
---

# 放在「頂層與底層」之間不行嗎？
<p v-click>沒辦法 100% 確定！</p>
<p v-click>不過可以檢視一下位於「底層」的函式呼叫了哪些東西</p>
<p v-click>若呼叫的東西高度重疊，就能進一步支持 remove_item_by_name() 更適合「底層」</p>
<img v-after width=700 src="https://i.imgur.com/uufS3Pc.jpeg" />


---
layout: two-cols
layoutClass: gap-16
---

<h2 class="mb-5">如果加上更多購物車操作呢？</h2>

```javascript
// 1. 計算總額
function calc_total(cart) {
  var total = 0;
  for (var i = 0; i < cart.length; i++) {
    var item = cart[i];
    total += item.price;
  }
  return total;
}

// 2. 是否有免運
function gets_free_shipping(cart) {
  return calc_total(cart) >= 20;
}
```

::right::
<br />
<br />

```javascript
// 3. 根據商品名稱設定價格
function setPriceByName(cart, name, price) {
  var cartCopy = cart.slice();
  for (var i = 0; i < cartCopy.length; i++) {
    if (cartCopy[i].name === name)
    cartCopy[i] = setPrice(cartCopy[i], price);
  }
  return cartCopy;
}

// 4. 計算稅金
function cartTax(cart) {
  return calc_tax(calc_total(cart));
}
```
<!-- NOTE: 可以想一下這四個函式中分別呼叫了什麼函式或使用了什麼元素，怕忘記 code 的可以拿起你的手機把程式拍下來 -->

---
transition: slide-up
level: 2
---

要把這四個函式加入呼叫圖中，調整到最合適的層
* `calc_total()` 計算總額
* `gets_free_shipping()` 是否有免運
* `setPriceByName()` 根據商品名稱設定價格
* `cartTax()` 計算稅金

<img width=700 src="https://i.imgur.com/LwJWU00.jpeg" alt="目前已知的函式和呼叫圖" />


---
transition: slide-up
level: 2
---

<h3>解答</h3>
<img v-click width=700 src="https://i.imgur.com/WbdCuH8.jpeg" alt="8-2 解答" />

<p v-click>所有箭頭都朝下，不該有平行或向上箭頭</p>
<!-- NOTE:
  gets_free_shipping(): 呼叫了 calc_total()
  cartTax()：呼叫了 calc_tax()
  setPriceByName()：使用了 for loop, arr index, slice 且還呼叫了 setPrice()
  calc_total()：使用了 for loop, arr index
-->


---
transition: slide-up
level: 2
---

# 同一層的函式應服務相同目的
<img width=650 src="https://i.imgur.com/kEbzCs1.jpeg" />
<ul>
  <li>每個層都代表一個「抽象層級」</li>
  <li>當某一層的函式被呼叫時，不需擔心任何低於該層的細節</li>
  <li>箭頭很亂也代表程式的呼叫關係也很亂！直觀的實作中，箭頭長度要一致</li>
</ul>
<!-- NOTE:
  不需擔心任何低於該層的細節：當呼叫購物車業務邏輯的函式時，我們不需要知道購物車是以陣列實作的，因為購物車的操作那層會處理
  箭頭長度要一致：箭頭有些只跨一層，有些跨三層，這代表同層中的函式有不同細節程度
-->


---
transition: slide-up
level: 2
---

# 三個不同的檢視等級

<p v-click>呼叫圖可以幫助我們發現錯誤，但資訊一多，也會更難知道錯誤在哪。事實上，在分層設計中，問題有可能來自以下三個地方：</p>

<ul>
  <li v-click>層與層的互動</li>
  <li v-click>某一層的實作</li>
  <li v-click>某一函式的實作</li>
</ul>

<p v-click>這三個地方須以不同檢視等級來查看</p>
<div v-click>
  <pre>1.全域檢視 (Global zoom level)
   觀察整張呼叫圖，此為預設的檢視等級
  </pre>
</div>

<div v-click>
  <pre>2.層檢視 (Layer zoom level)
   只關注某個目標層和其下方有「直接」呼叫關係的層
  </pre>
</div>

<div v-click>
  <pre>3.函式檢視 (Function zoom level)
   只關注某個目標函式和所有被該函式「直接」呼叫的下層函式
  </pre>
</div>


---
transition: slide-up
level: 2
---

# 以「層」檢視等級比較不同函式的箭頭關係
<p v-click>切換到「層」檢視等級，只看目標層和其下方有「直接」呼叫關係的層</p>
<p v-click>以「基本購物車操作」這層為例</p>
<img v-after src="https://i.imgur.com/L0ypW1v.jpeg" />

<!-- NOTE:  圖中的箭頭看起來很亂，箭頭亂也代表呼叫關係很亂，直觀的實作中，箭頭長度要一致
  在討論解法前，讓我們先放大到單一函式上
-->

---
transition: slide-up
level: 2
---

# 以「函式」檢視等級查看單一函式的箭頭關係
<p v-click>以 remove_item_by_name() 為例</p>
<img v-after src="https://i.imgur.com/DBDI7NJ.jpeg" />
<p>即便在單一函式，我們也用了來自兩個層的元素，這樣的設計不能稱為直觀</p>
<p>箭頭長度應要一致</p>
<p>常見做法是加入「中繼函式」</p>

---
transition: slide-up
level: 2
---

<h3>加入中繼函式後</h3>
<img v-click src="https://i.imgur.com/aMHAbyo.jpeg" />

<!-- NOTE: 在屏障中加入新函式，已移除跨越屏障的箭頭 -->

---
layout: two-cols
layoutClass: gap-16
---

<h3>擷取 remove_item_by_name() 中的 for loop</h3>

<p>Before</p>

```javascript{4-7}
function remove_item_by_name(cart, name) {
  var idx = null;

  // 逐一走訪陣列中的商品，找出指定商品的 index
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) idx = i;
  }

  if (idx !== null) return removeItems(cart, idx, 1);
  return cart;
}
```
<p>擷取 for loop 封裝程函式後，便能重複利用該函式</p>
<p>當具有良好的分層時，函式經常可以重複利用</p>

::right::

<br >
<br >
<p>After</p>

```javascript{2,6-13}
function remove_item_by_name(cart, name) {
  var idx = indexOfItem(cart, name);
  if (idx !== null) return removeItems(cart, idx, 1);
  return cart;
}

function indexOfItem(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name)
    return i;
  }
  return null;
}
```


<!-- NOTE: 該迴圈的用意是逐一走訪陣列中的商品，找出指定商品的 index
  故我們將新函式命名為 indexOfItem
 -->

---
transition: slide-up
level: 2
---

<h3>擷取後的呼叫圖</h3>
<p v-click>箭頭長度趨向一致</p>
<img v-after src="https://i.imgur.com/gAx50Qf.png" />
<br />

<!-- NOTE: 這個圖有點畫歪了，indexOfItem 和 removeItems 應該一樣高
 -->

---
transition: slide-up
level: 2
---

# 更多的練習-1

<p>isInCart() 和 indexOfItem() 有非常相似的程式碼</p>

```javascript
function isInCart(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return true;
  }
   return false;
}

function indexOfItem(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return i;
  }
  return null;
}
```
<p>1. 請用其中一個函式實作另一個</p>
<p>2. 畫出修改前、後的呼叫圖</p>

<!-- NOTE:  相同：找出特定的商品後，去做對應的操作-->

---
transition: slide-up
level: 2
---

### 答案
<p>isInCart() 的抽象層級較高，回傳布林值，它不需要知道任何資料結構</p>
<p>所以我們可以用 indexOfItem() 來實作 isInCart()</p>

```javascript
function isInCart(cart, name) {
  return indexOfItem(cart, name) !== null;
}
 
function indexOfItem(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return i;
  }
  return null;
}
```
<img width=500 src="https://i.imgur.com/vlgSJjM.png" />


---
transition: slide-up
level: 2
---

# 更多的練習-2

<p>setPriceByName() 裡有一個和 indexOfItem() 相似的 for loop</p>

```javascript{3-7,11-14}
function setPriceByName(cart, name, price) {
  var cartCopy = cart.slice();
  for (var i = 0; i < cartCopy.length; i++) {
    if (cartCopy[i].name === name) {
      cartCopy[i] = setPrice(cartCopy[i], price);
    }
  }
  return cartCopy;
}

function indexOfItem(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return i;
  }
  return null;
}
```
<p>1. 請用其中一個函式實作另一個</p>
<p>2. 畫出修改前、後的呼叫圖</p>

<!-- NOTE:
  setPriceByName 接受三個參數：

  cart：購物車陣列（陣列中的每個元素是一個商品物件）
  name：要修改的商品名稱
  price：新的價格

  使用 .slice() 對 cart 做 淺拷貝（寫入時複製），確保不會改到原始陣列 → 保持 不可變性 (immutability)
  迴圈的功能是：

  逐一走訪複製後的購物車
  如果找到商品名稱符合的項目，就用 setPrice() 更新該商品的價格
  setPrice(item, price) 應該是會建立新物件的函式（不直接修改舊的 item）
  回傳這個已經更新價格的購物車（新的陣列）
 -->
---
transition: slide-up
level: 2
---

### 答案
<p>setPriceByName() 的抽象層級較高，我們可以用 indexOfItem() 來實作 setPriceByName()</p>

```javascript
function setPriceByName(cart, name, price) {
  var cartCopy = cart.slice(b);
  var i = indexOfItem(cart, name);
  if (i !== null) {
    cartCopy[i] = setPrice(cartCopy[i], price);
  }
  return cartCopy;
}
 
function indexOfItem(cart, name) {
  for (var i = 0; i < cart.length; i++) {
    if (cart[i].name === name) return i;
  }
  return null;
}
```

---
transition: slide-up
level: 2
---

<img width=750 src="https://i.imgur.com/WAhXuUs.png" />
<p>一個長箭頭被短箭頭取代了</p>
<p>優化還沒結束，類似的修改還能繼續改下去</p>
<!-- NOTE: 呼叫圖似乎沒比較好，一個長箭頭被短箭頭取代了，類似的修改還能繼續改下去 -->


---
transition: slide-up
level: 2
---

# 更多的練習-3

<p>arraySet() 和 setPriceByName() 有需多相似的程式碼</p>

```javascript
function arraySet(array, idx, value) { // 以寫入時複製，將元素指定到傳入的 array index 位置上
  var copy = array.slice();
  copy[idx] = value;
  return copy;
}
 
function setPriceByName(cart, name, price) {
  var cartCopy = cart.slice(b);
  var i = indexOfItem(cart, name);
  if (i !== null) {
    cartCopy[i] = setPrice(cartCopy[i], price);
  }
  return cartCopy;
}
```
<p>1. 請用 arraySet() 實作 setPriceByName()</p>
<p>2. 畫出修改前、後的呼叫圖</p>


---
transition: slide-up
level: 2
---

### 答案

```javascript{all|10-11|all}
function arraySet(array, idx, value) { // 以寫入時複製，將元素指定到傳入的 array index 位置
  var copy = array.slice(); // cart copy
  copy[idx] = value;
  return copy;
}

function setPriceByName(cart, name, price) {
  var i = indexOfItem(cart, name);
  if (i !== null) {
    return arraySet(cart, i, setPrice(cart[i], price));
  }
  return cart;
}
```
<img width=680 src="https://i.imgur.com/1M6hbS6.png" />

<!-- NOTE:
  arraySet(array, idx, value) // 以寫入時複製，將元素指定到傳入的 array index 位置上
  有一個箭頭縮短了，指向了 arraySet，雖然指向的層數變多了，但重點是箭頭長度的縮短
  表示呼叫 setPriceByName 時我們能忽略更多細節
  但 setPriceByName 依然指向底層的 areay index，若覺得不直觀，請相信這種感覺
  就是藉由這感覺找出可以截取成通用函式的程式碼，
-->

---
transition: slide-up
level: 2
---

# 總結 - 原則 1 : 讓實作更直觀
<ul>
  <li v-click>直觀的實作具有相同的細節程度</li>
  <li v-click>處理某一層的函式時，不需要知道跟下層有關的資訊</li>
  <li v-click>函式簽章、函式本體、呼叫圖都能為「分層中的位置」提供線索</li>
  <li v-click>呼叫圖能提供大量與「細節程度」有關的線索</li>
  <li v-click>透過程式碼擷取建立更通用的函式，提升可重用性</li>
  <li v-click>函式的名稱反應其目的，我們可以把目的相關的函式放一起</li>
  <li v-click>當呼叫圖中存在長短不一的箭頭時，表示目前的實作不直觀</li>
  <li v-click>通用函式通常位於較低層，且能在程式中重複利用，變動性小，賦予上層穩定的基礎</li>
  <li v-click>越上層的函式通常變動性較高</li>
</ul>


---
layout: two-cols
layoutClass: gap-18
---

### 🔪 牛刀小試
<div v-click>
  <p>1. 以下哪一項不是分層設計的主要目標？</p>
  <p>A. 增加程式的重複使用性</p>
  <p>B. 降低每一層的耦合度</p>
  <p>C. 讓程式運行速度變得更快</p>
<p>D. 提高程式的可維護性與測試性</p>
</div>

<p style="color: red" v-click>答案：C</p>

<div v-click>
  <p>2. 以下哪一個最不建議直接放在上層函式中？</p>
  <p>A. 購物邏輯的條件判斷</p>
  <p>B. for 迴圈來找商品名稱</p>
  <p>C. 呼叫計算總價的函式</p>
  <p>D. 判斷是否免運費的邏輯</p>
</div>

<p style="color: red" v-click>答案：B</p>

::right::
<br>
<div v-click>
  <p>3. 以下哪一段程式碼最可能會產生較長的箭頭（呼叫距離）？</p>
  <a href="https://codepen.io/hangineer/pen/RNNvMvG" target="_blank">題目連結</a>
</div>
<p style="color: red" v-click>答案：getFinalPrice()</p>

<div v-click>
<p>4. 以下混用了不同層級的操作，請加入中繼函式，讓每層操作只處理對應層的邏輯</p>
<a href="https://codepen.io/hangineer/pen/RNNvMvG" target="_blank">題目連結</a>
</div>
<p style="color: red" v-click>答案：<a href="https://codepen.io/hangineer/pen/myyvxvO" target="_blank">連結（供參）</a></p>
<!-- NOTE:
  第一題答案：C
  第二題答案：B (for 迴圈屬於底層邏輯，應封裝)
  第三題答案：getFinalPrice()
  第四題答案：有連結可看
-->

---
transition: slide-up
level: 2
---

<p>5. 以下是某段購物邏輯的程式碼，假如你想重構這段程式，哪一個函式最適合作為被抽出來封裝的底層函式？</p>

```javascript
function applyShipping(cart) {
  let hasFreeShipping = false;
  for (let i = 0; i < cart.length; i++) {
    if (cart[i].price >= 1000) {
      hasFreeShipping = true;
    }
  }

  if (hasFreeShipping) {
    return setShippingCost(cart, 0);
  }
  return cart;
}
```
<div v-click>
  <p>A. applyShipping()</p>
  <p>B. setShippingCost()</p>
  <p>C. 判斷是否有高價商品的 for 迴圈邏輯</p>
  <p>D. 整段判斷邏輯從 hasFreeShipping 到 return</p>
</div>

<p style="color: red" v-click>答案：C</p>

<!-- NOTE: 第五題答案：C -->

---
## transition: fade-out
---

# 第 9 章 分層設計(2)

### 學習目標：

- 學習如何建立「抽象屏障」，以模組化程式碼
- 了解「分層設計」需要適可而止
- 了解「分層設計」能提高可維護性、可測試性、可重用性


---
transition: slide-up
level: 2
---

# 複習分層設計的原則

<!-- 後面會再次詳細提到 -->

<h3 class="mb-3" v-click>🔺原則 1：讓實作更直觀</h3>
<pre v-click class="mb-8">在直觀的程式中，元素的「細節程度」都差不多，若混雜不同的「細節程度」，則可視為有「程式碼異味」
🔹 程式碼異味：可能有潛在問題的程式碼特徵
🔹 本書中的「細節程度」和「抽象層級」可當同義詞看待，後面會再說明
</pre>
<h3 v-click>🔺原則 2：以抽象屏障輔助實作</h3>
<p v-click class="mb-10">可以把某些「層」(layers) 當成「屏障」 (也可叫介面 interface)，屏障可以隱藏實作細節，讓我們更專注在特定層的程式碼運作</p>

<h3 v-click>🔺原則 3：讓下層函式保持簡約不變</h3>
<p v-click class="mb-10">為了提高維護性，低層級（底層）的函式盡量越簡單越好，並用它來直接或間接定義上層函式</p>

<h3 v-click>🔺原則 4：分層只要舒適即可</h3>
<p v-click>勿過度追求完美</p>

<!-- NOTE:  本章節會討論原則 2 ~ 4-->

---
transition: slide-up
level: 2
---

# 原則 2：以抽象屏障(abstraction barrier)輔助實作
<p>情境：</p>

* 使用抽象屏障前
> 行銷主管 🤓：促銷活動的上線日期就快到了，程式怎麼還沒寫好呢？

> 開發小組的 Sarah 🥗：我們每週都要為新促銷寫程式，目前開發速度已經到極限，不能再等等嗎？

<br />

* 使用抽象屏障後

> 開發小組的 Sarah 🥗：促銷活動程式有順利上線嗎？

> 行銷主管 🤓：有的！自從使用了抽象屏障以後，行銷部門就能自己搞定促銷程式了

<!-- NOTE: 建立抽象屏障的好處之一是有助於團隊分工 -->

---
transition: slide-up
level: 2
---

# 抽象屏障可以隱藏實作細節

<p>抽象屏障(abstraction barrier)：指可以完美隱藏實作細節的函式層</p>
<p v-click>換句話說，當使用該層中的函式時，完全不需考慮下層如何運作，抽象屏障讓我們能從高階的觀點思考問題</p>

<img v-click width=580 height=300 src="https://i.imgur.com/nSQSgN8.jpeg" />

<!-- NOTE: 以前面促銷活動為例，行銷人員只會用到與促銷活動直接相關的函式（位於抽象屏障以上），而不需面對迴圈、陣列 -->

---
transition: slide-up
level: 2
---

# 細節忽略是雙向的

<p>抽象屏障讓行銷部門得以忽略底層程式細節，讓開發小組也不必頻繁的改行銷部門的程式 🎊</p>
<div v-click>
  <p>現實中</p>
  <p>像是利用第三方提供的 API 開發應用程式時</p>
  <p>只需思考如何撰寫應用程式就好，不必管 API 的實作</p>
  <p>同理，第三方也只負責 API 的開發，不必了解應用程式的程式碼</p>
</div>
<p v-click>處的 API 實際上就是抽象屏障，區隔了我們和第三方所負責的工作</p>

<!-- NOTE: 講完第一句後：行銷部門和開發小組的工作可以完全獨立
講完現實中...：此處的 API 實際上就是抽象屏障，區隔了我們和第三方所負責的工作
 -->


---
transition: slide-up
level: 2
---

# 更改「購物車」的資料結構
<p>情境：</p>

> 開發小組的 Sarah 🥗：陣列的線性搜尋太沒效率了，我們應該改用支援快速搜尋的資料結構

<br />

> 開發小組的 Sarah 🥗：hash map 是個好解方，不如改成類似的資料結構 「物件」吧！

<p v-click>購物車的資料結構改變後，以下哪些函式要改寫</p>
<a v-click href="https://codepen.io/hangineer/pen/azzPPqB" target="_blank">查看完整函式程式碼</a>
<img v-after width=780 src="https://i.imgur.com/A4wU3HU.jpeg" />


---
transition: slide-up
level: 2
---

### 答案
<p v-click>只有下圖灰色圖層中的函式要改寫</p>
<img v-after width=850 src="https://i.imgur.com/XZrNEjs.jpeg" />
<!-- NOTE: 念完 只有下圖灰色圖層中的函式要改寫：
  就是這些函式定義了購物車操作，其他函式皆為假定購物車為陣列
-->

---
layout: two-cols
layoutClass: gap-16
---

## 將「購物車」重新實作為物件

<p>購物車為「陣列」</p>

```javascript{2,7,15-21}
function add_item(cart, item) {
  return add_element_last(cart, item);
}

function calc_total(cart) {
  var total = 0;
  for (var i = 0; i < cart.length; i++) {
    var item = cart[i];
    total += item.price;
  }
  return total;
}

function setPriceByName(cart, name, price) {
  var cartCopy = cart.slice();
  for (var i = 0; i < cartCopy.length; i++) {
    if (cartCopy[i].name === name) {
      cartCopy[i] = setPrice(cartCopy[i], price);
    }
  }
  return cartCopy;
}
```

::right::
<br />
<p>購物車為「物件」</p>

```javascript{2,7-9,16-23}
function add_item(cart, item) {
  return objectSet(cart, item.name, item);
}

function calc_total(cart) {
  var total = 0;
  var names = Object.keys(cart);
  for (var i = 0; i < names.length; i++) {
    var item = cart[names[i]];
    total += item.price;
  }
  return total;
}

function setPriceByName(cart, name, price) {
  if (isInCart(cart, name)) {
    var item = cart[name];
    var copy = setPrice(item, price);
    return objectSet(cart, name, copy);
  } else {
    var item = make_item(name, price);
    return objectSet(cart, name, item);
  }
}
```
<!-- NOTE: 把陣列有關的部分拿掉，改為物件 -->

---
layout: two-cols
layoutClass: gap-16
---

## 將「購物車」重新實作為物件

<p>購物車為「陣列」</p>

```javascript{2-5,7-12,15}
function remove_item_by_name(cart, name) {
  var idx = indexOfItem(cart, name);
  if(idx !== null) return splice(cart, idx, 1);
  return cart;
}

function indexOfItem(cart, name) {
  for(var i = 0; i < cart.length; i++) {
    if(cart[i].name === name) return i;
  }
  return null;
}

function isInCart(cart, name) {
  return indexOfItem(cart, name) !== null;
}
```
<p v-click>有時程式碼不清楚，是因為用了錯誤的資料結構</p>
::right::
<br />
<p>購物車為「物件」</p>

```javascript{2,5,8}
function remove_item_by_name(cart, name) {
  return objectDelete(cart, name);
}

// indexOfItem 不需要了，故移除

function isInCart(cart, name) {
  return cart.hasOwnProperty(name);
}
```

---
transition: slide-up
level: 2
---

# 抽象屏障讓我們能夠忽略細節
<p v-click>為何更改購物車的資料結構以後，不必再修改所有使用該資料的程式碼？</p>
<p v-click>關鍵在於定義了「抽象屏障」，此處的抽象就等於問自己「哪項實作細節可以忽略？」</p>

<p v-after>當某一分層被稱為抽象屏障時，就是在說「此屏障中的函式已為我們處理了某些細節，
使用該層以上的函式時，就不需再顧慮該細節資訊</p>

<img v-click width=760 src="https://i.imgur.com/mwHIQW2.jpeg" />

<!-- NOTE: 只要利用屏障中的函式來撰寫上層函式，購物車的具體實作就變成可忽略的細節
  因此就算把資料結構從陣列變成物件，也不會影響到抽象屏障以上的程式
-->


---
transition: slide-up
level: 2
---

# 何時該用或不該用抽象屏障？
<p>使用抽象屏障的原則</p>

<h3 v-click>1. 抽象屏障應讓實作修改更容易</h3>
<p v-click>當不確定如何實作某項功能時，抽象屏障可提供間接層，允許稍後再來更改實作方式</p>
<p v-click>當已經確定未來要進行某項行動，只是現階段尚未準備好，例如：資料最終會從伺服器而來，但目前暫用假資料代替
</p>

<h3 v-click>2. 抽象屏障應讓程式更易讀、易撰寫</h3>
<p v-click>某些實作細節正是程式中容易出錯的地方，抽象屏障讓我們得以忽略這些細節，讓開發更輕鬆，只要被隱藏的細節是正確的</p>

<h3 v-click>3. 抽象屏障應降低不同部門的協調頻率</h3>
<p v-click>本章例子中，提升了開發人員和行銷人員的工作效率</p>

<h3 v-click>4.抽象屏障應允許開發者專注於特定問題</h3>
<p v-click>細節被忽略了，讓開發者可以把時間花在更重要的事情上</p>
<!-- NOTE:
  1. 抽象屏障應讓實作修改更容易，念完 允許稍後再來更改實作方式：另一個抽象屏障能派上用場的情況是...
  3. 抽象屏障應降低不同部門的協調頻率：在這章的例子裡，開發小組改動程式碼時不需告知行銷部門，
    行銷部門也不需再依賴開發小組，抽象屏障讓兩邊的開發人員不需顧及對方負責的細節
  4.
-->
<!--  -->

---
transition: slide-up
level: 2
---

# 總結 - 原則 2 : 以抽象屏障輔助實作
<ul>
  <li v-click>使用抽象屏障上方函式的人，不需顧及程式實作細節（例如：資料結構）</li>
  <li v-click>屏障中或屏障以下的程式，可忽略比自己上層的函式用途，所寫的函式可被用在任何地方</li>
  <li v-click>「抽象化」的運作方式：定義了抽象屏障上方、下方的函式可忽略哪些資訊</li>
  <li v-click>抽象屏障的用意是，降低跨部門的溝通時間、讓複雜的程式碼更易懂、協助相關人員專注於眼前的問題</li>
  <li v-click>要建立有用的抽象屏障，可問自己以下幾個問題：
    <ul>
      <li v-click>哪裡有細節可以忽略？具體資訊是什麼？</li>
      <li v-click>是否可以寫一組函式來封裝選定的細節？</li>
    </ul>
  </li>
</ul>


---
transition: slide-up
level: 2
---

# 程式變得更清楚了

<p>修改後的程式碼也更加符合原則一：讓實作更直觀</p>
<p>變更購物車的資料結構以後，很多函式實作只剩下一行，但重點並非行數，而是「細節程度的一致性」</p>

```javascript
function add_item(cart, item) {
  return objectSet(cart, item.name, item);
}

function gets_free_shipping(cart) {
  return calc_total(cart) >= 20;
}

function cartTax(cart) {
  return calc_tax(calc_total(cart));
}

function remove_item_by_name(cart, name) {
  return objectDelete(cart, name);
}

function isInCart(cart, name) {
  return cart.hasOwnProperty(name);
}
```
<!-- NOTE: 因單行程式沒有空間容納細節程度不一的程式，因此屬於好的設計 -->

---
transition: slide-up
level: 2
---

<p>以下函式的實作仍有改進空間，還無法簡化成一行，相關內容要到 10 ~ 11 章時才會講解</p>

```javascript
function calc_total(cart) {
  var total = 0;
  var names = Object.keys(cart);
  for(var i = 0; i < names.length; i++) {
    var item = cart[names[i]];
    total += item.price;
  }
  return total;
}

function setPriceByName(cart, name, price) {
  if(isInCart(cart, name)) {
    var itemCopy = objectSet(cart[name], 'price', price);
    return objectSet(cart, name, itemCopy);
  } else {
    return objectSet(cart, name, make_item(name, price));
  }
}
```


---
transition: slide-up
level: 2
---

# 原則 3：讓下層函式保持簡約不變
<p>情境：</p>
<p>MegaMart 行銷部門正在策劃新的促銷活動</p>
<p>想為手錶提供折扣優惠，只要購物車內的商品總額超過某個值且其中包含手錶，便能獲得 10% 優惠</p>
<img width=440 src="https://i.imgur.com/TpDeQ2q.jpeg" />

---
transition: slide-up
level: 2
---

# 新促銷函式的兩個可能位置
<p>為新功能撰寫函式時，須考慮其在分層架構中的位置，避免底層被不必要的功能塞滿</p>

<img src="https://i.imgur.com/tmJjd2B.jpeg" />


---
transition: slide-up
level: 2
---

### 選擇1: 抽象屏障中

```javascript
function getsWatchDiscount(cart) {
  var total = 0;
  var names = Object.keys(cart);
  for (var i = 0; i < names.length; i++) {
    var item = cart[names[i]];
    total += item.price;
  }
  return total > 100 && cart.hasOwnProperty("watch");
}
```

<br />

### 選擇2: 高於抽象屏障

```javascript
function getsWatchDiscount(cart) {
  var total = calcTotal(cart);
  var hasWatch = isInCart("watch");
  return total > 100 && hasWatch;
}
```
<p>想想看，上面兩個位置哪個較合適？為什麼？</p>


---
transition: slide-up
level: 2
---
### 答案

<p>選擇2: 高於抽象屏障比較好</p>

```javascript
function getsWatchDiscount(cart) {
  var total = calcTotal(cart);
  var hasWatch = isInCart("watch");
  return total > 100 && hasWatch;
}
```
<p v-click>為什麼？</p>
<p v-click>首先，選擇 1 違背了屏障存在的目的<br>定義抽象屏障在此是為了讓行銷部門可忽略底層程式細節<br>但卻出現了 for loop</p>
<p v-click>再來，根據原則 3：讓下層函式保持簡約不變<br>我們盡可能將新功能加到高層，避免擴充和修改較低的函式層</p>
<p v-click>最後，選擇 2 的程式碼使用了下層的函式元素，讓程式更直觀</p>


---
transition: slide-up
level: 2
---

# 記錄消費者將哪些商品加到購物車

> 行銷部門發現：消費者有時會將商品加入購物車，但最後卻沒有結帳

> 為了提高銷售率，行銷人員需要更多資訊，所以要求開發小組撰寫新程式，以記錄消費者加入購物車的每項商品

```javascript
// 預期的呼叫方式
logAddToCart(user_id, item)
```

<p>現在只要將 logAddToCart() 放在程式某處就行了，開發小組的吉娜建議加到 add_item() 中，如下：</p>

```javascript
function add_item(cart, item) {
  logAddToCart(global_user_id, item);
  return objectSet(cart, item.name, item);
}

// 對購物車進行寫入時複製
function objectSet(object, key, value) {
  var copy = Object.assign({}, object);
  copy[key] = value;
  return copy;
}
```
<p>不過，這是最佳的位置嗎？擺在這的好處、壞處有什麼？</p>


---
transition: slide-up
level: 2
---

# 函式位置與其影響

<p>我們的目標是，當消費者將商品加到購物車時，便將該商品記錄在資料庫中</p>
<p v-click>add_item() 是在購物車添加新物件，改成這樣後，便負起記錄的功能</p>
<p v-click>在撰寫高於 add_item() 所在層的函式時，就可忽略這項細節（意即不知道要記錄也沒關係，資料仍會被寫入）</p>

<br>
<h3 v-click>但在 add_item() 中執行 logAddToCart() 會有幾個缺點！！</h3>
<p v-click>1. logAddToCart() 是 Action 函式， 呼叫它，add_item() 也會變成 Action 函式</p>
<p v-click>2. 所有呼叫到 add_item() 的函式也會變成 Action 函式</p>
<p v-click>3. add_item() 原本是 calculation 函式，若變成 Action ，可能會出錯</p>

<p v-click>logAddToCart 的呼叫應發生在<b>「抽象屏障上方」</b>更合適</p>


---
transition: slide-up
level: 2
---
# 更理想的紀錄函式位置
<p>add_item_to_cart() 是點擊「Buy Now」按鈕會觸發的函式，本身已是 Action，所以放在這更佳合適</p>

```javascript
function add_item_to_cart(name, price) {
  var item = make_cart_item(name, price);

  shopping_cart = add_item(shopping_cart, item);
  var total = calc_total(shopping_cart);

  set_cart_total_dom(total);
  update_shipping_icons(shopping_cart);
  update_tax_dom(total);
  logAddToCart(); // 加在此，在商品加入購物車後，必須執行的動作之一
}
```
<p>以上位置並非最佳解，但卻是當前設計下最正確的選擇。若要找更好的答案，則需要改變整個程式的架構</p>

---
transition: slide-up
level: 2
---

# 總結 - 原則 3：讓下層函式保持簡約不變
<ul>
  <li v-click>原則 3 適用於所有層，而非只有抽象屏障</li>
  <li v-click>在屏障中加入的函式越少，未來更改時要變更的東西也越少</li>
  <li v-click>抽象屏障內的函式屬於與程式的「底層運作」較有關的函式，簡化此層中的函式能讓除錯更容易</li>
  <li v-click>當要在程式中加入新功能時，盡可能在更高的層級實現。 應利用下層提供的函式作為基礎，避免直接修改下層函式</li>
  <li v-click>抽象屏障內的函式越少，使用者更容易記得其中有哪些操作可用</li>
   <li v-click>在理想情況下每個程式層級，應只包含必須的函式，且無需頻繁進行修改或添加新函式，以保持每層的簡潔性</li>
</ul>


---
transition: slide-up
level: 2
---

# 原則 4：分層只要舒適即可

<p>層數越多可能造成設計越困難</p>
<p v-click>當時間久了，回頭檢視先前建立的抽象屏障時，往往會發現，有些層其實沒什麼用處</p>

<p v-click>那怎樣的程式算是足夠好呢？</p>
<p v-click>你可以問問自己...</p>
<p v-click>當前的程式有沒有任何讓人不舒服的地方? 例如：某一個函式層包含太多細節、實作太過雜亂</p>
<p v-click>若答案為「否」，即便程式碼中有未擷取的迴圈或呼叫圖上有過長的箭頭，也並不一定要再優化設計</p>
<p v-click>若答案為「是」，可使用原則 1-3 繼續優化</p>

<p v-click>總之，沒有程式是完美的！在追加新功能時又希望設計維持簡約，請用上述方法做為指引，評估是否有改進必要，若當前程式已滿足需求，就該適可而止</p>

---
transition: slide-up
level: 2
---

# 呼叫圖呈現了哪些與程式有關的資訊
<img width=450 src="https://i.imgur.com/uYPERXI.jpeg" />
<p>呼叫圖的結構取決於函式之間的呼叫關係，也訴說了三項「非功能性的需求」(nonfunctional requirements, NFRs)</p>
<p v-click>1.可維護性(maintainability)：當需求改變時，哪些函式修改起來較安全?</p>
<p v-click>2.可測試性(testability)：哪些函式需要重點測試?</p>
<p v-click>3.可重複使用性(reusability)：哪些函式較容易重複使用?</p>
<p v-click>以上三則的英文都是以 ility 結尾，故也被合稱為「ilities」</p>

<!-- NOTE: 非功能性需求則與可維護性 、可測試性、可重複使用性 有關，這也是程式需要設計的原因。 -->

---
transition: slide-up
level: 2
---

# 修改呼叫圖上層的程式較安全
<img width=500 src="https://i.imgur.com/pNbS88b.jpeg" />

Do’s  將會頻繁改變的函式擺在上層<br>
Don’t  請避免用可能改變的函式去定義其他函式<br>
分層設計原則 1、 3 (讓實作更直觀、讓下層函式保持簡約不變)，相當於依未來修改程式碼的可能性來分層
<!-- NOTE:
最頂層的函式由於沒有其他程式呼叫該函式，故修改時不需考慮太多

最底層的函式情況則與頂層相反。呼叫圖上的三層實作皆依賴於此層

如果底層函式的行為改了，影響將一路延伸到最上層

Don’t 把隨時接改變的程式碼擺在呼叫圖下層

Do’s 這就是為什麼寫入實複製的函式位子那麼下面的原因
 -->

---
transition: slide-up
level: 2
---

# 測試底層函式較重要
<img width=500 src="https://i.imgur.com/mrVJK9n.jpeg" />
<p>絕大多數的程式碼有賴於底層函式的運作正確，如果測試的時間有限，可把重心放在底層程式</p>

---
transition: slide-up
level: 2
---

# 底層函式較能重複利用
<img width=500 src="https://i.imgur.com/D5bPS28.jpeg" />
<p>越下層函式的可重複使用性越好</p>

---
transition: slide-up
level: 2
---

# 總結：呼叫圖告訴我們的訊息
<br>
<p v-click>▫️可維護性：一個函式與上層連接的箭頭越少，修改起來越安全</p>
<p v-after>實務建議：將經常改變的程式碼放到上層去</p>


<p v-click>▫️可測試性：一個函式與上層連接的箭頭越多，測試價值越高</p>
<p v-after>實務建議：把測試重點放在底層函式</p>


<p v-click>▫️可重複使用性：一個函式下方的其他函式越少，可重複使用性越高</p>
<p v-after>實務建議：將函式的程式碼擷取成底層函式，增加重複使用性</p>



<!-- ::right:: -->
<!-- <br>
<img width=300 src="https://i.imgur.com/vEpEJ6e.jpeg" />
<br>
<img width=300 src="https://i.imgur.com/vEpEJ6e.jpeg" />
<br>
<img width=300 src="https://i.imgur.com/3OnQtrz.jpeg" /> -->
<!-- NOTE:
  圖一：A 比 Ｂ 在修改上更安全，C 是影響程度最小的
  圖二：Ｂ 的測試價值比 A 高
  圖三：A 和 Ｂ 的重複使用性差不多，C 的重複使用性最差
-->

---
transition: slide-up
level: 2
---

### 🔪 牛刀小試
<div v-click>
  <p>1. 關於抽象屏障（abstraction barrier）哪個敘述是正確的？</p>
  <p>A. 抽象屏障是用來限制程式不能跨層呼叫函式的機制</p>
  <p>B. 抽象屏障會降低函式的呼叫效率，僅適合用於大型系統</p>
  <p>C. 抽象屏障能幫助開發者與非工程部門各自專注自己的職責</p>
  <p>D. 抽象屏障內部的所有函式都必須是純函式（pure function）</p>
</div>

<p style="color: red" v-click>答案：C</p>


<div v-click>
  <p>2. 根據呼叫圖的原則，以下哪一層函式的測試價值最高？</p>
  <p>A. 一個只被一個函式呼叫的頂層函式</p>
  <p>B. 一個同時被多個中層函式呼叫的底層工具函式</p>
  <p>C. 一個位於抽象屏障上方、主要處理 DOM 操作的函式</p>
  <p>D. 一個只處理例外錯誤的 try-catch 包裝函式</p>
</div>
<p style="color: red" v-click>答案：B</p>
<!-- NOTE:
  第一題：答案：C，抽象屏障的目的是「隱藏細節，提升溝通與維護效率」，能讓不同部門專注各自責任範圍
  第二題：答案：B，被多個函式依賴的底層函式，一旦出錯會波及整體系統，因此測試價值最高
-->

---
layout: two-cols
layoutClass: gap-16
---

### 🔪 牛刀小試
<div v-click>
  <p>3. 下列哪一個情況最容易導致呼叫圖上的箭頭變亂或變長？</p>
  <p>A. 每一層函式都只呼叫下一層</p>
  <p>B. 為了簡化程式碼，把所有邏輯合併進同一層</p>
  <p>C. 把所有通用函式集中到一個 utilities 層中</p>
  <p>D. 把原本位於上層的 UI 控制邏輯下放到底層</p>
</div>

<p style="color: red" v-click>答案：D</p>


<div v-click>
  <p>4. 以可測試性來說，哪個函式的效益最大</p>
  <p>A. A()</p>
  <p>B. B()</p>
  <p>C. C()</p>
</div>

<p style="color: red" v-click>答案：B</p>
<!-- NOTE:
  第三題：答案：D，當 UI 控制邏輯下放到底層，會導致底層反而依賴上層的邏輯，破壞單向箭頭原則，造成「呼叫反轉」
  第四題：答案：B，B 的可次是性比 A 高
-->
::right::
<br><br><br><br><br><br><br><br><br><br><br><br><br><br>
<img width=300 src="https://i.imgur.com/vEpEJ6e.jpeg" />


---
transition: slide-up
level: 2
---

### 🔪 牛刀小試
<div v-click>
  <p>5. 哪個函式修改起來比較安全</p>
  <p>A. A()</p>
  <p>B. B()</p>
  <p>C. C()</p>
</div>
<img v-after width=300 src="https://i.imgur.com/vEpEJ6e.jpeg" />
<p style="color: red" v-click>答案：A</p>

<!-- NOTE:
  第五題：答案：A，A 比 Ｂ 在修改上更安全，C 是影響程度最小的
-->


---
transition: slide-up
level: 2
---

# 感謝各位聆聽

第九章是第一部分（徹底學通 Actions、Calculations、Data）的尾聲\
下一章就要延續本章節，學習如何真正將迴圈「抽象化」

<br />

## 下次讀書會預告: CH10 ~ CH11 頭等函式(1)(2)

* 日期：2025/05/29
* 導讀人：Monica
* 筆記工：Mi
