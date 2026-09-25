from pathlib import Path
import zipfile, shutil, textwrap

root = Path("/mnt/data/korva_website")
if root.exists():
    shutil.rmtree(root)
(root / "assets").mkdir(parents=True)

# Reuse the generated KORVA logo that is already available in the conversation runtime.
logo_src = Path("/mnt/data/a_clean_minimalist_brand_identity_logo_design_on.png")
if logo_src.exists():
    shutil.copy2(logo_src, root / "assets" / "korva-logo.png")

index_html = r'''<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="KORVA — contemporary lighting for considered spaces.">
  <title>KORVA — FORM. LIGHT. SPACE.</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@400;500;600&family=Manrope:wght@400;500;600;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div id="app"></div>
  <script src="script.js"></script>
</body>
</html>
'''

styles_css = r''':root{
  --obsidian:#171717;
  --ivory:#f5f1e8;
  --champagne:#c9a86a;
  --white:#fff;
  --muted:#77736c;
  --line:#ddd8ce;
  --soft:#ece8df;
  --max:1240px;
}
*{box-sizing:border-box}
html{scroll-behavior:smooth}
body{margin:0;background:var(--ivory);color:var(--obsidian);font-family:Manrope,Arial,sans-serif}
a{text-decoration:none;color:inherit}
button,input,select,textarea{font:inherit}
button{cursor:pointer}
img{max-width:100%;display:block}
.container{width:min(var(--max),calc(100% - 40px));margin:auto}
.topbar{background:var(--obsidian);color:#fff;padding:9px 20px;text-align:center;font-size:11px;letter-spacing:.14em;text-transform:uppercase}
.nav{position:sticky;top:0;z-index:50;background:rgba(245,241,232,.96);backdrop-filter:blur(10px);border-bottom:1px solid var(--line)}
.nav-inner{height:78px;display:flex;align-items:center;justify-content:space-between;gap:25px}
.logo{font-weight:600;letter-spacing:.36em;font-size:22px;margin-left:.36em}
.logo-wrap{display:flex;align-items:center;gap:12px}
.logo-mark{width:34px;height:34px;border:1px solid var(--champagne);display:grid;place-items:center;color:var(--champagne);font-family:Georgia,serif;font-size:18px}
.nav-links{display:flex;gap:26px;font-size:12px;text-transform:uppercase;letter-spacing:.12em}
.nav-actions{display:flex;gap:10px;align-items:center}
.icon-btn{border:0;background:transparent;width:38px;height:38px;position:relative}
.badge{position:absolute;right:3px;top:1px;background:var(--champagne);color:var(--obsidian);border-radius:50%;width:17px;height:17px;font-size:9px;display:grid;place-items:center}
.hero{min-height:650px;display:grid;place-items:center;text-align:center;position:relative;overflow:hidden;background:
linear-gradient(90deg,rgba(23,23,23,.72),rgba(23,23,23,.18)),
linear-gradient(135deg,#30302d 0%,#817d72 45%,#e2d8c8 100%)}
.hero:before{content:"";position:absolute;inset:0;background:
radial-gradient(circle at 65% 38%,rgba(255,230,164,.75),transparent 14%),
radial-gradient(circle at 68% 39%,rgba(255,255,255,.25),transparent 27%)}
.hero-content{position:relative;color:#fff;max-width:850px;padding:80px 20px}
.eyebrow{font-size:11px;letter-spacing:.28em;text-transform:uppercase;color:var(--champagne);margin-bottom:16px}
h1,h2,h3{font-family:"Cormorant Garamond",Georgia,serif;font-weight:500;margin:0}
.hero h1{font-size:clamp(54px,8vw,108px);letter-spacing:.03em;line-height:.88}
.hero p{max-width:680px;margin:28px auto;color:#f4f1eb;font-size:15px;line-height:1.8}
.btn{display:inline-flex;align-items:center;justify-content:center;gap:8px;border:1px solid var(--obsidian);padding:13px 24px;background:var(--obsidian);color:#fff;text-transform:uppercase;letter-spacing:.14em;font-size:10px}
.btn.light{background:transparent;border-color:#fff;color:#fff}
.btn.gold{background:var(--champagne);border-color:var(--champagne);color:var(--obsidian)}
.section{padding:86px 0}
.section-head{text-align:center;margin-bottom:42px}
.section-head h2{font-size:52px}
.section-head p{color:var(--muted);max-width:650px;margin:12px auto;line-height:1.7;font-size:13px}
.grid{display:grid;gap:20px}
.cat-grid{grid-template-columns:repeat(4,1fr)}
.space-grid{grid-template-columns:repeat(3,1fr)}
.product-grid{grid-template-columns:repeat(4,1fr)}
.card{background:#fff;border:1px solid #e5e0d7;overflow:hidden}
.card-image{aspect-ratio:1/1.08;background:linear-gradient(135deg,#dad5ca,#9e9788);position:relative;overflow:hidden}
.card-image:after{content:"";position:absolute;width:46%;height:46%;left:27%;top:27%;border-radius:50%;background:radial-gradient(circle,#fff4c9 0%,#d0b777 25%,#6e6659 65%,transparent 67%);filter:blur(1px);opacity:.82}
.card-image.tall{aspect-ratio:1/1.3}
.card-body{padding:17px}
.card-body h3{font-size:25px}
.card-body p{font-size:11px;color:var(--muted);margin:6px 0}
.price{font-size:13px;font-weight:700;margin-top:12px}
.card-actions{display:flex;justify-content:space-between;align-items:center;margin-top:15px}
.small-btn{border:1px solid var(--line);background:var(--ivory);padding:9px 11px;font-size:9px;text-transform:uppercase;letter-spacing:.12em}
.image-label{position:absolute;left:12px;top:12px;background:rgba(255,255,255,.88);padding:7px 9px;font-size:9px;letter-spacing:.12em;text-transform:uppercase;z-index:2}
.split{display:grid;grid-template-columns:1fr 1fr;min-height:540px}
.split-media{background:linear-gradient(135deg,#2b2a27,#8e8779 60%,#eee4d4);position:relative}
.split-media:after{content:"";position:absolute;inset:20% 23%;border:1px solid rgba(255,255,255,.5);box-shadow:0 0 90px rgba(255,224,157,.65)}
.split-copy{padding:80px;background:#fff}
.split-copy h2{font-size:58px;line-height:.95}
.split-copy p{line-height:1.9;color:var(--muted);font-size:13px;max-width:500px}
.pill-row{display:flex;gap:8px;flex-wrap:wrap;justify-content:center}
.pill{border:1px solid var(--line);padding:10px 15px;font-size:10px;text-transform:uppercase;letter-spacing:.1em;background:#fff}
.quote{background:var(--obsidian);color:#fff;text-align:center;padding:80px 20px}
.quote blockquote{font-family:"Cormorant Garamond",serif;font-size:42px;max-width:900px;margin:0 auto 20px;line-height:1.05}
.quote small{color:#bbb;letter-spacing:.16em;text-transform:uppercase}
.footer{background:#111;color:#fff;padding:60px 0 25px}
.footer-grid{display:grid;grid-template-columns:2fr 1fr 1fr 1fr;gap:40px}
.footer h3{font-size:28px;margin-bottom:16px}
.footer p,.footer a{font-size:11px;color:#b9b5ad;line-height:2}
.footer-bottom{border-top:1px solid #333;margin-top:45px;padding-top:20px;font-size:10px;color:#777;display:flex;justify-content:space-between}
.page-head{padding:72px 0 30px}
.page-head h1{font-size:70px}
.breadcrumb{font-size:10px;letter-spacing:.1em;text-transform:uppercase;color:var(--muted);margin-bottom:16px}
.filters{display:flex;justify-content:space-between;align-items:center;gap:15px;margin:20px 0 30px;padding:14px 0;border-top:1px solid var(--line);border-bottom:1px solid var(--line)}
.filter-group{display:flex;gap:8px;flex-wrap:wrap}
.detail{display:grid;grid-template-columns:1.1fr .9fr;gap:55px;padding:50px 0 90px}
.gallery-main{aspect-ratio:1/1;background:linear-gradient(135deg,#d7d1c6,#81796b);position:relative}
.gallery-main:after{content:"";position:absolute;inset:20%;border-radius:50%;background:radial-gradient(circle,#fff7cf,#c5a35c 18%,#5e574c 55%,transparent 57%);box-shadow:0 0 80px rgba(255,224,147,.65)}
.thumbs{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;margin-top:10px}
.thumb{aspect-ratio:1;background:linear-gradient(135deg,#d9d3c8,#999184);border:1px solid transparent}
.detail-copy h1{font-size:65px;line-height:.95}
.detail-copy .price{font-size:22px;margin:22px 0}
.specs{border-top:1px solid var(--line);margin-top:25px}
.spec{display:flex;justify-content:space-between;padding:13px 0;border-bottom:1px solid var(--line);font-size:11px}
.quantity{display:flex;align-items:center;border:1px solid var(--line);width:max-content;margin:15px 0}
.quantity button{border:0;background:transparent;width:38px;height:38px}
.quantity span{width:40px;text-align:center;font-size:12px}
.info-layout{display:grid;grid-template-columns:250px 1fr;gap:50px;padding-bottom:80px}
.info-nav{position:sticky;top:105px;align-self:start;border-top:1px solid var(--line)}
.info-nav button{display:block;width:100%;padding:13px 0;text-align:left;background:none;border:0;border-bottom:1px solid var(--line);font-size:10px;text-transform:uppercase;letter-spacing:.1em}
.info-content{background:#fff;padding:45px;min-height:500px}
.info-content h2{font-size:48px}
.info-content p,.info-content li{font-size:13px;line-height:1.9;color:#5f5b54}
.form-grid{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.field{padding:13px;border:1px solid var(--line);background:#fff;width:100%}
.field.full{grid-column:1/-1}
.notice{padding:14px;background:#fff8e7;border-left:3px solid var(--champagne);font-size:11px;line-height:1.6}
.cart-row{display:grid;grid-template-columns:100px 1fr auto auto;gap:18px;align-items:center;border-bottom:1px solid var(--line);padding:18px 0}
.cart-img{width:100px;height:100px;background:linear-gradient(135deg,#d7d1c6,#82796a)}
.total-box{background:#fff;padding:25px;margin-top:25px;max-width:420px;margin-left:auto}
.total-line{display:flex;justify-content:space-between;padding:9px 0;font-size:12px}
.total-line.grand{border-top:1px solid var(--line);margin-top:8px;padding-top:18px;font-weight:700;font-size:16px}
@media(max-width:900px){
  .nav-links{display:none}
  .cat-grid,.product-grid{grid-template-columns:repeat(2,1fr)}
  .space-grid{grid-template-columns:1fr}
  .split,.detail,.info-layout{grid-template-columns:1fr}
  .info-nav{position:static}
  .footer-grid{grid-template-columns:1fr 1fr}
}
@media(max-width:560px){
  .container{width:min(100% - 24px,var(--max))}
  .nav-inner{height:65px}
  .logo{font-size:18px}
  .cat-grid,.product-grid,.form-grid{grid-template-columns:1fr}
  .section{padding:60px 0}
  .section-head h2,.detail-copy h1,.page-head h1{font-size:48px}
  .hero{min-height:600px}
  .split-copy{padding:50px 24px}
  .footer-grid{grid-template-columns:1fr}
  .footer-bottom{display:block}
}
'''

