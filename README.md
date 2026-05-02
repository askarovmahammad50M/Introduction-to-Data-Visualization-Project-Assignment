# 🍽️ FoodRadar AI

## Ollama Destekli Yemek Fiyat Karşılaştırma ve Veri Görselleştirme Uygulaması

**FoodRadar AI**, kullanıcının aradığı bir yemeği farklı yemek sipariş platformları arasında karşılaştıran, fiyatları ucuzdan pahalıya sıralayan ve sonuçları grafiklerle görselleştiren modern bir masaüstü uygulamasıdır.

Proje; **Trendyol Go**, **Yemeksepeti** ve **GetirYemek** platformlarını örnek alarak çalışır. Kullanıcı bir yemek adı girdiğinde uygulama, platformlara göre ürün fiyatı, teslimat ücreti, hizmet bedeli, indirim, toplam fiyat, restoran puanı ve tahmini teslimat süresi gibi değerleri analiz eder.

Ayrıca proje, **Ollama AI** desteği sayesinde fiyat karşılaştırma sonuçlarını yapay zekâ ile yorumlayabilir. Böylece kullanıcı yalnızca fiyat listesini değil, aynı zamanda hangi seçeneğin daha mantıklı olduğunu açıklayan akıllı bir analiz de görebilir.

---

## 📌 Proje Adı

