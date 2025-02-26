# Random Forest ile Makine Öğrenmesi Uygulaması

Bu projede, Random Forest algoritmasını hem regresyon hem de sınıflandırma problemlerinde uyguluyoruz. Odak noktamız, Türkiye’deki farklı şehirlerdeki dağıtım depolarına sahip bir üretici firma. Bu firma, ürünlerini bayilerine satıyor; yani müşteriler, firmadan ürün alan bayiler oluyor.

## 1. Görev Tanımları

### Regresyon Görevi
- **Hedef Değişken:** **Satın Alma Miktari Trendi (Quantity_Trend)**
- **Amaç:**  
  Üretici firmanın, farklı şehirlerdeki dağıtım depolarından yapılan satış verilerine dayanarak bayilere yapılan toplam satış miktarini tahmin etmek. Bu sayede, hangi şehirdeki depo daha yüksek satış yapıyor, buna göre strateji belirlenebilir.
  
  Özellikle, günlük bazda hesaplanan Quantity (satın alınan ürün adedi) verisinin mevsimsel bileşenlerinden elde edilen trend kısmını tahmin etmek amaçlanmıştır. Böylece, satış miktarındaki genel eğilim incelenerek geleceğe yönelik öngörüler elde edilebilir.

### Sınıflandırma Görevi
- **Hedef Değişken:** **Bayi Kaybı (Churn)** (1: Bayi alışveriş yapmayı durdurdu, 0: Alışverişe devam ediyor)
- **Amaç:**  
  Bayilerin geçmiş satın alma verilerine dayanarak, gelecekte tekrar alışveriş yapıp yapmayacaklarını tahmin etmek. Böylece, kayıp riski yüksek bayilere yönelik önlemler alınabilir.

## 2. Veri Seti ve Degiskenler

Kullanılan veri seti, 2010-2011 yıllarına ait online perakende satış verilerinden uyarlanmıştır. Ancak senaryomuzda:
- **City:**  
  Veri setindeki “Country” değişkeni, artık üretici firmanın ürünlerini dağıttığı şehirleri temsil ediyor. Her satır, satışın belirli bir şehirdeki dağıtım deposundan yapıldığını gösterir.
- Diğer ana sütunlar:
  - **InvoiceNo:** Fatura numarası (benzersiz)
  - **StockCode:** Ürün kodu
  - **Description:** Ürün adı
  - **Quantity:** Satın alınan ürün adedi
  - **InvoiceDate:** İşlem tarihi
  - **UnitPrice:** Ürün birim fiyatı
  - **CustomerID:** Bayinin benzersiz kimliği

### Veri Ön İşleme
- Eksik bayi (CustomerID) kayıtları temizlendi.
- Negatif veya sıfır Quantity ve UnitPrice değerleri çıkarıldı.

## 3. Degisken Mühendisliği (Feature Engineering)

### Regresyon Modeli
- **Günlük Toplamlar:**  
  Veri seti, sadece sayısal sütunlar seçilerek günlük bazda (resample('D')) toplam değerler elde edilecek şekilde yeniden düzenlendi.

- **Mevsimsel Ayristirma (Seasonal Decompose):**  
  - Quantity ve UnitPrice sütunları, 30 günlük periyotlarla mevsimsel ayrıştırma işlemine tabi tutuldu.
  - Ayrıştırma sonucunda, özellikle Quantity için elde edilen trend bileşeni, modelin hedef değişkeni olarak belirlendi.

- **City (Dağıtım Deposu):**  
  City değişkeni, üretici firmanın farklı şehirlerdeki dağıtım depolarını temsil eder. Kategorik bir değişken olarak sayısallaştırılarak modele dahil edilir.  
  *Neden?* Böylece model, farklı şehirlerdeki satış trendlerini öğrenebilir ve bölge bazlı performans analizleri yapılabilir.

  ### Siniflandirma Modeli
  - **Kategorik Degiskenin Kodlanması:**  
  Country sütunu, LabelEncoder kullanılarak sayısal forma dönüştürülmüştür.

