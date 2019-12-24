# Yazilim-mimarisi-ve-tasarimi

## Behavioral(Davranissal) Pattern ##
**Builder Tasarim Deseni**

Bazi nesneler birden fazla nesnenin birlesmesinden(bazi islemleri yapmasi sonucu) olusabilir. 
Zamanla bu ana nesneyi olusturan nesnelerin yapisi degisebilir, bu nesnelerin olusturulmasi karisik 
bir hal alabilir veya bu nesnelere baska nesneler de eklenebilir. Builder tasarim deseni bu 
gibi durumlarda genisletilebilirligi saglamak ve kod karmasikligini engellemek için kullanilir.
Builder tasarim deseninde bu nesnelerin olusturulmasi Builder denilen siniflarin sorumlulugundadir.
Client sadece olusturulacak nesne türünü belirterek ana nesneyi olusturan nesnelerin olusturulmasiyla ilgilenmez.
Abstract factory tasarim kalibi ile benzer bir yapisi vardir. Aralarindaki fark builder tasarim deseni birden 
fazla nesnenin birlesmesinden olusan nesnelerin üretilmesinden sorumludur.

*Builder desenini olusturan 4 yapi vardir.*

Product: Olusturulan nesne.
Builder: Product olusturacak nesnelerin (Concrete Builder) uygulamasi gereken arayüz.
Concrete Builder: Product nesnesini olusturan nesne veya özelliklerin olusturuldugu siniflar.
Her concrete builder sinifi ayni arayüzde farkli bir ürünün olusturulmasini saglar.
Director: Verilen builder nesnesine göre product örnegi olusturur.
Örnegin Marka, Model ve Lastik özellikleri olan bir araba nesnemiz olsun. Bu araba nesnemizin
özelliklerinin farkli degerler almasi ile farkli özelliklerde araba nesnesi üretebiliriz. 
```java
public class Araba
    {
        public string Marka { get; set; }
        public string Motor { get; set; }
        public string Lastik { get; set; }
 
        public override string ToString()
        {
            return String.Format("Marka:{0},Motor:{1},Lastik:{2}", Marka, Motor, Lastik);
        }
    }

    // ArabaBuilder arayüzü builder desenindeki Builder yapisidir. 
    // Bir sinifin Product i olusturan nesneleri olusturmasi veya product in özelliklerini setlemesi ile product i olusturan
    siniflar bu arayüzü kullanmalidir.
    public abstract class ArabaBuilder
    {
        protected Araba _araba;
        public Araba araba
        {
            get { return _araba; }
        }
        public abstract void MotorTak();
        public abstract void LastikTak();
    }
 
    // ArabaBuilder arayüzünden türeyen bütün siniflar Builder desenindeki ConcreteBuilder yapisidir.
    // ConcreteBuilder yapisi degisik product nesnelerinin olusturulmasini saglamaktir.
    public class ArabaConcrete1 : ArabaBuilder
    {
        public ArabaConcrete1()
        {
            _araba = new Araba { Marka = "Concrete1" };
        }
 
        public override void MotorTak()
        {
            _araba.Motor = "1.4 LPG";
        }
 
        public override void LastikTak()
        {
            _araba.Lastik = "15' Çelik jant";
        }
    }
 
    public class ArabaConcrete2 : ArabaBuilder
    {
        public ArabaConcrete2()
        {
            _araba = new Araba { Marka = "Concrete2" };
        }
 
        public override void MotorTak()
        {
            _araba.Motor = "1.8 Dizel";
        }
 
        public override void LastikTak()
        {
            _araba.Lastik = "17' Bor alasimli çelik jant";
        }
    }
 
    // ArabaBuilder arayüzündeki metodlari çalistirarak productin olusturulmasini saglar.
    // Builder desenindeki Director yapisidir.
    public class ArabaDirector
    {
        public ArabaDirector(ArabaBuilder ArabaConcrete)
        {
            ArabaConcrete.MotorTak();
            ArabaConcrete.LastikTak();
        }
    }
 
//Desenimizi asagidaki sekilde kullanabiliriz.
 
class Program
{
    static void Main(string[] args)
    {
        ArabaBuilder araba_builder = new ArabaConcrete1();
        ArabaDirector araba_olusutucu = new ArabaDirector(araba_builder);
        Console.WriteLine(araba_builder.araba.ToString());
 
        araba_builder = new ArabaConcrete2();
        araba_olusutucu = new ArabaDirector(araba_builder);
        Console.WriteLine(araba_builder.araba.ToString());
 
        Console.ReadKey();
    }
}



 ```

----------------------------------------------


Creational(Yaratimsal) Pattern
Builder Pattern (Olusturucu Deseni)

Gelistirilen yazilimlarda POJO olarak adlandirabilecek siniflarimizi kullaniyoruz ve bu siniflardan nesne elde ederken 
constructor’lardan faydalanabiliyoruz. Gelgelelim ki POJO içerisindeki alanlarin sayisi arttikça constructor kullanimi zora
girmekte ve kodlama açisinda hos olmayan bir durum ortaya çikmaktadir.

User sinifimiz oldugunu ve söyle bir constructor’imiz oldugunu düsünelim:



```java

public User(String firstName, String surname, int age, String phone, String address) {
        this.firstName = firstName;
        this.surname = surname;
        this.age = age;
        this.phone = phone;
        this.address = address;
    }

```