```text
FoodRadar AI
🎯 Projenin Amacı

Günümüzde aynı yemek farklı yemek sipariş uygulamalarında farklı fiyatlara sahip olabilmektedir. Kullanıcılar genellikle en uygun fiyatı bulmak için birden fazla uygulamayı ayrı ayrı kontrol etmek zorunda kalır.

FoodRadar AI bu problemi çözmek amacıyla geliştirilmiştir.

Projenin temel amacı:

Kullanıcının aradığı yemeği analiz etmek
Farklı yemek sipariş uygulamalarındaki fiyatları karşılaştırmak
En ucuz seçeneği otomatik olarak belirlemek
Toplam sepet maliyetini hesaplamak
Sonuçları tablo ve grafiklerle görselleştirmek
Ollama destekli yapay zekâ ile kullanıcıya karar desteği sunmak

Bu yönüyle proje yalnızca basit bir fiyat listeleme uygulaması değil; aynı zamanda veri analizi, veri görselleştirme, karar destek sistemi ve yerel yapay zekâ entegrasyonu içeren kapsamlı bir öğrenci projesidir.

🧠 Proje Fikri

FoodRadar AI, temel olarak “Akakçe benzeri fiyat karşılaştırma” mantığının yemek sipariş uygulamalarına uyarlanmış halidir.

Akakçe gibi platformlar ürün fiyatlarını karşılaştırırken, FoodRadar AI yemek sipariş platformlarındaki yemek fiyatlarını karşılaştırır.

Örneğin kullanıcı:

lahmacun

yazdığında uygulama bu yemeği şu platformlarda karşılaştırır:

Trendyol Go
Yemeksepeti
GetirYemek

Daha sonra sonuçları en ucuzdan en pahalıya doğru sıralar.

🖥️ Uygulama Özellikleri

FoodRadar AI aşağıdaki özelliklere sahiptir:

Modern koyu tema arayüz
Kullanıcı dostu masaüstü uygulaması
Yemek adı ile arama yapabilme
Seçili metni F8 tuşu ile otomatik analiz etme
Üç farklı yemek sipariş platformu karşılaştırması
Ürün fiyatı, teslimat ücreti ve hizmet bedeli analizi
İndirimleri hesaba katan toplam fiyat hesaplama
Ucuzdan pahalıya otomatik sıralama
En ucuz seçeneği vurgulama
Toplam fiyatları grafikle görselleştirme
Ortalama fiyat, en ucuz fiyat, en pahalı fiyat ve fiyat farkı hesaplama
Ollama AI ile akıllı yorum üretme
Tek dosya üzerinden kolay çalıştırılabilir yapı
🚀 Kullanılan Teknolojiler
Teknoloji	Kullanım Amacı
Python	Ana programlama dili
Tkinter	Masaüstü arayüz tasarımı
Canvas	Grafik çizimi ve veri görselleştirme
Ollama	Yerel yapay zekâ analizi
Requests	Ollama API ile iletişim
Pyperclip	Panodan veri okuma
PyAutoGUI	Seçili metni kopyalama
Pynput	F8 klavye kısayolu
JSON	AI modeline veri gönderme
Random	Demo fiyat verisi üretme
Threading	Arayüz donmadan AI işlemi yapma
🤖 Ollama AI Desteği

Projede Ollama desteği bulunmaktadır. Ollama sayesinde yapay zekâ modeli bilgisayar üzerinde yerel olarak çalışır.

Uygulama, fiyat karşılaştırma sonucunu Ollama modeline gönderir ve modelden kısa, profesyonel ve anlaşılır bir analiz ister.

AI analiz bölümü şu konularda yorum yapabilir:

En ucuz platform hangisidir?
En pahalı platform ile fiyat farkı ne kadardır?
Teslimat süresi fiyat avantajını etkiliyor mu?
Restoran puanı seçimi değiştirebilir mi?
Kullanıcı için en mantıklı tercih hangisidir?
🧩 Ollama Model Mantığı

Uygulama sistemde yüklü Ollama modellerini kontrol eder. Aşağıdaki modellerden biri varsa otomatik olarak kullanmaya çalışır:

gemma3:1b
llama3.2
mistral
qwen2.5:3b
gemma3

Eğer bu modeller yoksa, bilgisayarda kurulu olan ilk Ollama modelini kullanmayı dener.

Önerilen hafif model:

ollama pull gemma3:1b

Alternatif model:

ollama pull llama3.2
📊 Veri Görselleştirme Yaklaşımı

Bu proje, Introduction to Data Visualization dersi kapsamında geliştirildiği için veri görselleştirme kısmı özellikle önemlidir.

Uygulamada fiyat verileri yalnızca tablo halinde gösterilmez. Aynı zamanda toplam sepet maliyetleri grafikle görselleştirilir.

Grafik üzerinde:

Platform isimleri
Toplam fiyatlar
En ucuz seçenek
Platformlar arası fiyat farkı
Minimum ve maksimum fiyat bilgisi

görülebilir.

Bu sayede kullanıcı, hangi platformun daha uygun olduğunu yalnızca sayısal olarak değil, görsel olarak da hızlıca anlayabilir.

🧮 Hesaplama Sistemi

Uygulamada her platform için toplam fiyat şu şekilde hesaplanır:

Toplam Fiyat = Ürün Fiyatı + Teslimat Ücreti + Hizmet Bedeli - İndirim

Örnek:

Ürün Fiyatı: 120 TL
Teslimat Ücreti: 15 TL
Hizmet Bedeli: 5 TL
İndirim: 20 TL

Toplam Fiyat = 120 + 15 + 5 - 20
Toplam Fiyat = 120 TL

Bu hesaplama sonucunda tüm platformlar toplam fiyata göre sıralanır.

📈 Analiz Edilen Değerler

FoodRadar AI aşağıdaki değerleri analiz eder:

Değer	Açıklama
Ürün Fiyatı	Yemeğin platformdaki temel fiyatı
Teslimat Ücreti	Siparişin teslimat maliyeti
Hizmet Bedeli	Platformun aldığı ek hizmet bedeli
İndirim	Kampanya veya kupon indirimi
Toplam Fiyat	Kullanıcının ödeyeceği nihai fiyat
Restoran Puanı	Sipariş verilen restoranın puanı
Teslimat Süresi	Tahmini teslimat süresi
Fiyat Farkı	En ucuz seçeneğe göre fark
📦 Desteklenen Yemek Örnekleri

Uygulama demo veri setinde birçok yemek türünü destekler.

Örnek aramalar:

lahmacun
pizza
hamburger
döner
tavuk döner
et döner
çiğ köfte
mantı
kebap
pide
sushi
makarna
tost
köfte
baklava

Kullanıcı farklı bir yemek adı girerse sistem yine benzer bir fiyat karşılaştırması oluşturabilir.

🏗️ Sistem Mimarisi

FoodRadar AI dört temel katmandan oluşur.

1. Kullanıcı Arayüzü Katmanı

Bu katman Tkinter ile geliştirilmiştir. Kullanıcı yemek adı girer, sonuçları tablo ve grafik halinde görür.

Arayüzde:

Arama kutusu
Karşılaştırma butonu
AI analiz butonu
Sonuç tablosu
Grafik alanı
AI yorum alanı

bulunur.

2. Veri Üretim Katmanı

Proje eğitim amaçlı olduğu için gerçek platform API’leri kullanılmaz. Bunun yerine demo amaçlı kontrollü fiyat verileri üretilir.

Bu veri üretim sistemi, aynı yemek adı için benzer ve tutarlı sonuçlar üretir. Böylece proje sunumunda uygulama kararlı şekilde çalışır.

3. Analiz Katmanı

Bu katmanda platformlardan gelen fiyat verileri işlenir.

Analiz katmanı:

Toplam fiyatı hesaplar
Fiyatları ucuzdan pahalıya sıralar
En ucuz seçeneği belirler
Ortalama fiyatı hesaplar
Fiyat farkını hesaplar
Sonuçları grafik için hazırlar
4. Yapay Zekâ Katmanı

Bu katman Ollama API ile çalışır. Uygulama, analiz edilen fiyat verilerini JSON formatında Ollama modeline gönderir.

Model, kullanıcıya kısa ve anlaşılır bir karar desteği sunar.

🔄 Uygulama Akışı

Uygulamanın çalışma mantığı aşağıdaki gibidir:

Kullanıcı yemek adı girer
        ↓
Sistem yemek adını analiz eder
        ↓
Platformlara göre fiyat verisi oluşturulur
        ↓
Toplam fiyat hesaplanır
        ↓
Sonuçlar ucuzdan pahalıya sıralanır
        ↓
Tablo ve grafik güncellenir
        ↓
Kullanıcı isterse Ollama AI analizi alır
⌨️ F8 Kısayol Özelliği

FoodRadar AI’ın en dikkat çekici özelliklerinden biri F8 kısayoludur.

Kullanıcı herhangi bir yerde bir yemek adını seçip F8 tuşuna bastığında uygulama seçili metni alır ve otomatik olarak arama yapar.

Örnek:

Kullanıcı herhangi bir metinde pizza kelimesini seçer.
Klavyeden F8 tuşuna basar.
FoodRadar AI açılır.
Pizza fiyatları platformlara göre sıralanır.

Bu özellik uygulamayı daha pratik ve profesyonel hale getirir.

📁 Proje Dosya Yapısı

Proje klasörü aşağıdaki dosyalardan oluşur:

FoodRadar-AI/
│
├── main.pyw
├── requirements.txt
├── BASLAT.bat
├── kurulum.bat
└── README.md
📄 Dosyaların Görevleri
Dosya	Görevi
main.pyw	Ana uygulama kodu
requirements.txt	Gerekli Python paketleri
BASLAT.bat	Uygulamayı başlatan dosya
kurulum.bat	Sanal ortam ve paket kurulum dosyası
README.md	Proje açıklama dosyası
