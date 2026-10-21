# Kriptografi Yöntemleri

**Hazırlayan:** Tolgahan Keleş

Kriptografi; verinin **gizliliğini** (başkası okuyamasın), **bütünlüğünü** (değiştirilmesin)
ve **kimlik doğrulamasını** (gerçekten beklenen kişiden gelsin) sağlamak için kullanılır.
Aşağıda temel yöntemler birkaç cümle ile özetlenmiştir.

## 1. Simetrik Şifreleme

Şifreleme ve çözme için **aynı anahtar** kullanılır. Hızlıdır ve az kaynak tüketir; bu yüzden
mikrodenetleyicilerde ve İHA telemetri/komut hatlarında tercih edilir. En büyük sorunu,
anahtarın iki tarafa güvenli bir şekilde dağıtılmasıdır.

- **AES (Advanced Encryption Standard):** Günümüzün standart blok şifresidir. Veriyi 16 baytlık
  bloklar hâlinde işler ve 128, 192 veya 256 bitlik anahtar kullanır. Derste yapılan
  alıcı/verici örneğinde AES-128 kullanılmıştır.
- **DES / 3DES:** AES'ten önceki standartlardır. DES'in 56 bitlik anahtarı artık kolayca
  kırılabildiği için güvensiz kabul edilir; 3DES de yavaş olduğundan terk edilmektedir.
- **ChaCha20:** Bir akış şifresidir. Donanım AES hızlandırıcısı olmayan işlemcilerde
  AES'ten daha hızlı çalışabilir.

## 2. Blok Şifreleme Modları

AES tek başına yalnızca 16 baytlık bir bloğu şifreler; daha uzun veriler için bir **mod** gerekir.

- **ECB:** Her blok bağımsız şifrelenir. Aynı düz metin her zaman aynı şifreli metni verir,
  bu yüzden desenler görünür. Ders örneğinde basitlik için kullanılmıştır, gerçek sistemlerde önerilmez.
- **CBC:** Her blok bir önceki şifreli blokla karıştırılır ve rastgele bir IV kullanılır.
  ECB'deki desen sorununu çözer.
- **CTR:** AES'i bir sayaçla birlikte kullanarak akış şifresine dönüştürür; hızlıdır ve paralel çalışabilir.
- **GCM:** CTR moduna ek olarak bir doğrulama etiketi (tag) üretir. Böylece hem gizlilik hem
  bütünlük sağlar (AEAD). Kablosuz haberleşme için en uygun seçeneklerden biridir.

## 3. Asimetrik (Açık Anahtarlı) Şifreleme

Her tarafın bir **açık** ve bir **gizli** anahtarı vardır. Açık anahtarla şifrelenen veri yalnızca
gizli anahtarla çözülebilir. Simetrik şifrelemeye göre çok daha yavaştır; genellikle anahtar
değişimi ve dijital imza için kullanılır.

- **RSA:** Büyük sayıları çarpanlarına ayırmanın zorluğuna dayanır. Yaygındır ancak güvenli
  olması için büyük anahtarlar (2048 bit ve üzeri) gerekir.
- **ECC (Eliptik Eğri Kriptografisi):** RSA ile aynı güvenliği çok daha küçük anahtarlarla
  (ör. 256 bit) sağlar. Bu yüzden gömülü sistemler ve İHA'lar için daha uygundur.
- **Diffie-Hellman / ECDH:** İki tarafın güvensiz bir kanal üzerinden ortak bir gizli anahtar
  üretmesini sağlar. Bu anahtar daha sonra AES gibi simetrik bir şifre için kullanılabilir.

## 4. Özet (Hash) Fonksiyonları

Herhangi uzunluktaki veriden sabit uzunlukta bir "parmak izi" üretir. İşlem tek yönlüdür,
yani özetten asıl veri geri elde edilemez. Verinin değişip değişmediğini kontrol etmek için kullanılır.

- **SHA-256 / SHA-3:** Güncel ve güvenli kabul edilen özet algoritmalarıdır.
- **MD5 / SHA-1:** Çakışma (collision) saldırıları bulunduğu için artık güvenli değildir.

## 5. Mesaj Doğrulama ve Dijital İmza

- **HMAC:** Bir özet fonksiyonu ile gizli anahtarı birleştirir. Mesajın yolda değiştirilmediğini
  ve anahtara sahip birinden geldiğini doğrular.
- **Dijital imza (RSA, ECDSA, Ed25519):** Gönderen mesajı gizli anahtarıyla imzalar, herkes
  açık anahtarla doğrulayabilir. Örneğin yer istasyonundan gelen komutların veya bir yazılım
  güncellemesinin gerçekliğini kanıtlamak için kullanılır.
