# QA-System-for-Coding

# Dev-Pulse — Kod Tabanlı Soru-Cevap & Dokümantasyon Asistanı

Dev-Pulse, yazılım repository’lerini **internet bağlantısı olmadan**, kodun yapısal bağlamını koruyarak analiz eden ve geliştiricilerin doğal dil üzerinden kod tabanına soru sorup **kaynak kanıtlı yanıt** almasını sağlayan bir yapay zekâ sistemidir.  
Mimari, **RAG (Retrieval-Augmented Generation)** pipeline’ı ve **reranking destekli bağlam seçimi** üzerine inşa edilmiştir.

---

## 🚩 Problem
- Yazılım projeleri büyüdükçe sınıf ve fonksiyon sayısı artar, bağımlılık zincirleri karmaşıklaşır.
- Dokümantasyon çoğu zaman kodla senkronize değildir ve güncelliğini kaybeder.
- Geliştiriciler kodu manuel taramak zorunda kaldığında **zaman kaybı ve bilişsel yük** oluşur.

Dev-Pulse, bu sorunu **kodun en güncel halini embedding uzayından çekip LLM’e bağlam sunarak** çözmeyi hedefler.

---

## 🎯 Amaç ve Kapsam
Dev-Pulse’ın hedefi, geliştirici için **kod farkındalığı yüksek bir soru-cevap asistanı** ve **otomatik README dokümantasyon üretici** sunmaktır.

Kapsam:
1. **Soru-Cevap:** Doğal dil ile sorulan teknik soruları yanıtlar.
2. **Otomatik Dokümantasyon:** Kod ve file-tree analizinden README üretir.
3. **Kanıtlı Analiz:** Yanıtlar doğrudan kod chunk’larına dayandırılır.
4. **Modüler Pipeline:** Yeni repo analizlerinde yeniden kullanılabilir.
5. **Otomatik Test:** Kodun farklı bölümlerini hedefleyen dinamik testler içerir.

---

## 🧠 Çözüm Yaklaşımı

### Neden RAG?
- Kod sürekli değişen dinamik bir yapıdır.
- Fine-tuning her kod güncellemesinde yeniden eğitim gerektirir, sürdürülebilir değildir.
- RAG’de ise model eğitilmez; **vektör tabanı güncellenir**, model en güncel chunk’tan yanıt üretir.

Pipeline özeti:
1. Repository yüklenir
2. Kod **regex ile class/def bazlı** chunk’lara ayrılır
3. Chunk’lar embedding modeli ile vektörleştirilir
4. Vektörler lokal ChromaDB koleksiyonuna yazılır
5. Query’de top-K en yakın bağlam çekilir
6. Reranking ile en alakalı chunk seçilip LLM’e verilir
7. LLM inference ile **tutarlı ve tekrarsız** cevap üretilir

---

## ⚙️ Kullanılan Teknolojiler

| Bileşen | Model / Teknoloji | Gerekçe |
|---|---|---|
| **LLM** | Qwen2.5-Coder-3B-Instruct (GGUF) | Kod odaklı, T4 VRAM’e sığabilir, instruction takip edebilir |
| **Embedding** | all-MiniLM-L6-v2 | Hafif, hızlı, semantik benzerlik araması için güçlü |
| **Vector DB** | ChromaDB | Serverless ve yerel diskte çalışabilir |
| **Arayüz** | Streamlit + Cloudflare Tunnel | Notebook’a web üzerinden erişim sağlar |
| **Chunking** | Regex tabanlı yapısal bölme | Bağlam bütünlüğünü korur |
| **Örnek Test Projesi** | HOG Görüntü İşleme ZIP’i | Pipeline’ın zorlayıcı koşullarda test edilmesi için kullanılmıştır |

---

📌 **HOG.zip**, sistemin test edildiği örnek proje olup repository’ye eklenmiştir. Bu dosya, Dev-Pulse’ın kod analiz ve retrieval pipeline’ını **gerçekçi ve zorlayıcı görüntü işleme bağlamı** üzerinde değerlendirmek amacıyla kullanılmıştır.

---

## 🧪 Test ve Performans (Notebook’tan elde edilen bulgular)
- **Vector DB retrieval süresi:** 0.02 saniye
- **Konum doğruluğu (MetaOK / Recall benzeri):** 1.00
- **LLM inference süresi:** 41 saniye (T4 GPU)
- **Cevap başına kod kanıt referansı:** 1.8 chunk

Bu sonuçlar, sistemin:
✔ stabil çalıştığını  
✔ bağlamı doğru çektiğini  
✔ yanıtları tekrarsız üretebildiğini  
✔ doğrudan kod kanıtına dayandığını  
göstermektedir.

---

## 🔐 Güvenlik
- API anahtarları `.env` üzerinden yönetilir
- Inference sırasında çıktı’ya sızdırılmaz
- Public repo kullanımıyla hassas veri riski düşük tutulmuştur

---

## 🚀 Geliştirme Süreci ve Sürdürülebilirlik

Dev-Pulse **aktif geliştirme aşamasındadır**.  
Planlanan iyileştirmeler proje akışını bozmadan sürdürülmektedir:

- Retrieval sıralama metrikleri (MRR, Hit@K) eklenecek
- Cevap doğruluk sınıflandırma testleri (F1, Precision, Recall) entegre edilecek
- VS Code / IDE extension’ı için paketleme yapılacak
- CI/CD dokümantasyon bot entegrasyonu geliştirilecek
- Bağlam penceresi, güçlü GPU’larda 32K+ token seviyesine ölçeklenecek
- Yanıt kalitesi, modüller arası reasoning ve refactor önerileri genişletilecek

---

## 📌 Sonuç
Dev-Pulse:
- **Yerel inference ile çalışabilen**
- **Kod bağlamını koruyan**
- **Semantik retrieval + reranking içeren**
- **Kanıtlı yanıt üretebilen**
- **Geliştirici yükünü azaltmaya yönelik realist bir RAG asistanı temeli** sunmaktadır.

---

## 📝 Changelog (Güncelleme Notları)

### v0.1.0 — İlk Stabil Sürüm
- RAG pipeline kuruldu
- Regex tabanlı chunking eklendi
- ChromaDB koleksiyonu oluşturuldu
- all-MiniLM embedding entegrasyonu yapıldı
- Qwen2.5-Coder-3B inference stabil çalışır hale getirildi
- HOG örnek projesi ile stres testleri tamamlandı

### v0.1.1 — Devam Eden İyileştirmeler
- Reranking mantığı güçlendirildi
- Otomatik test modülü genişletildi
- Yanıt tutarlılığı ve tekrar kontrolü optimize edildi

### v0.2.0 (Planlanan — Geliştirme Sürüyor)
- IDE extension sürümü yayınlanacak
- CI/CD bot entegrasyonu tamamlanacak

---

**Geliştirmeler aktif olarak devam etmektedir.**
