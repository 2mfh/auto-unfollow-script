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

    let stop = false;
    let unfollowedCount = 0;

    console.log("🚀 Mulai unfollow massal... Pastikan kamu ada di halaman 'following'.");

    while (!stop) {
        let buttons = [...document.querySelectorAll('button')]
            .filter(b => /Following|Diikuti/i.test(b.innerText));

        if (buttons.length === 0) {
            stop = true;
            console.log("✅ Sudah selesai, tidak ada tombol lagi.");
            break;
        }

        for (let btn of buttons) {
            btn.click();
            await delay(1000);

            let confirmBtn = [...document.querySelectorAll('button')]
                .find(b => /Unfollow|Berhenti mengikuti/i.test(b.innerText));
            if (confirmBtn) {
                confirmBtn.click();
                unfollowedCount++;
                console.log(`❌ Unfollow ke-${unfollowedCount}`);
                await delay(3000);
            }
        }

        window.scrollBy(0, 1000);
        await delay(2000);
    }

    console.log(`🎯 Total akun di-unfollow: ${unfollowedCount}`);
})();


```
5. Tekan Enter dan tunggu proses selesai.
