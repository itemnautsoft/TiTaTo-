# Tic Tac Tower — Credits screen (removed in ver.0.31)

The credits screen was built in ver.0.28–0.30 and removed from the game in ver.0.31 so it can be brought back later. The last build that contains it is **ver.0.30, commit `8291371`**. The quickest way to see or restore it:

```
git show 8291371:index.html > credits-version.html
```

## What it was

A full-screen overlay opened from the menu's **Credits** button.
- Names float slowly around a transparent screen (over the 3D stage background), each in a different font, faded to 20% opacity, scaled up when they are the active speaker.
- The game's real 3D sun (same model as the line-clearing sun: spinning wire cage, pulsing core, eyes, cheeks, nose, mouth) sits at the bottom right at one third of its in-game size, and scales with camera zoom.
- Every ~4.2 seconds the sun moves to the next person, changes core color (from the game's sun palette `SUNPAL`), pops slightly and opens its mouth while a comic bubble above it says why that person is listed. Tapping a name makes the sun speak for it right away. ✕ closes the screen.

### Credit text (as of ver.0.30)

| Name | Why |
|---|---|
| itemnaut | Creative director, game designer & concept artist. Dreamed up the tower, drew the characters and steered every decision. |
| Claude | Engineer & technical artist. Built the 3D world, animation, AI opponents and online play from itemnaut's direction. |
| Noa | for squashing |
| Andres | for new feature ideas |
| Starky | for demanding difficulty |
| Rosalie | for making mistakes |
| Englebert | for chasing challenges |
| Lyd | for having fun |
| You | for playing! |

## How to put it back (4 edits in `index.html`)

1. **CSS** — paste block A at the end of the `<style>` element, just before `</style>`.
2. **HTML** — paste block B directly before `<div id="cs">` (the color-studio overlay).
3. **Menu button** — add `<button id="mnK">Credits</button>` to the menu (`#mnb`), for example right after the Colors button.
4. **JS** — paste block C just before the `/* self-update:` comment (it must come after the color studio code, because it uses `closeMn`, `$n`, `FONTS`, `SUNPAL`, `wm`, `TH`, `th`, `cam`, `cv`, `camZ` and `ppu`), then replace the render line in `frame()`:

```js
// replace this (the current line):
if(csOn){csStep(now);rend.render(scene2,cam2)}else rend.render(scene,cam);
// with this:
if(crOn)crStep(performance.now());if(crOn)rend.render(scene3,cam);else if(csOn){csStep(now);rend.render(scene2,cam2)}else rend.render(scene,cam);
```

Then bump the version number in the `#ver` bubble and in `BUILD`.

## Block A — CSS

```css
#cr{position:fixed;inset:0;z-index:42;background:transparent;color:#000;display:none;overflow:hidden;touch-action:manipulation}
#cr.on{display:block}
#crn span{position:absolute;white-space:nowrap;cursor:pointer;transition:opacity .35s,transform .35s;user-select:none;-webkit-user-select:none;will-change:transform}
#crx{position:absolute;right:max(12px,env(safe-area-inset-right,0px));top:calc(env(safe-area-inset-top,0px) + 12px);z-index:3;font:900 18px system-ui,-apple-system,sans-serif;color:#000;background:#fff;border:3px solid #000;border-radius:12px;box-shadow:3px 3px 0 #000;padding:4px 12px}
#crs{position:absolute;right:max(40px,env(safe-area-inset-right,0px));bottom:calc(8% + env(safe-area-inset-bottom,0px));width:70px;height:70px;z-index:2;pointer-events:none}
#crb{position:absolute;right:-14px;bottom:calc(100% + 16px);transform-origin:85% 100%;width:max-content;max-width:min(300px,86vw);background:#fff;border:3px solid #000;border-radius:18px;box-shadow:4px 4px 0 #000;padding:9px 14px;text-align:center;font:700 15px/1.3 system-ui,-apple-system,sans-serif;animation:crp .3s cubic-bezier(.3,1.7,.5,1)}
#crb b{display:block;font:900 20px/1.1 system-ui,-apple-system,sans-serif;margin-bottom:2px}
#crb:after{content:'';position:absolute;right:46px;bottom:-16px;border:10px solid transparent;border-top:14px solid #000;border-bottom:0}
#crb:before{content:'';position:absolute;right:50px;bottom:-10px;border:6px solid transparent;border-top:10px solid #fff;border-bottom:0;z-index:1}
@keyframes crp{from{transform:scale(.2);opacity:0}to{transform:scale(1);opacity:1}}
```

## Block B — HTML

```html
<div id="cr"><button id="crx">✕</button><div id="crn"></div><div id="crs"><div id="crb"></div></div></div>
```

## Block C — JavaScript

```js
/* ---------- credits ---------- */
const CRED=[['itemnaut','Creative director, game designer & concept artist. Dreamed up the tower, drew the characters and steered every decision.'],
 ['Claude','Engineer & technical artist. Built the 3D world, animation, AI opponents and online play from itemnaut’s direction.'],
 ['Noa','for squashing'],['Andres','for new feature ideas'],['Starky','for demanding difficulty'],['Rosalie','for making mistakes'],['Englebert','for chasing challenges'],['Lyd','for having fun'],['You','for playing!']];
let crOn=false,crI=-1,crT=0,crSp=0,crEls=null;
const scene3=new THREE.Scene(),_sp=new THREE.Vector3();let SUN=null;
function makeSun(){const PL=SUNPAL[0],coreM=new THREE.MeshBasicMaterial({color:PL[0]}),ink=new THREE.MeshBasicMaterial({color:0x1d2a00}),cm=new THREE.MeshBasicMaterial({color:PL[2]}),root=new THREE.Group(),ch=[],eyes=[];
 const core=new THREE.Mesh(new THREE.SphereGeometry(2.1,16,12),coreM),wire=new THREE.Mesh(new THREE.IcosahedronGeometry(3.1,1),wm(new THREE.Color(TH[th].R)));root.add(core,wire);
 [-1,1].forEach(sg=>{const e=new THREE.Mesh(new THREE.CircleGeometry(.065,10),ink);e.position.set(sg*.14,.16,2.08);e.scale.y=1.6;root.add(e);eyes.push(e);
  const c2=new THREE.Mesh(new THREE.SphereGeometry(.1,8,6),cm);c2.position.set(sg*.27,-.03,2.06);root.add(c2);ch.push(c2)});
 const ns=new THREE.Mesh(new THREE.SphereGeometry(.035,8,6),ink);ns.position.set(0,.07,2.1);root.add(ns);
 const mo=new THREE.Mesh(new THREE.CircleGeometry(.05,10),ink);mo.position.set(0,-.04,2.115);root.add(mo);
 const sml=new THREE.Mesh(new THREE.TorusGeometry(.09,.014,6,14,PI),ink);sml.rotation.z=PI;sml.position.set(0,.045,2.115);root.add(sml);
 root.scale.setScalar(1/3);scene3.add(root);return{root,core,wire,coreM,cm,ch,eyes,ns,mo,sml,k1:new THREE.Color(),k2:new THREE.Color()}}
function crSay(i,now){crI=i;crT=now;crSp=now+2600;const [n,w]=CRED[i],b=$n('crb');b.style.animation='none';void b.offsetWidth;b.style.animation='';b.innerHTML='<b></b>';b.firstChild.textContent=n;b.append(document.createTextNode(w));if(!SUN)SUN=makeSun();{const PL=SUNPAL[i%SUNPAL.length];SUN.k1.set(PL[0]);SUN.k2.set(PL[1]);SUN.cm.color.set(PL[2])}crEls.forEach((e,k)=>{e.el.style.opacity=k===i?.95:.2;e.el.style.transform=''})}
function openCr(){const host=$n('crn');if(!crEls){host.innerHTML='';crEls=CRED.map((c,i)=>{const el=document.createElement('span');el.textContent=c[0];el.style.fontFamily=FONTS[(i*3+1)%FONTS.length];el.style.fontSize=(26+Math.random()*20|0)+'px';if(Math.random()<.35)el.style.fontStyle='italic';el.style.fontWeight=700;el.style.opacity=.2;el.onclick=()=>crSay(i,performance.now());host.append(el);
  return{el,u:.07+(i%2)*.5+Math.random()*.05,v:.09+(i/2|0)*.11+Math.random()*.025,ph:Math.random()*6.28}})}
 crOn=true;$n('cr').classList.add('on');crSay(0,performance.now())}
function crStep(now){const t=now/1000,W=innerWidth,H=innerHeight;
 crEls.forEach((e,k)=>{const x=Math.max(8,Math.min(W-e.el.offsetWidth*1.25-8,e.u*W+Math.sin(t*.45+e.ph)*16)),y=e.v*H+Math.cos(t*.35+e.ph*1.3)*14,s=k===crI?1.25:1;e.el.style.left=x+'px';e.el.style.top=y+'px';e.el.style.transform='scale('+s+') rotate('+Math.sin(t*.3+e.ph)*4+'deg)'});
 const talk=now<crSp,S=SUN,pp=ppu||60;
 {const sz=2*1.05*pp*camZ;$n('crs').style.width=$n('crs').style.height=sz+'px';const r=$n('crs').getBoundingClientRect(),cr=cv.getBoundingClientRect();
  _sp.set(((r.left+r.width/2-cr.left)/cr.width)*2-1,1-((r.top+r.height/2-cr.top)/cr.height)*2,0).unproject(cam);S.root.position.copy(_sp);S.root.lookAt(cam.position)}
 S.root.scale.setScalar((1/3)*(1+.14*Math.max(0,1-(now-crT)/260)));S.wire.rotation.y=t*1.4;S.wire.material.color.set(TH[th].R);
 S.coreM.color.copy(S.k1).lerp(S.k2,.5+.5*Math.sin(t*5));S.core.scale.setScalar(1+.04*Math.sin(t*6));S.ch.forEach(c=>c.scale.setScalar(1+.18*Math.sin(t*7)));
 const bl=(t%3.2)<.12?1:0;S.eyes.forEach(e=>e.scale.set(1+.5*bl,1.6*(1-bl)+.14*bl,1));
 S.mo.visible=talk;S.sml.visible=!talk;if(talk)S.mo.scale.setScalar(.75+.875*Math.abs(Math.sin(t*14)));
 if(now-crT>4200)crSay((crI+1)%CRED.length,now)}
$n('crx').onclick=()=>{crOn=false;$n('cr').classList.remove('on')};
$n('mnK').onclick=()=>{closeMn();openCr()};
```

## Notes
- The mouth was reduced to a quarter of its first size while talking (`S.mo.scale ... .75+.875*...`).
- Names were laid out in two columns in the upper two thirds of the screen so the sun and bubble at the bottom right never overlap them.
- Dark mode (untested, known gap): dark mode inverts only the 3D canvas, so the floating names (black text) would sit on a black background. When reintroducing, add `html[data-theme="dark"] #crn span{color:#fff}` to the CSS.