Bu yapilandiriciya baktigimizda iki sey görüyoruz. Birincisi bir User nesnesi olusturulacagi zaman yapilandiriciya
bekledigi sayi kadar parametre geçirilmesi gerekiyor. Mesela ben User nesnesi olusturulurken phone ya da address bilgilerinin 
zorunlu olarak geçirilmesini istemezsem ne olacak? Ilk akla gelen bir constructor daha yazarim ve orada da phone hariç diger 4 
parametreyi alirim olabilir ancak bu da dogru bir yaklasim degil. Daha esnek ve kod bakimindan düzgün bir yol bulmamiz gerek.


Ikinci ise User nesnesi olusturulurken ortaya çikacak, uzun ve çirkin görünecek constructor kod yapisi.

Builder design pattern bu iki sorunu çözmek için bir yöntem sunuyor. Simdi sundugu bu yöntemi inceleyelim.

Elimizdeki User POJO’sunun yeni hali söyle olacaktir:
```java


public class User {

    private final String firstName;
    private final String surname;
    private final int age;
    private final String phone;
    private final String address;

    public User(UserBuilder userBuilder) {
        this.firstName = userBuilder.firstName;
        this.surname = userBuilder.surname;
        this.age = userBuilder.age;
        this.phone = userBuilder.phone;
        this.address = userBuilder.address;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getSurname() {
        return surname;
    }

    public int getAge() {
        return age;
    }

    public String getPhone() {
        return phone;
    }

    public String getAddress() {
        return address;
    }

    @Override
    public String toString() {
        return "User: " + this.firstName + ", " + this.surname + ", " + this.age + ", " + this.phone + ", " + this.address;
    }

    public static class UserBuilder {

        private final String firstName;
        private final String surname;
        private int age;
        private String phone;
        private String address;

        public UserBuilder(String firstName, String surname) {
            this.firstName = firstName;
            this.surname = surname;
        }

        public UserBuilder age(int age) {
            this.age = age;
            return this;
        }

        public UserBuilder phone(String phone) {
            this.phone = phone;
            return this;
        }

        public UserBuilder address(String address) {
            this.address = address;
            return this;
        }

        public User build() {
            User user = new User(this);
            return user;
        }
    }
}

```

Sinif içerisindeki alanlarin tümü final olarak tanimlanmis. Java’da eger bir degisken final olarak tanimlanirsa Java size
bu degiskene kod içerisinde bir deger atanmasini zorunlu kilar ve bu degeri bir daha degistiremezsiniz. Bu durum isimize 
yarar bir durumdur çünkü bir User nesnesi olustugunda degistirilmemesini istediginiz, sart kostugunuz alanlar olabilir. Ayrica
bu final tanimlanmasi sinif içerisindeki alanlari zorunlu tutmustur fakat bu zorunluluk User nesnesi olusturulmasindan gelmemektedir.
Olusturulan her User nesnesi deger atansa da atanmasa da bu alanlara sahip olacak demektir.
User constructor’ina baktigimiz zaman bir UserBuilder nesnesini parametre aldigini görüyoruz. Az sonra main metodu’nu incelerken
bu constructor’in kullanimini görecegiz.
Sinif içerisindeki degiskenlerin sadece get metotlari var, çünkü User sinifi üzerinden bir set islemi olmayacak.
Inner Class olarak tabir edilen bir UserBuilder sinifini User sinifi içinde yazdik. UserBuilder içerisinde sadece firstName ve surname
alanlari final olarak tanimli çünkü User nesnesinin en azindan bu iki alana sahip olmasini istiyoruz ve final tanimlayarak da ilk
deger atamasini zorunlu kiliyoruz. Bu ilk deger atamasi da UserBuilder constructor’i ile olacak.
Diger alanlar için ise set metotlarina benzer bir metot yazdik. Farkli olarak bu metotlar UserBuilder tipinde dönüs yapiyorlar. 
Bu dönüsün amacini da az sonra main metodu içinde görecegiz.


Simdi de main metodunu inceleyelim:


```java

public class MainClass {

    public static void main(String[] args) {
        User class1 = new User.UserBuilder("halit", "akkuş")
                .age(26)
                .phone("+90 216 504 5655")
                .address("Akasya Acibadem Ofis Kuleleri\n A Blok 24. Kat No:127\n Acibadem Istanbul Turkey")
                .build();

        User class2 = new User.UserBuilder("reşit", "demir")
                .age(22)
                .phone("+90 216 504 5655")
                .build();

        User class3 = new User.UserBuilder("bora", "aslan")
                .age(22)
                .build();

        System.out.println(class1);
        System.out.println(class2);
        System.out.println(class3);
    }
}
```
Main metodumuz içerisinde 3 tane User nesnesi olusturuyoruz. Nesne olusturulmasi sirasinda biz User içerisinde static class
olarak tanimlanmis UserBuilder’a User.UserBuilder diyerek direk erisebiliyoruz. UserBuilder’in yapilandiricisina istedigi bilgileri
verdikten sonra doldurmak istedigimiz diger alanlari dolduruyoruz. Az önce UserBuilder içinde set metoduna benzeyen metotlardan
bahsetmistik ve bu metotlarin UserBuilder tipinde dönüs yaptigini söylemistik. Bu dönüsün amaci pes pese bir sekilde UserBuilder
içerisindeki metotlara erisim amacidir. Örnegin age metodu UserBuilder tipinde dönüs yaparak bizim UserBuilder içindeki diger
metotlara hemen arkasindan erismemizi sagliyor.

class nesnesi için baktigimizda age, phone ve address bilgilerini dolduruyoruz. Son olarak da build deyip bir User nesnesi elde edip
bunu class nesnesine atiyoruz.

kevser nesnesi için baktigimizda ise address bilgisinin atanmadan bir User nesnesinin elde edilebilidigini görüyoruz. merve nesnesinde
isim, soyisim bilgisine ek olarak sadece yas bilgisi var.
