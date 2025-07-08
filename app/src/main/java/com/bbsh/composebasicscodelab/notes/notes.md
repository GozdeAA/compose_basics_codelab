
https://developer.android.com/codelabs/jetpack-compose-basics#1


## 🔁 `remember` vs `rememberSaveable` — Nedir, Ne Değildir?

### 🧠 `remember`

* **RAM'de tutulur.**
* Compose recomposition sırasında aynı kalır.
* Ama **config değişikliklerinde (örn. ekran döndürme, arka plana atıp geri gelme)** kaybolur.

### 💾 `rememberSaveable`

* RAM + **Bundle / SavedInstanceState** / custom saver ile **disk’e yazılabilir.**
* Config değişikliği (rotate, dark mode, process kill → restore) gibi durumlarda veri **geri yüklenir.**

---

## 🔥 O Zaman Neden `rememberSaveable` Her Yerde Kullanmıyoruz?

İşte bu sorunun cevabı: **Kısa cevap:** çünkü **gereksiz yüktür** ve **her şeyin kaydedilebilir olmadığını varsayar.**

### 🧱 1. **Performans ve Hafıza Yükü**

* `rememberSaveable` arkasında `Bundle` veya `SavedStateHandle` gibi bir şey kullanır.
* Bu yapılar:

    * Sadece **serializable** / primitive türleri destekler.
    * Büyük objeleri (örn. görsel bitmap, ViewModel, network response listesi) taşımak **verimsizdir.**
* Bu objeler UI durumu için kritik değilse boş yere depolanır, memory & disk şişer.

### 🚫 2. **Bazı Şeyler "Kaydedilebilir" Değildir**

* Compose bir objeyi `rememberSaveable` ile tutarken onu serialize etmek ister.
* Eğer:

    * Custom bir sınıfın varsa
    * LiveData / Flow / CoroutineScope gibi şeyleri saklıyorsan
    * Ya da `State<T>` dışında başka yapılar varsa

  → hata alırsın: **"cannot be saved"**

Bunu çözmek için `Saver` kullanman gerekir ama bu da ek iş ve anlamlı değil her yerde.

### 🧘 3. **Her Veri Kalıcı Olmak Zorunda Değil**

* Örnek: animasyon state’i, geçici UI efektleri, scroll konumu, hover gibi durumlar
* Bunlar zaten kullanıcı için "önemli" değil, config değişince sıfırlanması mantıklı

---

## 🎯 Ne Zaman `rememberSaveable` Kullanmalı?

✅ Kullan:

* Kullanıcının girdiği metin (form field)
* Sayfalar arası dolaşımda korunması gereken state
* UI logic’e etki eden seçim (radio button, tab seçimi vs.)

🚫 Kullanma:

* Büyük veri yapıları
* Anlık animasyon state’leri
* Zaten ViewModel'de tutulan state
* Non-serializable state’ler (Flow, Job, vb.)

---

## 🤓 Ekstra Bilgi: ViewModel mi `rememberSaveable` mı?

* **ViewModel:** Kalıcı (config-safe), daha ağır state yönetimi. Genelde app-wide veya screen-wide logic.
* **`rememberSaveable`:** View-level state, kısa ömürlü ama "geri yüklenebilir".

ViewModel'den gelen datayı `remember` ile cachelemek mantıklıdır, `rememberSaveable` ile değil.

---

## 📌 Sonuç

> `rememberSaveable` güçlüdür, ama **her işin çekiçle çözülmeyeceği** gibi, her state'i onunla tutmak gereksizdir.

Gerektiği yerde kullanmak en sağlıklı, verimli ve sürdürülebilir yoldur. Eğer kullanacağın state konfigürasyon değiştiğinde **gerçekten geri gelmeli mi?** sorusunu sormadan `rememberSaveable` kullanırsan zamanla state jungle’ına dönersin. 🌴

----

## NOTE
LazyColumn and LazyRow are equivalent to RecyclerView in Android Views.

## TEMA 

| Soru                                                     | Cevap      |
| -------------------------------------------------------- | ---------- |
| `MaterialTheme` statik mi?                               | ❌ Hayır    |
| Aktif temaya göre davranır mı?                           | ✅ Evet     |
| Context üzerinden manuel çekmek gerekir mi?              | ❌ Gerekmez |
| `.copy()` ile override yapınca diğer stiller korunur mu? | ✅ Evet     |
