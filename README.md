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
    function randomDelay(min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    let stop = false;
    let unfollowedCount = 0;
    let batchCount = 0;
    const maxPerBatch = 10; // istirahat setiap 10 akun
    const pauseDuration = () => randomDelay(60_000, 120_000); // 1–2 menit

    console.log("🚀 Mulai unfollow massal mode AMAN + Bisa Tab-switch...");

    async function clickConfirmButton() {
        for (let i = 0; i < 50; i++) { // max 5 detik polling
            const confirmBtn = [...document.querySelectorAll('button')]
                .find(b => /Unfollow|Berhenti mengikuti|Batal mengikuti/i.test(b.innerText.trim()));
            if (confirmBtn) {
                confirmBtn.click();
                unfollowedCount++;
                console.log(`❌ Unfollow ke-${unfollowedCount}`);
                return true;
            }
            await delay(100); // cek setiap 100ms
        }
        console.warn("⚠ Tombol konfirmasi tidak ditemukan, lanjut akun berikutnya.");
        return false;
    }

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
                await delay(randomDelay(800, 1500));
                btn.click();

                await clickConfirmButton();
                await delay(randomDelay(2000, 5000)); // jeda antar unfollow

                batchCount++;
                if (batchCount >= maxPerBatch) {
                    let pauseTime = pauseDuration();
                    console.log(`⏸ Istirahat ${pauseTime / 1000} detik untuk menghindari limit...`);
                    await delay(pauseTime);
                    batchCount = 0;
                }
            } catch (err) {
                console.warn("⚠ Gagal unfollow:", err);
            }
        }

        window.scrollBy({ top: 1200, behavior: "smooth" });
        await delay(randomDelay(2000, 4000));
    }

    console.log(`🎯 Total akun di-unfollow: ${unfollowedCount}`);
})();




```
5. Tekan Enter dan tunggu proses selesai.
