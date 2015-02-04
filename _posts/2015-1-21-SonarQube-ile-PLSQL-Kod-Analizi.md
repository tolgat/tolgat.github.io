---
layout: post
title: SonarQube ile PL/SQL Static Kod Analizi & sonar.plsql.file.suffixes
---

Sonar ile PL/SQL kodları analiz edilebiliyor. Ücretli bir plugin ama kurumsal firmalar için gayet makul.
[http://www.sonarsource.com/products/plugins/languages/plsql/] (http://www.sonarsource.com/products/plugins/languages/plsql/) sayfasından lisans bilgileri alınabilir.


Analiz sırasında sadece sql, pkb ve pks uzantılarını analiz ettiğini gördüm ama dosya uzantıları ile ilgili bir parametre bilgisine bir yerde rastlamadım.
Çokca eski kodlardan oluşan Function, Procedure ve Trigger'lar için default gelen dosya uzantısı fnc, prc ve trg.
Neyse, sonunda plugin içinde sonar.plsql.file.suffixes ile aşağıdaki gibi farklı dosya uzantılarının da analiz edilebileceğini gördüm.
Bu parametreyi sonar-project.properties içide vermelisiniz.


```
  sonar.plsql.file.suffixes=sql,pks,pkb,fnc,prc,trg
```