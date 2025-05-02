#include <iostream>
#include <string>
#include <stdexcept>

template <typename T>
T topla(T a, T b) { return a + b; }

template <typename T>
T cikar(T a, T b) { return a - b; }

template <typename T>
T carp(T a, T b) { return a * b; }

template <typename T>
T bol(T a, T b) {
    if (b == 0) {
        throw std::runtime_error("Sıfıra bölme hatası!");
    }
    return a / b;
}

struct Islem {
    std::string islemturu;
    double sayi1;
    double sayi2;
    double sonuc;
    Islem* next;
};

class IslemGecmisi {
public:
    virtual void goster(Islem* baslangic) = 0;
};

class BasitGecmis : public IslemGecmisi {
public:
    void goster(Islem* baslangic) override {
        std::cout << "\nBasit Geçmiş\n";
        while (baslangic != nullptr) {
            std::cout << "İşlem türü: " << baslangic->islemturu << std::endl;
            baslangic = baslangic->next;
        }
    }
};

class AyrintiliGecmis : public IslemGecmisi {
public:
    void goster(Islem* baslangic) override {
        std::cout << "\nAyrıntılı Geçmiş\n";
        while (baslangic != nullptr) {
            std::cout << "İşlem türü: " << baslangic->islemturu
                      << ", Parametreler: " << baslangic->sayi1 << ", " << baslangic->sayi2
                      << ", Sonuç: " << baslangic->sonuc << std::endl;
            baslangic = baslangic->next;
        }
    }
};

class HesapMakinasi {
private:
    double gizlisonuc;

public:
    template <typename T>
    void hesapla(std::string islemturu, T a, T b, Islem*& gecmis) {
        if (islemturu == "topla")
            gizlisonuc = topla(a, b);
        else if (islemturu == "cikar")
            gizlisonuc = cikar(a, b);
        else if (islemturu == "carp")
            gizlisonuc = carp(a, b);
        else if (islemturu == "bol")
            gizlisonuc = bol(a, b);

        Islem* yeniislem = new Islem{islemturu, a, b, gizlisonuc, nullptr};
        if (gecmis == nullptr) {
            gecmis = yeniislem;
        } else {
            Islem* temp = gecmis;
            while (temp->next != nullptr) {
                temp = temp->next;
            }
            temp->next = yeniislem;
        }
    }

    friend void sonucgoster(const HesapMakinasi& h) {
        std::cout << "Yapılan son işlem sonucu: " << h.gizlisonuc << std::endl;
    }
};

int main() {
    HesapMakinasi hesap;
    Islem* gecmis = nullptr;
    BasitGecmis basit;
    AyrintiliGecmis ayrintili;
    int secim;
    double sayi1, sayi2;

    while (true) {
        std::cout << "\n---Hesap Makinesi---\n";
        std::cout << "1. Toplama\n";
        std::cout << "2. Çıkarma\n";
        std::cout << "3. Çarpma\n";
        std::cout << "4. Bölme\n";
        std::cout << "5. Basit Geçmişi Göster\n";
        std::cout << "6. Ayrıntılı Geçmişi Göster\n";
        std::cout << "7. Çıkış\n";
        std::cout << "Seçiminizi yapın: ";
        std::cin >> secim;

        if (secim >= 1 && secim <= 4) {
            std::cout << "Birinci sayıyı girin: ";
            std::cin >> sayi1;
            std::cout << "İkinci sayıyı girin: ";
            std::cin >> sayi2;

            try {
                switch (secim) {
                case 1:
                    hesap.hesapla("topla", sayi1, sayi2, gecmis);
                    break;
                case 2:
                    hesap.hesapla("cikar", sayi1, sayi2, gecmis);
                    break;
                case 3:
                    hesap.hesapla("carp", sayi1, sayi2, gecmis);
                    break;
                case 4:
                    hesap.hesapla("bol", sayi1, sayi2, gecmis);
                    break;
                }

                sonucgoster(hesap);
            } catch (const std::exception& e) {
                std::cerr << "HATA: " << e.what() << std::endl;
            }
        } else if (secim == 5) {
            basit.goster(gecmis);
        } else if (secim == 6) {
            ayrintili.goster(gecmis);
        } else if (secim == 7) {
            break;
        } else {
            std::cerr << "Geçersiz seçim! Lütfen tekrar deneyin.\n";
        }
    }

    return 0;
}
