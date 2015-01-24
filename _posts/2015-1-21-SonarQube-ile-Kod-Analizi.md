---
layout: post
title: SonarQube ile Static Kod Analizi
---

Static kod analizi için Sonar iyi bir tercih olabilir. Sonar kod kalitesini ölçmek, raporlamak ve iyileştirmek için kullanılan bir platform. 20 civarında dil için analiz yapabiliyor ve plugin desteği ile ihtiyaca göre özellikleri zenginleştirilebiliyor. Open source ama bazı plugin’leri ücretli.

![_config.yml]({{ site.baseurl }}/images/sonar-screenshot.png)

Göze çarpan özellikleri:

- Zengin kod analiz raporu verir: duplicated code, coding standards, unit tests, code coverage, complex code, potential bugs, comments, design & architecture.
- 25 dil desteği
- Geçmiş analizleri saklaması ve zaman içinde farklılıkları raporlaması
- Mave, Gradle, Ant, Jenkins, gibi build ve integation tool’lar ile çalışabilmesi
- Jacoco, Fortify desteği
- Eclipse ve IntelliJ üzerinden kod analizi
- SQALE metodu ile “Technical Debt” raporu

Daha fazla detay için:

- [http://www.sonarqube.org/](http://www.sonarqube.org/)
- [http://www.sonarsource.com/products/features/](http://www.sonarsource.com/products/features/)

Ekip olarak çalışıyorsanız, ortak bir Sorce Control sisteminiz varsa ve de uygulamalarınız bir test ortamına deploy ediyorsanız Sonar’ı ortak bir makinede kurmak işleriniz kolaylaştırabilir.

Biz ilk taramada ciddi sorun yaratacak maddeleri temizledikten sonra yeni maddeler yaratmamak için şu şekilde çalışmaya karar verdik:

- Yeni issue yaratma
- Denk geldiğin hataları da temizle

Kullanım ihtiyaçlarına göre 2 farklı senaryo kullanıyoruz:

##Senaryo 1: Remote Sonar Server üzerinden kod analizi
Network’de erişebildiğimiz bir makinede kurulmuş olan bir Sonar üzerinden local’deki kodların analizi yapılabilir. Eclipse içinden Remote Server ile sorunsuz kod analizi yapılabilmesi için alttaki işlemler sırayla yapılır.

1. Eclipse Plugin Kurulumu: Linkte verilen bilgilere göre Eclipse plugin’i kurulur:
[http://docs.sonarqube.org/display/SONAR/Installing+SonarQube+in+Eclipse](http://docs.sonarqube.org/display/SONAR/Installing+SonarQube+in+Eclipse)
2. Eclipse Sonar Ayarları: Linkte verilen bilgilere göre Eclipse plugin ayarları yapılır. Server olarak localhost:9000 yerine Sonar’ın adresi girilir, örnek: http://TheSonarServer:9000/
[http://docs.sonarqube.org/display/SONAR/Configuring+SonarQube+in+Eclipse](http://docs.sonarqube.org/display/SONAR/Configuring+SonarQube+in+Eclipse)
3. Eclipse içinden Sonar analizi: Linkte verilen bilgilere göre Eclipse içinden Sonar kullanılır.
[http://docs.sonarqube.org/display/SONAR/Working+with+SonarQube+in+Eclipse](http://docs.sonarqube.org/display/SONAR/Working+with+SonarQube+in+Eclipse)
4. Check-in öncesi kod analizi: Yeni geliştirilen Kod’larda Sonar ile kod analizi yapılıp, issue’lar temizlendikten sonra check-inlenmelidir.


##Senaryo 2: Local Sonar Kullanımı
Local’de Sonar kurmak için aşağıdaki adımlar izlenir.

**1- JDK 1.7 kurulur.**
System Environment path’inde JDK 1.7 olmalıdır. java -version ile kontrol versiyon kontrol edilebilir.
Eğer 1.7 env set edilemezse, JDK 1.7 indirilip, C:\sonar-rmc\sonarqube-4.4.1\conf\wrapper.conf’da alttaki değişiklik ile JDK için path ayarlaması yapılır:
wrapper.java.command=D:/dev/java/jdk1.7.0_45/bin/java

**2- MySql kurulumu:** Sonar’da analizlerin daha hızlı yapılabilmesi için daha önce yapılan analizleri DB’de saklanması gerekmektedir. Default gelen inMemory DB kullanımı geçmiş analizlerin kaybedilmesine sebep olur ve her seferinde tüm analizlerin tekrar yapılmasını gerektirir. Bu yüzden bir db olmalıdır. Bunun için mySql iyi bir seçenek olur. MySql’in kendi sitesinden indirip kurmak daha fazla vakit alabiliyor.  MySql Kurulumu için XAMPP öneririm. İçinde gelen phpMyAdmin db işleri için kullanışlı oluyor. Eğer hali hazırda kurulu bir MySql ‘iniz varsa bunu da kullanabilirsiniz.
[https://www.apachefriends.org/download.html](https://www.apachefriends.org/download.html) adresinden XAMPP indirilip kurulur. XAMPP için örnek kurulum dizini: C:\sonar-rmc\xampp
Genel kullanım olarak MySql’i iki şekilde ayağa kaldırabilirsiniz:
[XAMPP_APP_PATH]\mysql\mysql_installservice.bat ile Windows Service kurarsanız, OS açıldığında MySql otomatik çalışır.
[XAMPP_APP_PATH]\mysql_start.bat ile manual start edebilirsiniz.--

Mysql kurulurken:

- sonar adında user create edilir.
- Encoding olarak utf_general_ci seçilmelidir.
- Aynı anda şema create edilmelidir.
- Ana makine localhost seçilir.
- Genel Yetkiler altındaki tüm yetkiler verilir.


**MySql’de “Row size too large (> 8126)” hatası** için 2 my.ini’de parametre değiştirilir:

```
max_allowed_packet=64M
innodb_log_file_size=256M
```
Önceden oluşmuş innodb log dosyaları silinir ve MySql restart edilir. Ref:
[https://confluence.atlassian.com/display/CONFKB/MySQLSyntaxErrorException%3A+Row+size+too+large](https://confluence.atlassian.com/display/CONFKB/MySQLSyntaxErrorException%3A+Row+size+too+large)
[http://stackoverflow.com/questions/8062496/how-to-change-max-allowed-packet-size](http://stackoverflow.com/questions/8062496/how-to-change-max-allowed-packet-size)

**3- Local makinede Sonar kurulumu**
http://www.sonarqube.org/downloads/ adresinden SonarQube 4.4.1 ve SonarQube Runner 2.4 indirilir. Ekteki dokümanda verilen detay ile kurulum yapılır.
MySQL ayakta ise [SONAR_APP_PATH]\bin\windows-x86-64\StartSonar.bat ile Sonar Server ayağa kaldırılır,http://localhost:9000/ adresinden kontrol edilir.

windows-x86-64 yerine uygun OS versiyonu seçilir.

**4- Local Sonar kod analizi:** Sonar ve MySql ayağa kaldırılıktan sonra kod analizi için şu adımlar olmalıdır:

a. Sonar property dosyası sonar-project.properties proje dizininde olmalıdır. Bu dosya ekipte Sonar ile ilgilenen developer tarafından clearCase veya SVN’e eklenecektir. Örnek:

```
# Required metadata
sonar.projectKey=MyProjectName
sonar.projectName=My Project Name
sonar.projectVersion=1.1
# Comma-separated paths to directories with sources (required)
sonar.sources=src/main/java
sonar.binaries=target/classes
# Language
sonar.language=java
# Encoding of the source files
sonar.sourceEncoding=UTF-8
#Tells SonarQube where the integration tests code coverage report is
sonar.jacoco.itReportPath=reports/jacoco/jacoco-it.exec
```

b. Kod analizi yapılabilmesi için uygulama build edilmiş yani class dosyaları üretilmiş olmalıdır.

c. SonarServer ayakta ise analiz edilecek proje dizinine gidilip C:\sonar-rmc\sonar-runner-2.4\bin\sonar-runner çalıştırılır.
Alttaki 2 Env. Path değişkeni ile herhangi bir satırda sonar-runner’ı çalıştırabilirsiniz.

- Environment Variables->System Variables altında yeni variable tanımlanır. Variable Name: SONAR_RUNNER_HOME Variable Value: C:\sonar-rmc\sonar-runner-2.4
- C:\sonar-rmc\sonar-runner-2.4\bin dizinini Environment Variables -> System->Path’in sonuna eklenir.

d. Sonar-Runner çalıştıktan sonra uygulama analiz edilmiş olur. Analiz edilen bilgiler daha sonraki adımlarda Eclipse içinde kullanılacak.

Bu adım development sürecinde kod analizi gerektiğinde tekrarlanmalıdır.

**5- Eclipse Plugin kurulumu:** (1. Senaryodaki madde ile aynı)

**6- Eclipse Sonar ayarları:** 1. Senaryodaki işlemler yapılır, yalnız server olarak localhost:9000 set edilir

**7- Eclipse içinden Sonar analizi** (1. Senaryodaki madde ile aynı)

**8- Check-in öncesi kod analizi:** SonarServer ayakta ise analiz edilecek proje dizinine gidilip sonar-runner çalıştırılır. Bu şekilde uygulama analiz edilmiş olur. Eclipse içinden Project Explorer’da proje üzerinde sağ tıklanıp SonarQube->Analyze seçeneği ile güncel analiz bilgileri Eclipse’e alınır.