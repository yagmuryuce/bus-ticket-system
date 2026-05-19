import os

admin_bilgileri = {"admin": "1234"}

KULLANICI_DOSYA = "kullanicilar.txt"
BILET_DOSYA = "biletler.txt"


seferler = {
    1: {"nereden": "Fethiye", "nereye": "İzmir", "saat": "09:00", "fiyat": 500, "koltuk": 5},
    2: {"nereden": "Fethiye", "nereye": "Ankara", "saat": "14:00", "fiyat": 800, "koltuk": 3},
    3: {"nereden": "Fethiye", "nereye": "İstanbul", "saat": "22:00", "fiyat": 1200, "koltuk": 2}
}

biletler = []


# ---------------- DOSYA SİSTEMİ ----------------

def kullanicilari_yukle():
    kullanicilar = {}

    if not os.path.exists(KULLANICI_DOSYA):
        return kullanicilar

    with open(KULLANICI_DOSYA, "r", encoding="utf-8") as f:
        for satir in f:
            if satir.strip():
                k, s = satir.strip().split(",")
                kullanicilar[k] = s

    return kullanicilar


def kullanici_kaydet(kullanici, sifre):
    with open(KULLANICI_DOSYA, "a", encoding="utf-8") as f:
        f.write(f"{kullanici},{sifre}\n")


def bilet_kaydet(bilet):
    with open(BILET_DOSYA, "a", encoding="utf-8") as f:
        f.write(str(bilet) + "\n")


# ---------------- SEFER ----------------

def seferleri_goster():
    print("\n--- SEFERLER ---")
    for no, s in seferler.items():
        print(f"""
{no} | {s["nereden"]} -> {s["nereye"]}
Saat: {s["saat"]} | Fiyat: {s["fiyat"]} TL | Koltuk: {s["koltuk"]}
""")


# ---------------- ÖDEME ----------------

def odeme_yap():
    while True:
        print("\n1- Nakit\n2- Kredi Kartı")
        secim = input("Seçim: ")

        if secim == "1":
            return "Nakit"

        elif secim == "2":

            kart_no = input("Kart no: ")
            skt = input("SKT (MMYY): ")
            cvv = input("CVV: ")
            sifre = input("Şifre: ")

            if (kart_no.isdigit() and skt.isdigit() and cvv.isdigit() and sifre.isdigit()
                and len(skt) == 4 and len(cvv) == 3):

                return "Kredi Kartı"

            print("Kart bilgileri hatalı")

        else:
            print("Hatalı seçim")


# ---------------- ÜYELİK ----------------

def uye_ol(kullanicilar):
    print("\n--- ÜYELİK ---")

    kullanici = input("Kullanıcı adı: ")

    if kullanici in kullanicilar:
        print("Bu kullanıcı zaten var")
        return kullanicilar

    sifre = input("Şifre: ")

    kullanicilar[kullanici] = sifre
    kullanici_kaydet(kullanici, sifre)

    print("Kayıt başarılı")
    return kullanicilar


def giris_ekrani(kullanicilar):

    while True:

        print("\n1- Giriş Yap\n2- Üye Ol\n3- Çıkış")
        secim = input("Seçim: ")

        if secim == "1":

            k = input("Kullanıcı: ")
            s = input("Şifre: ")

            if k in kullanicilar and kullanicilar[k] == s:
                print("Giriş başarılı")
                return k

            print("Hatalı giriş")

        elif secim == "2":
            kullanicilar = uye_ol(kullanicilar)

        elif secim == "3":
            return None

        else:
            print("Hatalı seçim")


# ---------------- BİLET ----------------

def bilet_al(kullanici):

    seferleri_goster()

    try:
        secim = int(input("Sefer no: "))
    except:
        print("Sayı gir")
        return

    if secim not in seferler:
        print("Geçersiz sefer")
        return

    if seferler[secim]["koltuk"] <= 0:
        print("Koltuk yok")
        return

    odeme = odeme_yap()

    seferler[secim]["koltuk"] -= 1

    bilet = {
        "kullanici": kullanici,
        "sefer": secim,
        "nereden": seferler[secim]["nereden"],
        "nereye": seferler[secim]["nereye"],
        "saat": seferler[secim]["saat"],
        "fiyat": seferler[secim]["fiyat"],
        "odeme": odeme
    }

    biletler.append(bilet)
    bilet_kaydet(bilet)

    print("\n--- BİLET OLUŞTU ---")
    print(bilet)

    print("\nSefer bilgisi:", seferler[secim])


# ---------------- USER PANEL ----------------

def user_panel(kullanici):

    while True:
        print("\n1- Seferler\n2- Bilet al\n3- Çıkış")
        s = input("Seçim: ")

        if s == "1":
            seferleri_goster()

        elif s == "2":
            bilet_al(kullanici)

        elif s == "3":
            return

        else:
            print("Hatalı")


# ---------------- ADMIN ----------------

def admin_panel():

    while True:
        print("\n1- Seferler\n2- Biletler\n3- Çıkış")
        s = input("Seçim: ")

        if s == "1":
            seferleri_goster()

        elif s == "2":
            print(biletler)

        elif s == "3":
            return

        else:
            print("Hatalı")


# ---------------- ANA SİSTEM ----------------

kullanicilar = kullanicilari_yukle()

while True:

    print("\n===== SİSTEM =====")
    print("1- Kullanıcı")
    print("2- Admin")
    print("3- Çıkış")

    secim = input("Seçim: ")

    if secim == "1":

        user = giris_ekrani(kullanicilar)

        if user:
            user_panel(user)

    elif secim == "2":

        a = input("Admin: ")
        s = input("Şifre: ")

        if a in admin_bilgileri and admin_bilgileri[a] == s:
            admin_panel()
        else:
            print("Hatalı admin")

    elif secim == "3":
        break

    else:
        print("Hatalı")
