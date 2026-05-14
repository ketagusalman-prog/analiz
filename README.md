<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>M4 | Üst Düzey Operasyonel Analiz Merkezi</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg: #0b0f1a; --card: #161b2a; --accent: #3b82f6; --success: #10b981;
            --danger: #ef4444; --warning: #f59e0b; --text: #f8fafc; --border: rgba(255,255,255,0.08);
        }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); margin: 0; padding: 20px; }
        .container { max-width: 1500px; margin: auto; }
        
        .header { display: flex; justify-content: space-between; align-items: center; padding: 20px; background: var(--card); border-radius: 15px; border: 1px solid var(--border); margin-bottom: 20px; }
        
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; margin-bottom: 20px; }
        .stat-card { background: var(--card); padding: 20px; border-radius: 15px; border: 1px solid var(--border); }
        .stat-val { font-size: 28px; font-weight: 800; display: block; margin: 10px 0; color: var(--accent); }
        
        .report-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        .report-section { background: var(--card); padding: 20px; border-radius: 15px; border: 1px solid var(--border); }
        
        table { width: 100%; border-collapse: collapse; margin-top: 15px; }
        th { text-align: left; padding: 12px; color: #64748b; font-size: 11px; text-transform: uppercase; border-bottom: 2px solid var(--border); }
        td { padding: 12px; border-bottom: 1px solid var(--border); font-size: 13px; }
        
        .badge { padding: 4px 8px; border-radius: 6px; font-size: 10px; font-weight: 800; }
        .bg-day { background: rgba(245, 158, 11, 0.15); color: #f59e0b; }
        .bg-night { background: rgba(139, 92, 246, 0.15); color: #8b5cf6; }
        .bg-success { background: rgba(16, 185, 129, 0.1); color: var(--success); }
        .bg-danger { background: rgba(239, 68, 68, 0.1); color: var(--danger); }
        
        .progress-bar { height: 8px; background: #2d3748; border-radius: 10px; margin-top: 5px; overflow: hidden; }
        .progress-fill { height: 100%; background: var(--accent); transition: 0.5s; }
        
        .btn-sync { background: var(--accent); color: white; border: none; padding: 12px 25px; border-radius: 10px; font-weight: 700; cursor: pointer; display: flex; align-items: center; gap: 10px; }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        <div>
            <h1 style="margin:0; font-size: 22px;">M4 Operasyonel Analiz Portalı</h1>
            <p id="live-info" style="margin:5px 0 0; color:#64748b;"></p>
        </div>
        <button class="btn-sync" onclick="verileriAnalizEt()">
            <i class="fas fa-sync"></i> CANLI VERİ SENKRONİZASYONU
        </button>
    </div>

    <!-- Üst Özet: Grup Bazlı -->
    <div class="grid-3">
        <div class="stat-card">
            <span style="font-size:12px; font-weight:700; color:#94a3b8;">1. GRUP DURUMU</span>
            <span class="stat-val" id="g1-active">0</span>
            <div id="g1-progress-text" style="font-size:11px;">Aktiflik Oranı: %0</div>
            <div class="progress-bar"><div id="g1-bar" class="progress-fill" style="width: 0%"></div></div>
        </div>
        <div class="stat-card">
            <span style="font-size:12px; font-weight:700; color:#94a3b8;">2. GRUP DURUMU</span>
            <span class="stat-val" id="g2-active" style="color:#8b5cf6;">0</span>
            <div id="g2-progress-text" style="font-size:11px;">Aktiflik Oranı: %0</div>
            <div class="progress-bar"><div id="g2-bar" class="progress-fill" style="width: 0%; background:#8b5cf6;"></div></div>
        </div>
        <div class="stat-card">
            <span style="font-size:12px; font-weight:700; color:#94a3b8;">ACİL TAKVİYE İHTİYACI</span>
            <span class="stat-val" id="total-urgent" style="color:var(--danger);">0</span>
            <span style="font-size:11px;" id="urgent-note">Kritik bir eksiklik bulunmuyor.</span>
        </div>
    </div>

    <div class="report-grid">
        <!-- Vardiya ve Posta Analizi -->
        <div class="report-section">
            <h3><i class="fas fa-clock"></i> Vardiya & Posta Dağılım Analizi</h3>
            <table>
                <thead>
                    <tr>
                        <th>Posta</th>
                        <th>Vardiya</th>
                        <th>İzinli</th>
                        <th>Raporlu</th>
                        <th>Takviye</th>
                    </tr>
                </thead>
                <tbody id="vardiya-body">
                    <!-- Dinamik Veri -->
                </tbody>
            </table>
        </div>

        <!-- Performans ve Geri Bildirim -->
        <div class="report-section">
            <h3><i class="fas fa-user-check"></i> Personel Geri Bildirim Özetleri</h3>
            <div id="feedback-summary" style="margin-top:15px;">
                <div style="background:rgba(255,255,255,0.03); padding:15px; border-radius:10px; margin-bottom:10px;">
                    <div style="font-size:12px; color:#94a3b8;">Bu Ay Toplam Bildirim</div>
                    <div style="font-size:24px; font-weight:800;" id="fb-total">0</div>
                </div>
                <div id="fb-list">
                    <!-- Liste JS ile gelecek -->
                </div>
            </div>
        </div>
    </div>

    <!-- Detaylı Grup Analizi -->
    <div class="report-section" style="margin-top:20px;">
        <h3><i class="fas fa-database"></i> M4 Mağazası Grup & Vardiya Detay Raporu</h3>
        <table>
            <thead>
                <tr>
                    <th>Grup</th>
                    <th>Vardiya Kırılımı</th>
                    <th>İzinli Sayısı</th>
                    <th>Raporlu Sayısı</th>
                    <th>Operasyonel Risk</th>
                    <th>Durum Notu</th>
                </tr>
            </thead>
            <tbody id="detail-body">
                <!-- Veriler JS ile gelecek -->
            </tbody>
        </table>
    </div>
</div>

<script>
async function verileriAnalizEt() {
    try {
        // 1. Verileri 'takipizin.html' dosyasından oku
        const resp = await fetch('takipizin.html');
        const html = await resp.text();
        const doc = new DOMParser().parseFromString(html, 'text/html');
        const rows = doc.querySelectorAll('#izinListesi tr');

        // Analiz Nesnesi
        let analiz = {
            g1: { izin:0, rapor:0, takviye:0, gunduz:0, gece:0 },
            g2: { izin:0, rapor:0, takviye:0, gunduz:0, gece:0 },
            postalar: {
                "1": { izin:0, rapor:0, takviye:0 },
                "2": { izin:0, rapor:0, takviye:0 },
                "3": { izin:0, rapor:0, takviye:0 },
                "11": { izin:0, rapor:0, takviye:0 },
                "12": { izin:0, rapor:0, takviye:0 }
            }
        };

        rows.forEach(row => {
            const cells = row.cells;
            if(!cells || cells.length < 5) return;

            const grup = cells[1].innerText;
            const posta = cells[1].innerText.replace(/[^0-9]/g, ''); // Sadece sayısal posta
            const tip = cells[4].innerText;
            const takviye = cells[3].innerText.includes('⚠️');
            
            // Grup Ayrımı
            let currentG = grup.includes("1") ? analiz.g1 : analiz.g2;
            
            // Vardiya Ayrımı (Basit mantık: Posta 3 ve 11 gece, diğerleri gündüz varsayalım)
            const isNight = (posta === "3" || posta === "11");
            if(isNight) currentG.gece++; else currentG.gunduz++;

            // İzin/Rapor Ayrımı
            if(tip.includes("Rapor")) currentG.rapor++; else currentG.izin++;
            if(takviye) currentG.takviye++;

            // Posta Bazlı Analiz
            if(analiz.postalar[posta]) {
                if(tip.includes("Rapor")) analiz.postalar[posta].rapor++;
                else analiz.postalar[posta].izin++;
                if(takviye) analiz.postalar[posta].takviye++;
            }
        });

        // UI Güncelleme
        updateUI(analiz);

    } catch (e) {
        console.error("Analiz Hatası:", e);
        alert("Veriler okunurken bir hata oluştu. Dosyaların aynı klasörde olduğundan emin olun.");
    }
}

function updateUI(data) {
    // Üst Kartlar
    document.getElementById('g1-active').innerText = data.g1.izin + data.g1.rapor + " Eksik";
    document.getElementById('g2-active').innerText = data.g2.izin + data.g2.rapor + " Eksik";
    document.getElementById('total-urgent').innerText = data.g1.takviye + data.g2.takviye;

    // Posta Tablosu
    let vHtml = "";
    Object.keys(data.postalar).forEach(p => {
        const vardiya = (p === "3" || p === "11") ? "Gece" : "Gündüz";
        vHtml += `
            <tr>
                <td><strong>Posta ${p}</strong></td>
                <td><span class="badge ${vardiya === 'Gece' ? 'bg-night' : 'bg-day'}">${vardiya}</span></td>
                <td>${data.postalar[p].izin}</td>
                <td>${data.postalar[p].rapor}</td>
                <td class="${data.postalar[p].takviye > 0 ? 'text-danger' : ''}">${data.postalar[p].takviye}</td>
            </tr>`;
    });
    document.getElementById('vardiya-body').innerHTML = vHtml;

    // Detay Tablosu
    document.getElementById('detail-body').innerHTML = `
        <tr>
            <td>1. GRUP</td>
            <td>Gündüz: ${data.g1.gunduz} / Gece: ${data.g1.gece}</td>
            <td>${data.g1.izin}</td>
            <td>${data.g1.rapor}</td>
            <td><span class="badge ${data.g1.takviye > 2 ? 'bg-danger':'bg-success'}">${data.g1.takviye > 2 ? 'KRİTİK':'STABİL'}</span></td>
            <td>${data.g1.rapor > 0 ? '⚠️ Raporlu personel takibi gerekiyor.' : 'Süreç normal.'}</td>
        </tr>
        <tr>
            <td>2. GRUP</td>
            <td>Gündüz: ${data.g2.gunduz} / Gece: ${data.g2.gece}</td>
            <td>${data.g2.izin}</td>
            <td>${data.g2.rapor}</td>
            <td><span class="badge ${data.g2.takviye > 2 ? 'bg-danger':'bg-success'}">${data.g2.takviye > 2 ? 'KRİTİK':'STABİL'}</span></td>
            <td>${data.g2.rapor > 0 ? '⚠️ Raporlu personel mevcut.' : 'Raporlu personel bulunmuyor.'}</td>
        </tr>
    `;

    // Feedback Simülasyonu (Bunu dosyanızdan da okuyabiliriz)
    document.getElementById('fb-total').innerText = "14";
    document.getElementById('fb-list').innerHTML = `
        <div style="font-size:13px; margin-bottom:5px;">• <strong>Kasa Hızı:</strong> 6 Personel <span class="text-success">▲</span></div>
        <div style="font-size:13px; margin-bottom:5px;">• <strong>Euro Satış:</strong> 4 Personel <span class="text-warning">●</span></div>
        <div style="font-size:13px;">• <strong>Genel Disiplin:</strong> 4 Personel <span class="text-danger">▼</span></div>
    `;
}

// Canlı Bilgi Güncelleme
setInterval(() => {
    const n = new Date();
    document.getElementById('live-info').innerText = n.toLocaleString('tr-TR') + " | Operasyonel Veri Akışı Aktif";
}, 1000);

window.onload = verileriAnalizEt;
</script>
</body>
</html>
