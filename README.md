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


# Creational(Yaratimsal) Pattern #
**Command Pattern**

Nedir?

Komut(Command) tasarım deseni, behavioral tasarım desenlerinden biridir. Request-response model arasında lose coupling ilişki sağlar. Bir isteği nesneye dönüştürerek, isteğin kullanıcı sınıfları tarafından rahatça erişilebilmesi sağlanır.

Ne zaman Kullanılır?

Diyelim ki 4 tane buton var ve her bir buton farklı işlemler yapıyor. Hangi butona tıklandığını if-else ile kontrol edip ilgili işlemin çalıştırılması yerine veya her buton tıklama olayı için ayrı 4 farklı metod yazmak yerine komut tasarım deseni kullanarak, yapılacak işlemler için(örneğin Açma, Kapama, Kaydetme gibi) sınıflar oluştururuz. Daha sonra, bu butonların onclick event'ları içerisinde bu sınıflardan nesne yaratılıp execute() metodu çağırabiliriz. Bu sayede karmaşık if-else yapısından da kurtulmuş oluruz.

Nasıl Kullanılır?

Her komut için bir sınıf yaratılır. Bu sınıfların ortak türe sahip olabilmesi için Command isminde bir interface tanımlanır. Command interface içerisinde tek bir tane metod gereklidir: execute() . Her komut için oluşturulan sınıflar execute() metodunu implement ederler. execute() metodu asıl işlevi yapacak olan sınıfın metodunu çağırır. Asıl işlevi yapan sınıf Receiver sınıfıdır. Daha sonra Komut sınıflarının çağrılması için Invoker sınıfı oluşturulur. Fakat Invoker sınıfı hangi komut nesnesini kullanacağını bilmez. Sadece nasıl kullanacağını bilir. Son olarak Client ise Invoker sınıfını çağırır.

Faydaları Nedir?

1. Uygulama daha modüler ve esnek olur.
2. Geriye alma işlemi( undo ) ve anlık durumun restorasyonu gibi işlemler yapılabilir.
3. Loglama ve transactional işlemler için kullanılabilir.

4. Makro komutlar oluşturarak bu komutların bir anda çalıştırılması sağlanabilir.

Gerekenler

Türü interface olan Command sınıfı
Tek bir tane somut Command sınıfı
Receiver sınıfı
Invoker sınıfı
Test sınıfı

Örnek Kullanım Alanları

1. java.lang.Runnable interface'nin tüm implementasyonları
2. javax.swing.Action interface'nin tüm implementasyonları


Örnek Uygulama

Command interface
```java
public interface Command {
    public void execute();
}
//LightOnCommand Sınıfı

public class LightOnCommand implements Command {
    Light light;
     
    public LightOnCommand(Light light) {
        this.light = light;
    }
     
    public void execute() {
        light.on();
    }
}

LightOffCommand Sınıfı

/**
* Somut Command sinifimiz
*/
public class LightOffCommand implements Command {
    Light light;
     
    public LightOffCommand(Light light) {
        this.light = light;
    }
     
    public void execute() {
        light.off();
    }
}

//SimpleRemoteControl Sınıfı


/**
* Invoker sinifimiz
*/
public class SimpleRemoteControl {
    Command slot;
     
    public SimpleRemoteControl() {}
     
    public void setCommand(Command command) {
        slot = command;
    }
     
    public void buttonWasPressed() {
        slot.execute();
    }
}


Light Sınıfı

/**
* Receiver sınıfımız
*/
public class Light {
     
    public Light() {
    }
     
    public void on() {
        System.out.println('Light is on');
    }
     
    public void off() {
        System.out.println('Light is off');
    }
}

//Test Sınıfı

/**
* Client sınıfımız
*/
public class RemoteControlTest {
    public static void main(String[] args) {
        SimpleRemoteControl remote = new SimpleRemoteControl();
        Light light = new Light();
        LightOnCommand lightOn = new LightOnCommand(light);
        remote.setCommand(lightOn);
        remote.buttonWasPressed();
        remote.buttonWasPressed();
    }
}
```
![class](https://github.com/halitakkus/Yazilim-mimarisi-ve-tasarimi/blob/master/ClassDiagram-Builder%20Pattern.jpg)

# Kaynakça #

[http://ilkaygunel.com/blog/2017/builder-design-pattern-in-java/](http://ilkaygunel.com/blog/2017/builder-design-pattern-in-java/)

[https://www.codesenior.com/tutorial/Komut-Command-Tasarim-Deseni](https://www.codesenior.com/tutorial/Komut-Command-Tasarim-Deseni)