- **Müşteri Düzeyinde Aggregasyon:**
  Müşteriler (CustomerID) bazında aşağıdaki metrikler hesaplanmıştır:
  - TotalSpend: Toplam harcama (TotalPrice toplamı)
  - OrderCount: Benzersiz fatura sayısı (alışveriş sayısı)
  - AvgOrderValue: Ortalama fatura değeri
  - Country: Müşterinin ait olduğu ülke (kodlanmış)
  - LastPurchase: En son satın alma tarihi

- **Churn Tanımlama:**  
  - Veri setindeki en son tarih referans alınarak, her müşterinin en son satın alma tarihi ile arasındaki fark hesaplandı.
  - 180 günden fazla süredir alışveriş yapmayan müşteriler churn (1), aksi halde churn (0) olarak etiketlendi.

## 4. Model Eğitimi ve Değerlendirmesi

Degiskenler ve hedef değişken, her iki model icin de, %80 eğitim ve %20 test olarak bölündü.
### Regresyon Modeli
- **Model:** Random Forest Regressor
- **Hedef:** Quantity Trend (Günlük toplam Quantity verisinin mevsimsel ayrıştırmadan elde edilen trend bileşeni)
- **Kullanılan Degiskenler:**  
  - Ham değerler: UnitPrice  
  - Zaman tabanlı degiskenler: Month, WeekOfYear
- **Hiperparametreler:**  
  - n_estimators = 100  
  - random_state = 42  
- **Değerlendirme:**  
  Model performansı, RMSE (Root Mean Squared Error) kullanılarak ölçülür. Ayrıca, basit bir ortalama tahmini (baseline) ile karşılaştırılır.

### Siniflandirma Modeli
- **En Iyi Model Secimi** 
  - Hiperparametre optimizasyonu sonucunda en iyi performansı gösteren Random Forest sınıflandırma modeli seçildi.
- **Performans Ölcütleri** 
  - Accuracy (Doğruluk)
  - Classification Report: Precision, Recall, F1-score gibi metrikler kullanılarak modelin sınıflandırma başarısı değerlendirildi.

## 5. Modelin Tedarik Zinciri Uygulamalarında Kullanımı

- **Satış Raporları ve Stok Yönetimi:**  
  Modelimiz, farklı şehirlerdeki dağıtım depolarından yapılan satış miktarlarini tahmin ederek, tedarikçilerin stok planlamasında yardımcı olabilir. Örneğin, belirli bir şehirde satışların artması bekleniyorsa, o bölge için daha fazla stok tutulabilir.

- **Bayi Yönetimi ve Pazarlama:**  
  Müşteri kaybı tahminleri sayesinde, bayilerin alışveriş yapma olasılığı değerlendirilebilir. Böylece, kayıp riski yüksek bayilere özel kampanyalar düzenlenerek müşteri ilişkileri güçlendirilebilir.

- **Dinamik Fiyatlandırma:**  
  Bölge bazında satış trendlerine göre, fiyatlandırma stratejileri geliştirilebilir. Örneğin, talebin yoğun olduğu dönemlerde fiyatlar optimize edilerek kâr artırılabilir.

## 6. Değerlendirme

- **Regresyon için:**  
  Modelin performansı, RMSE ile ölçülür. Basit bir ortalama tahmini ile kıyaslanarak modelin ne kadar geliştiği analiz edilir.
- **Sınıflandırma için:**  
  Doğruluk, F1-skoru gibi metrikler kullanılarak model performansı değerlendirilir.

Temel fark; regresyon sürekli değişkenleri tahmin etmeye, sınıflandırma ise verileri belirli sınıflara ayırmaya yöneliktir.

---

Bu proje, Random Forest algoritmasını kullanarak, üretici firmanın farklı şehirlerdeki dağıtım depolarından yapılan satışları (TotalPrice) tahmin etmeyi ve bayilerin alışveriş davranışlarını sınıflandırmayı amaçlamaktadır. Böylece, tedarik zinciri yönetimi, pazarlama stratejileri ve stok planlaması gibi alanlarda daha bilinçli kararlar alınabilir.
