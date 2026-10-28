# token-learning-platform



\# SENG 321 - Token Learning Platform (Geliştirici İş Akışı Rehberi)



Bu doküman, projenin \*\*TEK\*\* geçerli çalışma kural setidir. Ekipteki herkesin kod yazarken, commit atarken ve görev teslim ederken aşağıdaki 5 adımı eksiksiz uygulaması zorunludur. Kurallara uymayan kodlar (Pull Request'ler) incelenmeden reddedilecektir.



\---



\## ADIM 1: Görev Alma ve Numarasını Öğrenme (GitHub Projects)

Hiçbir kod veya doküman, bir GitHub Görevi (Issue) olmadan yazılamaz.

1\. GitHub repomuzdaki üst menüden \*\*Projects\*\* sekmesine girin.

2\. `Sprint 1 - Token Learning Platform` tahtasını açın.

3\. Adınıza atanmış görevi bulun ve \*\*Todo\*\* kolonundan \*\*In Progress\*\* kolonuna sürükleyin.

4\. \*\*ÇOK ÖNEMLİ:\*\* Görevinizin başlığına tıklayın. Açılan pencerede başlığın hemen yanında bir numara göreceksiniz (Örn: `#3`, `#5`). \*\*Bu numara sizin Issue (Görev) numaranızdır ve commit atarken bunu kullanmak zorundasınız.\*\*



\---



\## ADIM 2: Çalışma Dalı (Branch) Açma

Projede \*\*Git-Flow\*\* mimarisi kullanılmaktadır.

\* ⛔ \*\*YASAK:\*\* `main` veya `develop` dalına doğrudan kod göndermek (push atmak) sistem tarafından engellenmiştir.

\* ✅ \*\*DOĞRU:\*\* Her iş, güncel `develop` dalından türetilmiş bir `feature/` dalında yapılmalıdır.



\*\*Terminal Komutları (Sırasıyla Yazın):\*\*

```bash

\# 1. Geliştirme dalına geçin

git checkout develop



\# 2. En güncel kodları bilgisayarınıza çekin

git pull origin develop



\# 3. Kendi göreviniz için yeni bir dal açın (USXX ve kısa ad)

git checkout -b feature/USXX-kisa-aciklama



(Örnek doğru dal adı: git checkout -b feature/US03-frontend-auth)

ADIM 3: ZORUNLU FORMAT (Conventional Commits)



Yaptığınız işi kaydederken rastgele veya Türkçe mesajlar ("hata çözüldü", "update", "kod eklendi" vb.) YAZILAMAZ. Her commit, adım 1'de bulduğunuz Görev Numarası ile bitmek zorundadır.



Zorunlu Commit Formatı: tip: ingilizce kısa açıklama (Closes #GörevNumarası)



Kullanılması Gereken Tipler (Types):



&#x20;   feat: Yeni bir kod/özellik eklendiğinde kullanılır.



&#x20;       (Örnek: feat: add user login API (Closes #3))



&#x20;   fix: Bir hata çözüldüğünde kullanılır.



&#x20;       (Örnek: fix: resolve token calculation bug (Closes #5))



&#x20;   docs: Sadece dokümantasyon, README veya SRS yazıldığında kullanılır.



&#x20;       (Örnek: docs: update SRS document (Closes #4))



&#x20;   test: Postman veya kod testleri eklendiğinde kullanılır.



&#x20;       (Örnek: test: add auth API tests (Closes #7))



&#x20;   refactor: Mevcut kodun çalışmasını değiştirmeden yapısı iyileştirildiğinde kullanılır.



&#x20;       (Örnek: refactor: clean up database schema (Closes #2))



(Kritik Uyarı: Commit mesajının sonuna (Closes #X) yazmazsanız, kodunuz birleştiğinde proje tahtasındaki göreviniz otomatik kapanmaz ve havada kalır.)

ADIM 4: Kodu Sisteme Gönderme (Push)



İşinizi bitirdiğinizde ve kurala uygun commit attığınızda, kodunuzu GitHub'daki kendi dalınıza göndermelisiniz:

Bash



\# 1. Değişen tüm dosyaları ekleyin

git add .



\# 2. Kurallara uygun commit mesajınızı yazın

git commit -m "feat: detaylı açıklama (Closes #X)"



\# 3. Kodunuzu KENDİ DALINIZA (feature/...) pushlayın

git push origin feature/USXX-kisa-aciklama



ADIM 5: Teslimat ve Onay (Pull Request - PR)



Kodu pushlamak işin bittiği anlamına gelmez. Yazdığınız kodun ana projeye (develop dalına) birleşmesi (Merge) gerekir.



&#x20;   GitHub reposuna tarayıcıdan girin.



&#x20;   Ana ekranda beliren sarı/yeşil renkli "Compare \& pull request" butonuna basın.



&#x20;   Yönlendirmenin doğru olduğundan emin olun:



&#x20;       base: develop



&#x20;       compare: feature/sizin-daliniz



&#x20;   PR Başlığına yine commit mesajınızdaki gibi net bir açıklama yazın (Örn: feat: Add frontend auth system).



&#x20;   Create pull request butonuna basın.



&#x20;   Takımdan birinin (DevOps, Scrum Master veya Lead) kodunuzu inceleyip Approve (Onay) vermesini bekleyin. Onay almayan kodlar sisteme dahil edilmez.

