# auto-unfollow-script

Script JavaScript untuk menghapus koneksi atau "unfollow" secara massal di halaman *following* akun media sosial, dijalankan langsung dari **Console** browser.

⚠️ **Disclaimer**:  
Gunakan script ini hanya untuk akun pribadi dan dengan risiko Anda sendiri. Melakukan aksi massal berpotensi membuat akun terkena pembatasan sementara oleh platform terkait.

---

## 📦 Fitur
- Unfollow otomatis di halaman *following*
- Scroll otomatis untuk memuat akun baru
- Jeda antar unfollow untuk mengurangi risiko limit

---

## 🛠 Cara Pakai
1. Buka halaman **Following** akun media sosial Anda (misalnya Instagram).
2. Tekan `F12` atau `Ctrl+Shift+I` (Windows) / `Cmd+Opt+I` (Mac) untuk membuka Developer Tools.
3. Masuk ke tab **Console**.
4. Tempelkan script berikut:

```javascript
(async () => {
    function delay(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }
    
    function randomDelay(base = 1000, variance = 500) {
        return base + Math.floor(Math.random() * variance);
    }

    let stop = false;
    let unfollowedCount = 0;
    let batchCount = 0;
    const maxPerBatch = 10; // berhenti sementara setiap 10 unfollow
    const pauseDuration = 60 * 1000; // 1 menit

    console.log("🚀 Mulai unfollow massal... Pastikan kamu ada di halaman 'following'.");

    while (!stop) {
        let buttons = [...document.querySelectorAll('button')]
            .filter(b => /Following|Diikuti/i.test(b.innerText.trim()));

        if (buttons.length === 0) {
            stop = true;
            console.log("✅ Sudah selesai, tidak ada tombol lagi.");
            break;
        }

        for (let btn of buttons) {
            try {
                btn.scrollIntoView({ behavior: "smooth", block: "center" });
                await delay(randomDelay(800, 400));
                btn.click();
                await delay(randomDelay(800, 400));

                let confirmBtn = [...document.querySelectorAll('button')]
                    .find(b => /Unfollow|Berhenti mengikuti/i.test(b.innerText.trim()));
                
                if (confirmBtn) {
                    confirmBtn.click();
                    unfollowedCount++;
                    batchCount++;
                    console.log(`❌ Unfollow ke-${unfollowedCount}`);

                    // pause kalau sudah batch limit
                    if (batchCount >= maxPerBatch) {
                        console.log(`⏸ Istirahat ${pauseDuration / 1000} detik untuk menghindari limit...`);
                        batchCount = 0;
                        await delay(pauseDuration);
                    }
                    await delay(randomDelay(2000, 1000));
                }
            } catch (err) {
                console.warn("⚠ Gagal unfollow:", err);
            }
        }

        window.scrollBy({
            top: 1000,
            behavior: "smooth"
        });
        await delay(randomDelay(2000, 1000));
    }

    console.log(`🎯 Total akun di-unfollow: ${unfollowedCount}`);
})();



```
5. Tekan Enter dan tunggu proses selesai.
