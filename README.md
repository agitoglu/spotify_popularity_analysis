# spotify_popularity_analysis
# Spotify Müzik Popülerlik Analizi ve Finansal Optimizasyon 🎵📊
**Büyük Veri ile Şarkı Duygu Analizi ve Yapay Zeka Tabanlı ROI Projesi**

---

## 📌 1. Projenin Amacı ve Kapsamı
Bu proje, müzik platformlarının öneri algoritmalarının arkasındaki veriyi anlamak ve "Bir şarkıyı popüler yapan şey nedir? Yalnızca melodisi mi, sözlerinin duygusal tonu mu, yoksa ikisinin birleşimi mi?" sorusuna yanıt aramak amacıyla geliştirilmiştir. 

Projenin nihai hedefi, yalnızca teknik bir makine öğrenmesi modeli kurmak değil; bu modelin Spotify gibi büyük bir platforma ne kadar finansal değer (ROI) yaratabileceğini somut ve simüle edilmiş verilerle ortaya koymaktır.

---

## 📁 2. Veri Kaynakları ve Birleşim Süreci
Projede Kaggle üzerinden temin edilen iki bağımsız gerçek dünya veri seti birleştirilmiştir:

1. **900K Spotify Songs with Lyrics (Kaggle - devdope):** 120.000 şarkılık alt küme. Şarkı adı, sanatçı, ses özellikleri (danceability, energy, valence vb.) ve şarkı sözlerini içerir.
2. **Spotify Tracks Dataset (Kaggle - maharshi):** 114.000 şarkı ve 22 sütun. Şarkı adı, sanatçı, popülerlik (popularity) skoru, türü (genre) ve ses özelliklerini içerir.

### 🔗 Proje Veri Setleri
Projede kullanılan ham veri setlerine ve klasör yapısına aşağıdaki bağlantıdan ulaşabilirsiniz:
* 📂 [Google Drive - Proje Veri Setleri](https://drive.google.com/drive/folders/1AAerUUmEUrfZAc37FipNFyBHCapVFlA4)

### 🛠️ Veri Ön İşleme & Birleştirme
* Her iki veri setindeki şarkı adları ve sanatçı isimleri küçük harfe dönüştürülerek normalize edilmiştir.
* Çoklu sanatçı içeren kayıtlarda yalnızca ilk sanatçı baz alınmıştır.
* Veri setleri **şarkı adı + sanatçı** bazlı `inner join` yöntemiyle birleştirilerek **5.237 ortak kayıt (28 sütun)** elde edilmiştir.

---

## 🧠 3. Özellik Mühendisliği (Feature Engineering)
Ham verilerden iş mantığına uygun 4 yeni anlamlı değişken türetilmiştir:

1. **canlilik_endeksi:** `danceability * energy` -> Şarkının sahne enerjisini ve dans edilebilirliğini tek değerde toplar.
2. **duygu_skoru:** `valence` -> Şarkı sözü ve müzikten elde edilen duygu yoğunluğu ($0 = \text{melankolik}$, $1 = \text{neşeli}$).
3. **guvenir_popularite:** `popularity * log(1 + valence)` -> Ham popülerlik skorunu müzikal pozitifliğe göre ağırlıklandırarak daha güvenilir bir ölçüt sunar.
4. **dinlenme_suresi_tahmini:** `(duration_ms / 1000) * canlilik_endeksi` -> Enerjik şarkıların daha uzun süre dinleneceği varsayımıyla türetilmiştir.

> 💡 **Kritik Bulgu:** XAI (Açıklanabilir Yapay Zeka) permütasyon önem analizi sonucunda, bizim türettiğimiz **"guvenir_popularite"** değişkeni **0.224** önem skoru ile modelin açık ara en belirleyici değişkeni olmuştur.

---

## 🤖 4. Makine Öğrenmesi Modeli ve Bulgular
Popülerlik skoru 60 ve üzeri olan şarkılar "Popüler" olarak sınıflandırılmış, veri seti **%80 Eğitim / %20 Test** olarak bölünmüştür. Model olarak **Random Forest Classifier** (200 ağaç, max_depth=10) tercih edilmiştir.

### 📊 Model Performans Metrikleri
Model genel olarak **%95 doğruluk (Accuracy)** oranıyla çalışmaktadır.

| Metrik | Popüler Değil | Popüler | Genel (Ağırlıklı) |
| :--- | :---: | :---: | :---: |
| **Precision** | %94 | %99 | **%95** |
| **Recall** | %100 | %66 | **%95** |
| **F1-Score** | %97 | %79 | **%94** |
| **Örnek Sayısı** | 885 şarkı | 163 şarkı | 1.048 şarkı |

*Model "Bu şarkı popülerdir" dediğinde (Precision) %99 oranında doğru bilmektedir.*

### 🔍 XAI Analizi (Önem Skorları)
1. **guvenir_popularite:** 0.224 (Türetildi) -> En güçlü tahmin göstergesi.
2. **duygu_skoru:** 0.035 (Türetildi) -> Şarkının duygusal tonu popülerlikle doğrudan bağlantılı.
3. **valence:** 0.034 (Ham Veri) -> Müzikal pozitiflik, duygu skoru ile yüksek korelasyona sahip.
4. **canlilik_endeksi:** 0.008 (Türetildi) -> Anlamlı düzeyde modele katkı sağlamaktadır.

---

## 📈 5. Finansal Simülasyon ve ROI Analizi
**Senaryo:** Modelin, yüksek canlılık endeksine ve pozitif duygu skoruna sahip şarkıları ücretsiz kullanıcılara öncelikli önermesi sağlanmıştır. Bu kişiselleştirmenin premium aboneliğe geçiş oranını artırdığı simüle edilmiştir.

* **Toplam Spotify Kullanıcısı:** 500.000.000 (2024 Yıllık Raporu uyarınca)
* **Ücretsiz Kullanıcı Oranı:** %60 (Sektör Ortalaması - 300M Kullanıcı)
* **Aylık Premium Ücret:** \$9,99
* **Mevcut Geçiş Oranı:** %2,0
* **Model Sonrası Hedef Geçiş Oranı:** %3,5 (Artış: +%1,5 puan)

### 💰 Finansal Sonuçlar:
* **Ekstra Premium Kullanıcı:** 4.500.000 kişi
* **Aylık Ekstra Gelir:** \$44.955.000
* **Yıllık Potansiyel Ekstra Gelir:** **\$539.460.000**

---

## 🏁 6. Sonuç ve Proje Durumu
Bu çalışma, veri biliminin sadece matematikten ibaret olmadığını, ham verilere iş mantığı çerçevesinde anlam yükleyerek şirketler için nasıl milyonlarca dolarlık finansal değere dönüştürülebileceğini kanıtlayan uçtan uca bir büyük veri projesidir.

* **2 Farklı Veri Seti Birleşimi:** 🟢 Tamamlandı (5.237 Eşleşme)
* **Özellik Mühendisliği:** 🟢 Tamamlandı (4 Yeni Değişken)
* **Makine Öğrenmesi Modeli:** 🟢 Tamamlandı (Random Forest, %95 Başarı)
* **XAI Analizi:** 🟢 Tamamlandı (Permütasyon Önem Analizi)
* **Finansal Simülasyon:** 🟢 Tamamlandı (Yıllık \$539M+ Potansiyel)

---
*Python ile Veri Bilimi | Dönem Sonu Bitirme Projesi | 2024-2025*
