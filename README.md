<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TF家族缘分指数测试</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=ZCOOL+KuaiLe&family=Noto+Sans+SC:wght@400;700&display=swap');

        :root {
            --tf-orange: #ffb11b;
        }

        body {
            font-family: 'Noto Sans SC', sans-serif;
            background: linear-gradient(135deg, #fffbf0 0%, #fff5e6 100%);
            color: #4a3f35;
            min-height: 100vh;
        }

        .cute-card {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 24px;
            border: 3px solid #ffffff;
            box-shadow: 0 10px 30px rgba(255, 177, 27, 0.1);
        }

        .title-font {
            font-family: 'ZCOOL KuaiLe', cursive;
        }

        .btn-tf {
            background: linear-gradient(90deg, #ff8c00 0%, #ffb11b 100%);
            border-radius: 50px;
            color: white;
            box-shadow: 0 6px 15px rgba(255, 140, 0, 0.3);
            transition: all 0.3s;
        }

        .btn-tf:active {
            transform: scale(0.95);
        }

        .score-val {
            font-family: 'ZCOOL KuaiLe', cursive;
            color: #ff8c00;
            font-size: 1.6rem;
        }

        .tag {
            font-size: 0.65rem;
            padding: 2px 8px;
            border-radius: 8px;
            font-weight: bold;
        }

        .tag-ye { background: #fef3c7; color: #d97706; } /* 业胎/命之星 */
        .tag-rong { background: #dbeafe; color: #2563eb; } /* 荣亲/成 */
        .tag-wei { background: #f3f4f6; color: #6b7280; } /* 危/坏 */

        .gen-badge {
            font-size: 0.55rem;
            padding: 1px 4px;
            border: 1px solid #ffcc80;
            color: #fb8c00;
            border-radius: 4px;
            background: #fff;
        }

        input[type="date"] {
            border: 2px solid #ffe0b2;
            border-radius: 12px;
            padding: 12px;
            width: 100%;
            outline: none;
            font-size: 1.1rem;
            -webkit-appearance: none;
        }

        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #ffb11b; border-radius: 10px; }
    </style>
</head>
<body class="p-4 flex flex-col items-center">
    <div class="max-w-md w-full">
        <header class="text-center mb-8 mt-6">
            <h1 class="text-4xl title-font text-orange-500 mb-2"> TF家族缘分指数测试</h1>
            <div class="flex items-center justify-center gap-2">
                <span class="h-px w-6 bg-orange-200"></span>
                <p class="text-[10px] text-orange-300 tracking-[0.2em] font-bold">全员47人·深度命理分析</p>
                <span class="h-px w-6 bg-orange-200"></span>
            </div>
        </header>

        <div id="input-view" class="cute-card p-8 animate-in fade-in zoom-in duration-500">
            <div class="space-y-6">
                <div class="text-center pb-4 border-b border-orange-50">
                    <p class="text-sm text-gray-400 italic">“少年与你的距离，由星辰与生肖决定”</p>
                </div>
                <div>
                    <label class="block text-sm font-bold text-orange-500 mb-2 ml-1"> 请输入你的出生日期</label>
                    <input type="date" id="birthDate">
                </div>
                <button onclick="calculateDestiny()" class="w-full btn-tf py-4 text-xl font-bold tracking-widest mt-4">
                    全员47人深度排盘
                </button>
            </div>
        </div>

        <div id="result-view" class="hidden space-y-4 pb-20">
            <div class="cute-card p-6 text-center bg-white border-2 border-orange-100">
                <div id="userHeader" class="text-2xl font-bold text-gray-700"></div>
                <div id="userSub" class="text-xs text-orange-400 mt-2 font-medium"></div>
            </div>

            <div id="listContainer" class="space-y-3"></div>

            <button onclick="location.reload()" class="w-full text-center text-xs text-gray-300 py-8 italic underline">← 重新测试</button>
        </div>
    </div>

    <script>
        // --- 全员47人数据库 ---
        const members = [
            { name: "王俊凯", xiu: "室", sun: "处女", moon: "水瓶", zod: "兔", gen: "一代" },
            { name: "王源", xiu: "昴", sun: "天蝎", moon: "白羊", zod: "龙", gen: "一代" },
            { name: "易烊千玺", xiu: "虚", sun: "射手", moon: "摩羯", zod: "龙", gen: "一代" },
            { name: "马嘉祺", xiu: "胃", sun: "射手", moon: "双鱼", zod: "马", gen: "二代" },
            { name: "丁程鑫", xiu: "张", sun: "双鱼", moon: "巨蟹", zod: "马", gen: "二代" },
            { name: "宋亚轩", xiu: "张", sun: "双鱼", moon: "狮子", zod: "猴", gen: "二代" },
            { name: "刘耀文", xiu: "参", sun: "天秤", moon: "金牛", zod: "鸡", gen: "二代" },
            { name: "张真源", xiu: "氐", sun: "白羊", moon: "天秤", zod: "羊", gen: "二代" },
            { name: "严浩翔", xiu: "轸", sun: "狮子", moon: "处女", zod: "猴", gen: "二代" },
            { name: "贺峻霖", xiu: "毕", sun: "双子", moon: "金牛", zod: "猴", gen: "二代" },
            { name: "敖子逸", xiu: "角", sun: "摩羯", moon: "处女", zod: "马", gen: "二代" },
            { name: "李天泽", xiu: "壁", sun: "白羊", moon: "双鱼", zod: "马", gen: "旗下" },
            { name: "姚景元", xiu: "张", sun: "处女", moon: "双子", zod: "马", gen: "旗下" },
            { name: "宋文嘉", xiu: "奎", sun: "双子", moon: "射手", zod: "马", gen: "旗下" },
            { name: "朱志鑫", xiu: "轸", sun: "天蝎", moon: "巨蟹", zod: "鸡", gen: "三代" },
            { name: "左航", xiu: "娄", sun: "双子", moon: "双鱼", zod: "狗", gen: "三代" },
            { name: "苏新皓", xiu: "心", sun: "摩羯", moon: "天秤", zod: "猪", gen: "三代" },
            { name: "张极", xiu: "翼", sun: "水瓶", moon: "天蝎", zod: "猪", gen: "三代" },
            { name: "张澤禹", xiu: "氐", sun: "金牛", moon: "天秤", zod: "猪", gen: "三代" },
            { name: "张峻豪", xiu: "井", sun: "巨蟹", moon: "双子", zod: "猪", gen: "三代" },
            { name: "余宇涵", xiu: "虚", sun: "射手", moon: "摩羯", zod: "狗", gen: "三代" },
            { name: "童禹坤", xiu: "柳", sun: "巨蟹", moon: "白羊", zod: "鸡", gen: "三代" },
            { name: "邓佳鑫", xiu: "斗", sun: "狮子", moon: "水瓶", zod: "狗", gen: "三代" },
            { name: "陈天润", xiu: "亢", sun: "双子", moon: "狮子", zod: "猪", gen: "三代" },
            { name: "穆祉丞", xiu: "奎", sun: "天蝎", moon: "狮子", zod: "猪", gen: "三代" },
            { name: "黄朔", xiu: "张", sun: "处女", moon: "摩羯", zod: "猪", gen: "三代" },
            { name: "张桂源", xiu: "箕", sun: "金牛", moon: "射手", zod: "牛", gen: "四代" },
            { name: "左奇函", xiu: "昴", sun: "双鱼", moon: "金牛", zod: "虎", gen: "四代" },
            { name: "陈思罕", xiu: "昴", sun: "天蝎", moon: "白羊", zod: "兔", gen: "四代" },
            { name: "陈浚铭", xiu: "昴", sun: "金牛", moon: "白羊", zod: "龙", gen: "四代" },
            { name: "聂玮辰", xiu: "轸", sun: "天秤", moon: "金牛", zod: "虎", gen: "四代" },
            { name: "张奕然", xiu: "箕", sun: "处女", moon: "射手", zod: "虎", gen: "四代" },
            { name: "王橹杰", xiu: "轸", sun: "摩羯", moon: "天秤", zod: "虎", gen: "四代" },
            { name: "李嘉森", xiu: "壁", sun: "摩羯", moon: "双鱼", zod: "虎", gen: "四代" },
            { name: "杨博文", xiu: "危", sun: "双子", moon: "水瓶", zod: "虎", gen: "四代" },
            { name: "杨涵博", xiu: "壁", sun: "巨蟹", moon: "处女", zod: "虎", gen: "四代" },
            { name: "智恩源", xiu: "虚", sun: "摩羯", moon: "双子", zod: "牛", gen: "四代" },
            { name: "官俊臣", xiu: "心", sun: "巨蟹", moon: "水瓶", zod: "猪", gen: "四代" },
            { name: "魏子宸", xiu: "张", sun: "天蝎", moon: "双鱼", zod: "虎", gen: "四代" },
            { name: "张函瑞", xiu: "娄", sun: "天秤", moon: "天秤", zod: "牛", gen: "四代" },
            { name: "傅托斯", xiu: "参", sun: "摩羯", moon: "狮子", zod: "虎", gen: "四代" },
            { name: "张重晟", xiu: "毕", sun: "金牛", moon: "射手", zod: "牛", gen: "五代" },
            { name: "徐子恒", xiu: "心", sun: "射手", moon: "处女", zod: "虎", gen: "五代" },
            { name: "李嘉宝", xiu: "亢", sun: "天蝎", moon: "狮子", zod: "虎", gen: "五代" },
            { name: "林子诚", xiu: "角", sun: "双子", moon: "天秤", zod: "龙", gen: "五代" },
            { name: "张艺凡", xiu: "昴", sun: "水瓶", moon: "白羊", zod: "龙", gen: "旗下" },
            { name: "邓恩熙", xiu: "翼", sun: "白羊", moon: "天蝎", zod: "鸡", gen: "旗下" }
        ];

        const xingxiu = ["角", "亢", "氐", "房", "心", "尾", "箕", "斗", "女", "虚", "危", "室", "壁", "奎", "娄", "胃", "昴", "毕", "觜", "参", "井", "鬼", "柳", "星", "张", "翼", "轸"];

        function calculateDestiny() {
            const birthInput = document.getElementById('birthDate').value;
            if (!birthInput) return;

            const birthDate = new Date(birthInput);
            const m = birthDate.getMonth() + 1;
            const d = birthDate.getDate();
            const y = birthDate.getFullYear();

            const uSun = getSunSign(m, d);
            const uZod = ["鼠", "牛", "虎", "兔", "龙", "蛇", "马", "羊", "猴", "鸡", "狗", "猪"][(y - 4) % 12];
            const diff = Math.floor((birthDate - new Date("1998-09-01")) / 86400000);
            let uIdx = (7 + diff) % 27; if (uIdx < 0) uIdx += 27;
            const uXiu = xingxiu[uIdx];

            document.getElementById('userHeader').innerText = `${uXiu}宿 · ${uSun}座`;
            document.getElementById('userSub').innerText = `✨ 属${uZod}的你，与 47 位少年的缘分如下 ✨`;

            const results = members.map(mem => {
                const uX_idx = xingxiu.indexOf(uXiu);
                const mX_idx = xingxiu.indexOf(mem.xiu);
                const dist = (mX_idx - uX_idx + 27) % 27;

                let base = 70;
                let type = "友衰";
                if (dist === 0) { base = 86; type = "命之星"; }
                else if ([9, 18].includes(dist)) { base = 93; type = "业胎"; }
                else if ([1, 26, 10, 17, 19, 8].includes(dist)) { base = 89; type = "荣亲"; }
                else if ([2, 11, 20].includes(dist)) { base = 82; type = "成"; }
                else if ([7, 16, 25].includes(dist)) { base = 56; type = "危"; }
                else if ([4, 13, 22].includes(dist)) { base = 51; type = "坏"; }

                let weight = 0;
                if (uSun === mem.moon) weight += 6.5;
                const element = { "土":["金牛","处女","摩羯"], "水":["巨蟹","天蝎","双鱼"], "火":["白羊","狮子","射手"], "风":["双子","天秤","水瓶"] };
                const uE = Object.keys(element).find(k => element[k].includes(uSun));
                const mE_moon = Object.keys(element).find(k => element[k].includes(mem.moon));
                if (uE === mE_moon) weight += 4;
                if (uSun === mem.sun) weight += 2.5;

                const zodiacRel = {
                    "鼠": { trine: ["龙", "猴"], hex: "牛", clash: ["马", "羊"] },
                    "牛": { trine: ["蛇", "鸡"], hex: "鼠", clash: ["羊", "马"] },
                    "虎": { trine: ["马", "狗"], hex: "猪", clash: ["猴", "蛇"] },
                    "兔": { trine: ["猪", "羊"], hex: "狗", clash: ["鸡", "龙"] },
                    "龙": { trine: ["鼠", "猴"], hex: "鸡", clash: ["狗", "兔"] },
                    "蛇": { trine: ["鸡", "牛"], hex: "猴", clash: ["猪", "虎"] },
                    "马": { trine: ["虎", "狗"], hex: "羊", clash: ["鼠", "牛"] },
                    "羊": { trine: ["兔", "猪"], hex: "马", clash: ["牛", "鼠"] },
                    "猴": { trine: ["鼠", "龙"], hex: "蛇", clash: ["虎", "猪"] },
                    "鸡": { trine: ["蛇", "牛"], hex: "龙", clash: ["兔", "狗"] },
                    "狗": { trine: ["虎", "马"], hex: "兔", clash: ["龙", "鸡"] },
                    "猪": { trine: ["羊", "兔"], hex: "虎", clash: ["蛇", "猴"] }
                };
                const rel = zodiacRel[uZod];
                if (rel.trine.includes(mem.zod)) weight += 4.5;
                if (rel.hex === mem.zod) weight += 5.5;
                if (rel.clash.includes(mem.zod)) weight -= 6.0;

                let finalScore = Math.min(99, base + weight + (Math.random() * 1.2));
                return { ...mem, final: finalScore.toFixed(1), type };
            }).sort((a, b) => b.final - a.final);

            const container = document.getElementById('listContainer');
            container.innerHTML = '';

            results.forEach((res, i) => {
                const card = document.createElement('div');
                const isBottom = i > 41;
                card.className = `cute-card p-4 flex justify-between items-center transition-all ${isBottom ? 'opacity-50 grayscale bg-gray-50 scale-95' : 'hover:border-orange-200'}`;
                card.innerHTML = `
                    <div class="flex-1">
                        <div class="flex items-center gap-2 mb-1">
                            <span class="text-base font-bold text-gray-700">${res.name}</span>
                            <span class="gen-badge">${res.gen}</span>
                            <span class="tag ${res.final > 80 ? 'tag-ye' : res.final > 70 ? 'tag-rong' : 'tag-wei'}">${res.type}</span>
                        </div>
                        <p class="text-[9px] text-gray-400 italic">日${res.sun} · 月${res.moon} · 属${res.zod} · ${res.xiu}宿</p>
                    </div>
                    <div class="score-val">${Math.floor(res.final)}<span class="text-xs">%</span></div>
                `;
                
                if (i === 0) {
                    const h = document.createElement('p');
                    h.className = "text-[10px] text-orange-400 font-bold mb-2 ml-1 mt-4 tracking-widest";
                    h.innerText = " 命中注定天选区";
                    container.appendChild(h);
                }
                
                if (i === results.length - 5) {
                    const l = document.createElement('p');
                    l.className = "text-[10px] text-gray-400 font-bold mb-2 mt-8 ml-1 tracking-widest";
                    l.innerText = " 磁场冲突警戒区";
                    container.appendChild(l);
                }
                
                container.appendChild(card);
            });

            document.getElementById('input-view').classList.add('hidden');
            document.getElementById('result-view').classList.remove('hidden');
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function getSunSign(m, d) {
            const s = ["摩羯","水瓶","双鱼","白羊","金牛","双子","巨蟹","獅子","处女","天秤","天蝎","射手","摩羯"];
            const cuts = [19, 18, 20, 19, 20, 21, 22, 22, 22, 23, 22, 21];
            return d > cuts[m-1] ? s[m] : s[m-1];
        }
    </script>
</body>
</html>
