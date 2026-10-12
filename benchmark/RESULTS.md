# Qwen’e bir uçak oyunu yazdırmak — 13 Ekim 2026

Tarayıcıda çalışan 2D uçak oyunu. Oyun kodunu yerel Qwen3.8 üretti; Codex gereksinimleri/promptları yazdı, kodu inceledi, test ve ölçüm araçlarını hazırladı. Codex oyun kodunu doğrudan yazmadı; son iki değişiklik de Qwen’in kaydedilmiş JSON yamasıyla uygulandı.

## Tüm istekler

| Tur | Amaç | Girdi token | Çıktı token | Toplam token | İlk içerik ms | HTTP süresi sn | Sonuç |
|---|---|---:|---:|---:|---:|---:|---|
| 01-initial | İlk oyun | 656 | 4096 | 4752 | 2824.63 | 27.128 | length |
| 02-complete | Tam dosya | 709 | 6610 | 7319 | 279.32 | 39.914 | stop |
| 03-review-fixes | İnceleme düzeltmeleri | 7007 | 6864 | 13871 | 2489.88 | 39.858 | stop |
| 04-final-patch | İki küçük yama | 351 | 286 | 637 | 152.32 | 1.697 | stop |

Toplam **8723 girdi + 17856 çıktı = 26579 token**. Model HTTP çağrılarının toplamı **108.597 saniye**. Kesilen ilk yanıt dahil. Codex tokenları, insan/ajan inceleme süresi, uçtan uca proje geliştirme süresi ve elektrik tüketimi bu toplama dahil değil.

## Promptlar ve hatalar

Her tur klasöründe `request.json` gerçek system/user promptlarının tamamını, `response.md` ham yanıtı, `events.json` tüm SSE olaylarını, `metrics.json` kullanım ve süreleri içerir. 01: 4096 token sınırında kesildi, oyun olarak kullanılmadı. 02: 8192 bütçeyle tam dosya üretildi. 03: yeniden başlatmada input temizliği, P autorepeat, ekran dışı mermiler, küçük ekran/HUD, oyun bitişi ve reduced-motion incelemesi. 04: incelemenin kaçırdığı düğme Enter çift işlemesi ve can göstergesinin yeri için iki kesin yama. Ara sürümlerin index.html dosyaları 02 ve 03 altında korunur.

## Testler

Round 03 sonrasında Node VM testleri 11/12 geçti; native button Enter kontrolü başarısızdı. Round 04 sonrasında 12/12 geçti. Testler giriş temizliği, pause/resume, native button Enter, focus/visibility, pointer cancel, hareket/ateş, çarpışma/skor, ekran dışı mermi temizliği, oyun bitişi, storage hatası, resize/dt ve reduced motion içindir. Test-only VM instrumentation yalnız test aracında; yayımlanan oyun dosyasında gizli test arayüzü yok. Chrome’da başlangıç, oynama ekranı, duraklat/devam ayrıca kontrol edilir. Bu kontroller tam cihaz/tarayıcı erişilebilirlik veya kalite kabulü değildir.

## Ölçüm koşulları

Qwen3.8-27B NVFP4, GPU0/1 üzerinde 2×RTX5090/32GB, TP2/MTP3. Threadripper PRO5955WX. Üçüncü RTX5090’da coder ve embedding açık kaldı. NVIDIA595.99.02; Ubuntu26.04.1; kernel7.0.0-38-generic; vLLM0.30.0; PyTorch2.13.0+cu132; CUDA runtime13.2; FlashInfer0.6.18.post1; Transformers5.18.0. Model revision482ca0f3832238542f8f5295dde86b5f22711d80.

Bağlam16384,sequence10,batch2048,graph128,FlashInfer attention,BF16 KV,prefix cache açık. Cache sıfırlanmadı; üretim servisi üzerinde başka trafik kontrol edilmedi. Tek kodlama isteği sırayla gönderildi. Localhost8000 kullanıldı; gateway/HTTPS dahil değil. Temperature0.2,seed42,düşünme kapalı, doğal EOS; ignore_eos kullanılmadı. İstek bütçeleri4096/8192/8192/1024. Bunlar geliştirme isteklerinin bütçesi; müşteri API sınırını değiştirmedi. Oyunun Math.random davranışı seed42 ile sabitlenmedi.

TTFT ilk içerik parçasının istemciye ulaşması; toplam süre tüm HTTP akışı. `e2e_tokens_per_s=çıktı/HTTP süre`; `decode_tokens_per_s=(çıktı−1)/(son içerik−ilk içerik)` yaklaşık chunk ölçümüdür. MTP bir chunk’ta birden fazla token verebilir. Tek oyun/dört farklı istek; kontrollü hız karşılaştırması, coding sıralaması veya SLA değildir. Parasal API faturası yok; enerji ve amortisman ölçülmedi, maliyet sıfır iddiası yok.

## Tekrar çalıştırma

Oyunu `python3 -m http.server 18082 --bind 127.0.0.1` ile açıp http://127.0.0.1:18082 adresine girin. Test: `node --test tools/check-game.mjs` (Node22+). Yerel uyumlu vLLM’de herhangi bir kaydı tekrarlamak için request.json dosyasını POST /v1/chat/completions gövdesi olarak gönderin; response model adı deployment’a göre değişebilir. Tarihî kayıtların üstüne yazmayın. generate.py ilk4096 denemesini ölçen özgün araçtır; yeniden çalıştırmak 01 klasörünü değiştirir ve length durumunda bilinçli hata verir.
