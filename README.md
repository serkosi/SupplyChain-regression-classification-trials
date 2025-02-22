# Random Forest ile Makine Öğrenmesi Uygulaması

Bu projede, Random Forest algoritmasını kullanarak iki farklı problemi ele alıyoruz:
- **Regresyon Görevi:** Toplam satın alma tutarını (TotalPrice) tahmin etmek.
- **Sınıflandırma Görevi:** Müşteri kaybını (churn) tahmin etmek.

Veri seti olarak 2010-2011 yılları arasındaki online perakende verileri kullanılmıştır.

## 1. Görev Tanımları

### Regresyon Görevi
- **Hedef Değişken:** Toplam Satın Alma Tutarı (TotalPrice)
  - **TotalPrice = Quantity × UnitPrice**
- **Amaç:**  
  Ülke, müşteri davranışları, ürün fiyatı ve adet gibi özelliklere göre müşterinin toplam harcama tutarını tahmin etmek. Bizim senaryomuzda ülkeler, tedarikci dagitim depolari olarak düsünülebilir.

### Sınıflandırma Görevi
- **Hedef Değişken:** Müşteri Kaybı (Churn) (1: Kaybedildi, 0: Kaybedilmedi)
- **Amaç:**  
  Müşterinin geçmiş satın alma verilerine dayanarak gelecekte tekrar sipariş verip vermeyeceğini tahmin etmek.

## 2. Veri Seti ve Özellikler

Veri setimiz, 2010-2011 yıllarına ait online perakende işlemlerini içeriyor. Ana sütunlar:
- **InvoiceNo:** Fatura numarası (benzersiz)
- **StockCode:** Ürün kodu
- **Description:** Ürün adı
- **Quantity:** Satın alınan ürün adedi
- **InvoiceDate:** İşlem tarihi
- **UnitPrice:** Ürün birim fiyatı
- **CustomerID:** Müşterinin benzersiz kimliği
- **Country:** Müşterinin ülkesi

### Veri Ön İşleme
- Eksik müşteri kayıtları temizlendi.
- Negatif veya sıfır Quantity ve UnitPrice değerleri çıkarıldı.
- TotalPrice değişkeni (Quantity × UnitPrice) hesaplandı.

## 3. Özellik Mühendisliği

- **Zaman Bilgisi:**  
  InvoiceDate’i ay bilgisine çevirip, sinüs (`Month_sin`) ve kosinüs (`Month_cos`) dönüşümleriyle işliyoruz. Bu sayede, yılın belirli aylarında oluşan satış artışları/düşüşleri gibi döngüsel etkileri yakalayabiliyoruz.

- **Country'nin Kodlanması:**  
  Country değişkeni, farklı ülkelerdeki satış trendlerini yakalayabilmek için kategorik bir değişken olarak sayısallaştırılıyor. Böylece model, hangi ülkede hangi davranışların öne çıktığını öğrenebiliyor.

## 4. Modelin Tedarik Zinciri Uygulamalarında Kullanımı

- **Satış Raporları:**  
  Model, belirli dönemlerdeki toplam satışları tahmin ederek, tedarikçilerin stok planlamasına yardımcı olabilir.

- **Talep Tahmini:**  
  Ülke bazında satış tahminleri yapılarak, dağıtım ve lojistik süreçleri optimize edilebilir.

- **Pazarlama Stratejileri:**  
  Müşteri kaybı tahminleri sayesinde, potansiyel kayıp yaşayan müşterilere yönelik özel kampanyalar düzenlenebilir ve promosyon stratejileri geliştirilebilir.

## 5. Değerlendirme

- **Regresyon için:**  
  Modelin performansı, RMSE (Root Mean Squared Error) kullanılarak ölçülür. Basit bir ortalama tahmini ile kıyaslanarak, modelin ne kadar geliştiği analiz edilir.

- **Sınıflandırma için:**  
  Doğruluk ve F1-skoru gibi metrikler kullanılarak modelin performansı değerlendirilir.

---

