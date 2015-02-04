---
layout: post
title: SonarQube ile PL/SQL Static Kod Analizi & sonar.plsql.file.suffixes
---

Sonar ile PL/SQL kodları analiz edilebiliyor. Ücretli bir plugin ama kurumsal firmalar için gayet makul.
[http://www.sonarsource.com/products/plugins/languages/plsql/] (http://www.sonarsource.com/products/plugins/languages/plsql/) sayfasından lisans bilgileri alınabilir.


Çokca eski kodlardan oluşan Function, Procedure ve Trigger'lar için default gelen dosya uzantısı fnc, prc ve trg. Ama bu dosyalar analiz edilmedi.
Analiz sırasında sadece sql, pkb ve pks uzantılarının analiz edildiğini gördüm ama dosya uzantıları ile ilgili setedilecek bir parametre bilgisine bir yerde rastlamadım.
Neyse, sonunda plugin içinde sonar.plsql.file.suffixes ile aşağıdaki gibi farklı dosya uzantılarının da analiz edilebileceğini gördüm.
Bu parametreyi sonar-project.properties içinde vermelisiniz.


```
  sonar.plsql.file.suffixes=sql,pks,pkb,fnc,prc,trg
```