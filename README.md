# zeka uçuş ✈️

Yerel Qwen3.8-27B NVFP4 ile üretilmiş, tek HTML dosyalı 2D uçak oyunu. Harici paket, görsel, CDN veya API gerektirmez.

[Oyna](https://zekayapar.github.io/qwen-ucak-oyunu/) · [Geliştirme yazısı](https://zekayapar.com/yazi/qwen-ucak-oyunu) · [Ölçüm kayıtları](benchmark/RESULTS.md)

WASD/ok tuşları ile hareket, boşluk ile ateş, P ile duraklatma. Dokunmatik ekranda parmağı sürükleyerek hareket ve otomatik ateş.

Yerel çalıştırma: `python3 -m http.server 18082 --bind 127.0.0.1`, ardından http://127.0.0.1:18082. Test: `node --test tools/check-game.mjs`.

## Kimin yaptığı ne?

Qwen oyun kodunu üretti. Codex promptları, inceleme geri bildirimlerini, ölçüm/test araçlarını, belgeleri ve repo entegrasyonunu hazırladı. Düzeltmeler Qwen’e yeniden yazdırıldı. Tam promptlar ve yanıtlar benchmark tur klasörlerinde; başarısız ilk yanıt da dahil.

MIT lisansı. Bir prototip; tek denemeden genel model kalitesi çıkarımı yapılmamalı.
