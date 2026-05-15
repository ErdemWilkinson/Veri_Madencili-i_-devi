# Veri_Madenciligi_odevi


[446764_450657.zip](https://github.com/user-attachments/files/27655883/446764_450657.zip)

<img width="1553" height="853" alt="image" src="https://github.com/user-attachments/assets/b2c42250-70f7-41a0-85c7-70136f661a71" />

🎯 Bu Proje Ne İşe Yarıyor?
Bu proje, bir telekom şirketinin hangi müşterilerinin aboneliğini iptal edeceğini önceden tahmin etmek amacıyla geliştirilmiştir.
Gerçek hayatta bir telekom şirketinin binlerce müşterisi vardır. Bu müşterilerin bir kısmı zamanla aboneliğini iptal eder — buna churn (müşteri kaybı) denir. Şirket bu müşterileri önceden tespit edebilirse, onlara özel kampanya veya indirim sunarak kaybı önleyebilir.
Yeni bir müşteri kazanmak, mevcut müşteriyi elde tutmaktan 5-7 kat daha pahalıdır. Bu yüzden churn tahmini iş dünyasında kritik bir öneme sahiptir.
Bu proje; müşterilerin yaşı, sözleşme tipi, aylık ödedikleri ücret, kullandıkları hizmetler gibi bilgileri analiz ederek "Bu müşteri ayrılacak mı?" sorusuna yanıt üretir.

🔄 Adımlar Nasıl İşliyor?
1. Veri Yükleme
İlk adımda telecom-customer-churn.xlsx adlı veri seti projeye yüklenir. Bu veri setinde her satır bir müşteriyi, her sütun ise o müşteriye ait bir özelliği temsil eder. Cinsiyet, abonelik süresi, internet hizmeti türü, aylık ücret ve en önemlisi Churn (ayrıldı mı?) bilgisi bu sütunlar arasındadır. Ham veri, Data Table nodunda görüntülenerek içeriği kontrol edilir.

2. Eksik Veri Doldurma (Impute)
Gerçek dünya verilerinde bazı müşterilerin bilgileri eksik olabilir. Örneğin toplam ücret sütununda boş değerler bulunabilir. Bu eksik değerler olduğu gibi bırakılırsa model hata verir. Impute adımında bu boş değerler uygun istatistiksel yöntemlerle (ortalama, mod vb.) doldurulur. Doldurma işlemi sonrası veri tekrar Data Table ile kontrol edilir.

3. Özellik Seçimi (Select Columns)
Veri setindeki tüm sütunlar modele sokulmamalıdır. Örneğin müşteri ID numarası tamamen rastgele bir değerdir ve churn tahminiyle hiçbir ilgisi yoktur. Bu adımda anlamsız sütunlar çıkarılır, Churn sütunu hedef değişken olarak belirlenir ve geri kalan anlamlı sütunlar modele girdi olarak atanır.

4. Keşifsel Veri Analizi (Distributions + Scatter Plot)
Model kurulmadan önce veri görsel olarak incelenir.
Dağılım Grafikleri (Distributions) ile her değişkenin Churn'e göre nasıl dağıldığı incelenir. Örneğin ay-ay sözleşmeli müşterilerin churn oranının yıllık sözleşmelilere göre çok daha yüksek olduğu bu aşamada görülür.
Nokta Bulutu (Scatter Plot) ile aylık ücret ve abonelik süresi arasındaki ilişki görselleştirilir. Kısa süreli aboneliği olan ve yüksek ücret ödeyen müşterilerin churn'e çok daha yatkın olduğu gözlemlenir.

5. Veri Bölme (Data Sampler)
Model hem eğitmek hem de test etmek için aynı veri kullanılamaz — bu modelin veriyi ezberlemesine yol açar. Bu nedenle veri ikiye bölünür:

%80 Eğitim Verisi: Modelin öğrenmesi için kullanılır
%20 Test Verisi: Modelin hiç görmediği verilerle sınanması için kullanılır

Bölme işlemi dengeli (stratified) yapılır, yani her iki kümede de churn oranı benzer oranda korunur. Sabit seed kullanılarak sonuçların her çalıştırmada aynı olması sağlanır.

6. Model Eğitimi
Aynı eğitim verisi üç farklı algoritmaya paralel olarak gönderilir ve her biri churn tahmini için eğitilir.
Lojistik Regresyon, en basit modeldir ve referans noktası olarak kullanılır. Her özelliğe bir ağırlık atayarak müşterinin churn etme olasılığını hesaplar. L2 düzenlileştirme ile modelin aşırı öğrenmesi önlenmiştir.
Karar Ağacı, evet/hayır sorularıyla çalışır. Örneğin "Sözleşme ay-ay mı? → Evet → Abonelik süresi 12 aydan az mı? → Evet → Churn = Yes" şeklinde adım adım karar üretir. En büyük avantajı görselleştirilebilir olmasıdır; Tree Viewer ile ağacın her dalı incelenebilir.
Rastgele Orman, 10 farklı karar ağacının bir arada çalışmasıyla oluşur. Her ağaç farklı bir veri alt kümesiyle eğitilir ve sonunda tüm ağaçların oyları birleştirilerek final karar verilir. Tek ağaca göre çok daha güçlü ve aşırı öğrenmeye karşı dayanıklıdır.

7. Model Değerlendirme (Test and Score)
Üç modelin performansı aynı test verisiyle ölçülür ve yan yana karşılaştırılır. AUC, Doğruluk, F1 ve Recall gibi metrikler hesaplanır.
Bu projede en önemli metrik Recall (Duyarlılık)'tır. Çünkü churn edecek bir müşteriyi kaçırmak — yani yanlış "kalmaya devam edecek" demek — müşterinin tamamen kaybedilmesine yol açar. Bu, yanlış alarm vermekten çok daha maliyetli bir hatadır.

8. Karmaşıklık Matrisi (Confusion Matrix)
Modelin yaptığı tahminler dört kategoriye ayrılarak incelenir:

Doğru Negatif: Ayrılmayacak dedi, ayrılmadı ✅
Doğru Pozitif: Ayrılacak dedi, ayrıldı ✅
Yanlış Pozitif: Ayrılacak dedi, ayrılmadı ⚠️ (gereksiz kampanya maliyeti)
Yanlış Negatif: Ayrılmayacak dedi, ayrıldı ❌ (müşteri tamamen kaybedildi)

Bu matris sayesinde modelin nerede hata yaptığı somut olarak görülür ve iş kararları buna göre şekillendirilir.

💡 Bu İşlemlerle Neler Elde Edildi?
Projenin sonunda üç farklı makine öğrenmesi modelinin performansı karşılaştırılmış ve hangisinin churn tahmininde daha başarılı olduğu belirlenmiştir.
Analiz sonucunda şu bulgular elde edilmiştir:

Ay-ay sözleşmeli müşteriler uzun vadeli sözleşmelilere göre çok daha yüksek churn riski taşımaktadır
Kısa abonelik süresi ile yüksek aylık ücretin bir arada bulunması en güçlü churn göstergesidir
Fiber optik internet kullanan müşterilerde churn oranı DSL kullanıcılarına kıyasla daha yüksektir

Bu bulgular sayesinde telekom şirketi, riskli müşteri segmentlerini önceden tespit edebilir ve hedefli müşteri tutma stratejileri geliştirebilir.