script_js = r'''const products = [
  {id:"KV-ARC-01-BG", name:"ARCO Pendant", category:"Pendant", price:7490, old:8990, space:"Living Room", desc:"A sculptural pendant designed to create a warm architectural focal point."},
  {id:"KV-VET-02-BG", name:"VETRA Wall Light", category:"Wall Light", price:4290, old:4990, space:"Bedroom", desc:"A refined wall light with a soft, directional glow."},
  {id:"KV-NOM-03-BG", name:"NOMA Chandelier", category:"Chandelier", price:14990, old:17990, space:"Dining Room", desc:"A contemporary chandelier designed around proportion and atmosphere."},
  {id:"KV-VEL-04-BG", name:"VELA Table Lamp", category:"Table Lamp", price:5490, old:6490, space:"Study Room", desc:"A compact statement lamp for desks, consoles and bedside tables."},
  {id:"KV-ARC-05-BK", name:"ARCH Ceiling Light", category:"Ceiling Light", price:5990, old:6990, space:"Living Room", desc:"Clean architectural form with warm ambient illumination."},
  {id:"KV-FRM-06-BG", name:"FORM Pendant", category:"Pendant", price:8990, old:9990, space:"Kitchen", desc:"Minimal geometry with a warm, focused pool of light."},
  {id:"KV-ELM-07-BG", name:"ELEMENT Wall Light", category:"Wall Light", price:3890, old:4490, space:"Dining Room", desc:"A soft sculptural accent for walls and corridors."},
  {id:"KV-SGN-08-BG", name:"SIGNATURE Chandelier", category:"Chandelier", price:24990, old:28990, space:"Dining Room", desc:"A statement centrepiece for larger residential and hospitality spaces."}
];

const infoPages = {
  "returns-exchanges": ["Returns & Exchanges","Read the return, replacement and exchange process before placing an order."],
  "track-order": ["Track Order","Enter your order reference and registered phone number to view the latest delivery status."],
  "about": ["About KORVA","KORVA creates contemporary lighting around the relationship between form, light and space."],
  "warranty": ["Warranty Policy","Warranty coverage, exclusions, installation conditions and service process will be published here for every product."],
  "service": ["Service Policy","Our service model covers installation support, troubleshooting, replacement components and after-sales assistance."],
  "terms": ["Terms & Conditions","These terms govern website use, orders, payments, delivery, cancellations and customer responsibilities."],
  "blog": ["Journal","Lighting guides, room inspiration, product stories and practical advice for considered spaces."],
  "installation": ["Installation Policy","Professional installation is offered in serviceable locations. Product-specific installation requirements will be shown on each product page."]
};

let cart = JSON.parse(localStorage.getItem("korvaCart") || "[]");
let wishlist = JSON.parse(localStorage.getItem("korvaWishlist") || "[]");

function money(n){return "₹"+n.toLocaleString("en-IN")}
function save(){localStorage.setItem("korvaCart",JSON.stringify(cart));localStorage.setItem("korvaWishlist",JSON.stringify(wishlist))}
function cartCount(){return cart.reduce((a,x)=>a+x.qty,0)}
function icon(name){
  const icons={search:"⌕",account:"○",heart:"♡",cart:"□",menu:"☰"};
  return icons[name]||"";
}
function header(){
 return `<div class="topbar">FORM. LIGHT. SPACE. — CONTEMPORARY LIGHTING FOR CONSIDERED SPACES.</div>
 <header class="nav"><div class="container nav-inner">
  <a href="#/" class="logo-wrap"><span class="logo-mark">K</span><span class="logo">KORVA</span></a>
  <nav class="nav-links">
   <a href="#/category/chandeliers">Shop</a><a href="#/space/living-room">Spaces</a><a href="#/designer">Designers</a><a href="#/info/about">About</a>
  </nav>
  <div class="nav-actions">
   <button class="icon-btn" onclick="searchPrompt()" aria-label="Search">${icon("search")}</button>
   <button class="icon-btn" onclick="location.hash='#/account'" aria-label="Account">${icon("account")}</button>
   <button class="icon-btn" onclick="location.hash='#/wishlist'" aria-label="Wishlist">${icon("heart")}</button>
   <button class="icon-btn" onclick="location.hash='#/cart'" aria-label="Cart">${icon("cart")}<span class="badge">${cartCount()}</span></button>
  </div>
 </div></header>`;
}
function footer(){
 return `<footer class="footer"><div class="container">
  <div class="footer-grid">
   <div><div class="logo" style="margin-left:0;color:#fff">KORVA</div><p>Contemporary lighting designed around form, atmosphere and the way modern spaces are experienced.</p><p>FORM. LIGHT. SPACE.</p></div>
   <div><h3>Shop</h3><p><a href="#/category/chandeliers">Chandeliers</a><br><a href="#/category/wall-lights">Wall Lights</a><br><a href="#/category/pendants">Pendant Lights</a><br><a href="#/category/table-lamps">Table Lamps</a><br><a href="#/category/ceiling-lights">Ceiling Lights</a></p></div>
   <div><h3>Information</h3><p>${Object.entries(infoPages).map(([k,v])=>`<a href="#/info/${k}">${v[0]}</a>`).join("<br>")}</p></div>
   <div><h3>Contact</h3><p>Email: hello@korva.in<br>Phone: +91 00000 00000<br>Delhi NCR, India<br><br>Instagram · Facebook · YouTube · LinkedIn · Pinterest</p></div>
  </div>
  <div class="footer-bottom"><span>© 2026 KORVA. All rights reserved.</span><span>Privacy · Terms · Shipping</span></div>
 </div></footer>`;
}
function layout(content){return header()+content+footer()}
function home(){
 return layout(`<main>
  <section class="hero"><div class="hero-content"><div class="eyebrow">KORVA — FORM. LIGHT. SPACE.</div><h1>LIGHT THAT<br>DEFINES SPACE.</h1><p>Contemporary lighting designed around form, atmosphere and the way modern spaces are experienced.</p><a class="btn light" href="#/category/chandeliers">Explore Collection</a></div></section>
  <section class="section"><div class="container"><div class="section-head"><div class="eyebrow">Shop</div><h2>Shop by Categories</h2><p>From statement chandeliers to quiet architectural accents, explore lighting designed to belong.</p></div>
   <div class="grid cat-grid">${["Chandeliers","Wall Lights","Table Lamps","Pendant Lights","Bamboo Lights","Hanging Lights","Mirror Lights","Downlights"].map((x,i)=>`<a class="card" href="#/category/${slug(x)}"><div class="card-image ${i%3===0?"tall":""}"><span class="image-label">${x}</span></div><div class="card-body"><h3>${x}</h3><p>Explore collection →</p></div></a>`).join("")}</div>
  </div></section>
  <section class="section" style="padding-top:20px"><div class="container"><div class="section-head"><div class="eyebrow">Spaces</div><h2>Shop by Spaces</h2><p>Find the right light for the room, mood and moment.</p></div><div class="pill-row">${["Living Room","Dining Room","Bedroom","Kitchen","Study Room","Home Decor"].map(x=>`<a class="pill" href="#/space/${slug(x)}">${x}</a>`).join("")}</div></div></section>
  <section class="split"><div class="split-media"></div><div class="split-copy"><div class="eyebrow">The KORVA Approach</div><h2>Where light becomes form.</h2><p>Light does more than illuminate a room. It defines atmosphere, changes proportion, creates focus and reveals material. KORVA creates lighting around the relationship between form, light and space.</p><p>Designed beautifully. Supported completely — with thoughtful installation, warranty and after-sales service.</p><a class="btn" href="#/info/about">Discover KORVA</a></div></section>
  <section class="section"><div class="container"><div class="section-head"><div class="eyebrow">Featured</div><h2>New & Considered</h2><p>Selected pieces from the first KORVA collection.</p></div>${productGrid(products.slice(0,4))}</div></section>
  <section class="quote"><blockquote>“Lighting should not simply fill a room. It should shape how the room feels.”</blockquote><small>KORVA — FORM. LIGHT. SPACE.</small></section>
  <section class="section"><div class="container"><div class="section-head"><div class="eyebrow">Designers & Architects</div><h2>Create with KORVA</h2><p>Trade pricing, product information, project support, samples and installation coordination for professional projects.</p><a class="btn" href="#/designer">Work with us</a></div></div></section>
 </main>`);
}
function slug(s){return s.toLowerCase().replace(/&/g,"and").replace(/[^a-z0-9]+/g,"-").replace(/(^-|-$)/g,"")}
function productGrid(items){
 return `<div class="grid product-grid">${items.map(p=>`<article class="card"><a href="#/product/${p.id}"><div class="card-image"><span class="image-label">${p.category}</span></div></a><div class="card-body"><h3>${p.name}</h3><p>${p.desc}</p><div class="price">${money(p.price)} <del style="color:#999;font-weight:400;margin-left:5px">${money(p.old)}</del></div><div class="card-actions"><button class="small-btn" onclick="addToCart('${p.id}')">Add to cart</button><button class="small-btn" onclick="toggleWish('${p.id}')">${wishlist.includes(p.id)?"♥":"♡"}</button></div></div></article>`).join("")}</div>`;
}
function categoryPage(cat){
 const normalized=cat.replace(/-/g," ");
 let items=products;
 if(cat!=="all" && cat!=="chandeliers") items=products.filter(p=>slug(p.category)===cat);
 if(cat==="chandeliers") items=products.filter(p=>p.category==="Chandelier");
 const title=cat==="all"?"All Products":normalized.split(" ").map(x=>x[0]?.toUpperCase()+x.slice(1)).join(" ");
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Shop by Categories > ${title}</div><h1>${title}</h1><p style="color:var(--muted);max-width:680px;line-height:1.8">Explore KORVA pieces designed around proportion, material and atmosphere.</p></div><div class="filters"><div class="filter-group"><button class="pill" onclick="location.hash='#/category/all'">All</button><button class="pill" onclick="location.hash='#/category/chandeliers'">Chandeliers</button><button class="pill" onclick="location.hash='#/category/wall-lights'">Wall Lights</button><button class="pill" onclick="location.hash='#/category/pendant-lights'">Pendants</button><button class="pill" onclick="location.hash='#/category/table-lamps'">Table Lamps</button></div><span style="font-size:10px;color:var(--muted)">${items.length} products</span></div>${productGrid(items)}</main>`);
}
function spacePage(space){
 const name=space.replace(/-/g," ").replace(/\b\w/g,m=>m.toUpperCase());
 const items=products.filter(p=>p.space.toLowerCase()===space.replace(/-/g," "));
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Shop by Spaces > ${name}</div><h1>${name}</h1><div class="filter-group"><button class="pill">Indoor</button><button class="pill">Outdoor</button></div></div>${productGrid(items.length?items:products.slice(0,4))}</main>`);
}
function productPage(id){
 const p=products.find(x=>x.id===id)||products[0];
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Shop by Categories > ${p.category} > ${p.name}</div></div><section class="detail"><div><div class="gallery-main"></div><div class="thumbs">${[1,2,3,4].map(x=>`<div class="thumb"></div>`).join("")}</div></div><div class="detail-copy"><div class="eyebrow">${p.category}</div><h1>${p.name}</h1><p style="color:var(--muted);line-height:1.8">${p.desc}</p><div class="price">${money(p.price)} <del style="font-size:13px;color:#999">${money(p.old)}</del></div><div class="notice">Check delivery time and installation availability for your pincode before ordering.</div><div class="quantity"><button onclick="changeQty(-1)">−</button><span id="detailQty">1</span><button onclick="changeQty(1)">+</button></div><button class="btn" style="width:100%" onclick="addToCart('${p.id}')">Add to Cart</button><button class="btn gold" style="width:100%;margin-top:10px" onclick="buyNow('${p.id}')">Buy Now</button><div class="specs"><div class="spec"><span>SKU</span><b>${p.id}</b></div><div class="spec"><span>Size</span><b>Product-specific</b></div><div class="spec"><span>Warranty</span><b>As listed on product page</b></div><div class="spec"><span>Installation</span><b>Serviceable locations</b></div><div class="spec"><span>Delivery</span><b>Check at checkout</b></div></div></div></section><section class="section" style="padding-top:0"><div class="section-head"><div class="eyebrow">Product Information</div><h2>Description / Images / Video</h2><p>Product-specific specifications, dimensions, installation instructions and media can be inserted here as each SKU is finalized.</p></div></section></main>`);
}
let detailQty=1;
function changeQty(n){detailQty=Math.max(1,detailQty+n);const el=document.getElementById("detailQty");if(el)el.textContent=detailQty}
function addToCart(id){
 const existing=cart.find(x=>x.id===id);
 if(existing) existing.qty += detailQty || 1; else cart.push({id,qty:detailQty||1});
 detailQty=1;save();render();toast("Added to cart");
}
function buyNow(id){addToCart(id);location.hash="#/cart"}
function toggleWish(id){wishlist.includes(id)?wishlist=wishlist.filter(x=>x!==id):wishlist.push(id);save();render()}
function cartPage(){
 const rows=cart.map(item=>{const p=products.find(x=>x.id===item.id);return `<div class="cart-row"><div class="cart-img"></div><div><h3 style="font-size:25px">${p.name}</h3><p style="font-size:11px;color:var(--muted)">${p.category}</p></div><div class="quantity"><button onclick="updateCart('${p.id}',-1)">−</button><span>${item.qty}</span><button onclick="updateCart('${p.id}',1)">+</button></div><strong>${money(p.price*item.qty)}</strong></div>`}).join("");
 const total=cart.reduce((s,i)=>s+(products.find(p=>p.id===i.id).price*i.qty),0);
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Cart</div><h1>Your Cart</h1></div>${rows||`<div class="info-content"><h2>Your cart is empty.</h2><p>Explore the collection and add your first KORVA piece.</p><a class="btn" href="#/category/chandeliers">Explore Collection</a></div>`}${cart.length?`<div class="total-box"><div class="total-line"><span>Subtotal</span><b>${money(total)}</b></div><div class="total-line"><span>Shipping</span><span>Calculated at checkout</span></div><div class="total-line grand"><span>Total</span><b>${money(total)}</b></div><button class="btn" style="width:100%;margin-top:15px" onclick="checkout()">Proceed to Checkout</button></div>`:""}</main>`);
}
function updateCart(id,n){const x=cart.find(i=>i.id===id);if(!x)return;x.qty+=n;if(x.qty<=0)cart=cart.filter(i=>i.id!==id);save();render()}
function accountPage(){
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Account</div><h1>Account</h1></div><div class="info-content"><h2>Sign in</h2><p>Sign in, create an account, view activity and manage orders.</p><div class="form-grid"><input class="field" placeholder="Email"><input class="field" placeholder="Password"><button class="btn" onclick="toast('Demo sign-in — connect your backend later')">Sign In</button><button class="btn gold" onclick="toast('Demo account creation')">Create Account</button></div><hr style="border:0;border-top:1px solid var(--line);margin:40px 0"><h2>Activity</h2><p>Your order history, saved products and service requests will appear here after the ecommerce backend is connected.</p></div></main>`);
}
function wishlistPage(){
 const items=products.filter(p=>wishlist.includes(p.id));
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Wishlist</div><h1>Liked</h1></div>${items.length?productGrid(items):`<div class="info-content"><h2>No saved products yet.</h2><p>Use the ♡ icon on a product to save it here.</p></div>`}</main>`);
}
function infoPage(key){
 const [title,lead]=infoPages[key]||infoPages.about;
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Information > ${title}</div><h1>${title}</h1></div><div class="info-layout"><aside class="info-nav">${Object.entries(infoPages).map(([k,v])=>`<button onclick="location.hash='#/info/${k}'">${v[0]}</button>`).join("")}</aside><article class="info-content"><h2>${title}</h2><p>${lead}</p><p>This page is intentionally structured as a separate information page, matching your prototype. Replace this draft with the final approved KORVA policy/content before launch.</p><h3 style="font-size:28px;margin-top:30px">Information</h3><p>Customers should be able to read the complete policy, process, contact details and relevant conditions from this page without needing to leave the website.</p></article></div></main>`);
}
function designerPage(){
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Consultants / Hire Architects & Designers</div><h1>Design with KORVA</h1><p style="color:var(--muted);max-width:720px;line-height:1.8">A dedicated space for consultants, architects, interior designers and project teams.</p></div><div class="info-content"><h2>Tell us about your project.</h2><p>Submit your details and our team can share pricing, service information, product documentation and project support.</p><div class="form-grid"><input class="field" placeholder="Name"><input class="field" placeholder="Email"><input class="field" placeholder="Phone"><input class="field" placeholder="City"><input class="field full" placeholder="Company Name"><textarea class="field full" rows="5" placeholder="Project details / requirements"></textarea><button class="btn" onclick="toast('Thank you — connect this form to your email/CRM before launch.')">Submit</button></div><div class="section-head" style="margin:60px 0 20px"><div class="eyebrow">Professional Support</div><h2>Pricing · Service Info · Theory · Reviews</h2></div><div class="grid cat-grid">${["Trade Pricing","Service Info","Lighting Theory","Project Reviews"].map(x=>`<div class="card"><div class="card-body"><h3>${x}</h3><p>Dedicated content section for professional customers.</p></div></div>`).join("")}</div></div></main>`);
}
function qaPage(){
 const qs=["How do I choose the right chandelier size?","Do you provide installation?","What is covered under warranty?","How can I check delivery time?","Can I order a sample?","Do you support interior designers?","Can I replace a driver or component?","How do I return a damaged product?","Do you offer project pricing?","How can I contact KORVA?"];
 return layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Home > Q/A</div><h1>Q/A</h1></div><div class="info-content">${qs.map((q,i)=>`<details style="border-bottom:1px solid var(--line);padding:18px 0"><summary style="cursor:pointer;font-family:'Cormorant Garamond';font-size:25px">${i+1}. ${q}</summary><p style="margin:12px 0 0;color:var(--muted);line-height:1.8">Answer for this question will be finalized from KORVA's actual product, service and policy information.</p></details>`).join("")}</div></main>`);
}
function searchPrompt(){
 const q=prompt("Search KORVA products");
 if(!q)return;
 const found=products.filter(p=>(p.name+p.category+p.space).toLowerCase().includes(q.toLowerCase()));
 document.getElementById("app").innerHTML=layout(`<main class="container"><div class="page-head"><div class="breadcrumb">Search</div><h1>Search results</h1><p style="color:var(--muted)">Results for “${q}”</p></div>${found.length?productGrid(found):`<div class="info-content"><h2>No matching products.</h2><p>Try chandelier, wall light, pendant, lamp or living room.</p></div>`}</main>`);
}
function checkout(){toast("Checkout is a front-end demo. Connect Razorpay/Shopify/WooCommerce/backend before accepting real payments.")}
function toast(msg){const x=document.createElement("div");x.textContent=msg;x.style.cssText="position:fixed;right:20px;bottom:20px;background:#171717;color:#fff;padding:14px 18px;z-index:100;border-left:3px solid #c9a86a;font-size:12px";document.body.appendChild(x);setTimeout(()=>x.remove(),2800)}
function render(){
 const path=location.hash.replace(/^#\/?/,"").split("/");
 let html;
 if(!path[0]) html=home();
 else if(path[0]==="category") html=categoryPage(path[1]||"all");
 else if(path[0]==="space") html=spacePage(path[1]||"living-room");
 else if(path[0]==="product") html=productPage(path[1]);
 else if(path[0]==="account") html=accountPage();
 else if(path[0]==="cart") html=cartPage();
 else if(path[0]==="wishlist") html=wishlistPage();
 else if(path[0]==="designer") html=designerPage();
 else if(path[0]==="qa") html=qaPage();
 else if(path[0]==="info") html=infoPage(path[1]);
 else html=home();
 document.getElementById("app").innerHTML=html;
 window.scrollTo(0,0);
}
window.addEventListener("hashchange",render);
window.addEventListener("DOMContentLoaded",render);
'''

readme = r'''# KORVA Website — Prototype Build v1

This is a static HTML/CSS/JavaScript implementation based on the uploaded KORVA website prototype.

## Prototype structure implemented
1. Home page
2. Account / Cart
3. Information pages:
   - Returns & Exchanges
   - Track Order
   - About Us
   - Warranty Policy
   - Service Policy
   - Terms & Conditions
   - Blog / Journal
   - Installation Policy
4. Shop by Categories
5. Shop by Spaces
6. Consultants / Architects & Designers
7. Product page from category
8. Product page from space
9. Q/A

## Run locally
Open `index.html` in a browser.

## Run in Google Colab
Upload/unzip the project and run:

```python
!python -m http.server 8000 --directory /content/korva_website
