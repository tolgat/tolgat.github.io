---
layout: post
title: AngularJs Directive - Text Area Limit ve Karakter Sayacı
---

Binlerce güzel jQuery plugin'i arasından projeye çok da yakışacak çok güzel bir jQuery plugin'i buldunuz ama angularJs'i yazan core ekip sakın DOM'a müdahele etmeyin diyor.
Bu durumda yapmanız gereken şey bu plugin'i Directive içine alıp tekrar kullanılabilen bir kod haline getirmenizdir.
AngularJs'de jQuery gibi DOM'a müdahele eden hiç bir işlem Directive dışında hiç bir yerde olmamalıdır. Yani bir Controller veya Service içinden direk DOM'a müdahele etmemelisiniz.
DOM'a müdahele ederseniz kod en başta gayet güzel çalışır ama zamanla hayatınızı zorlaştırmaya başlar, ayrıca:

- Binding'de beklenmedik davranışlara neden olabilirsiniz
- Kodunu test edilebilir olmaz
- Reusable bir kod olmaktan çıkar
- CSS ve HTML bağımlılığı olur

Bunun için bir projede kullandığım ve tekrar tekrar ihityacım olan bir Directive örneği vereceğim: tcTextareaLimited.
textarea html input element'ine limit ve charCount ozelligi katar. jquery.charCount.js, jquery ve underscore bağımlılığı vardır!

Directive kodu:

```javascript
'use strict';

var ng = require('angular');

/**
 *  @name tcTextareaLimited
 *
 *  @description
 *   textarea html input element'ine limit ve charCount ozelligi katar.
 *   jquery.charCount.js, jquery ve underscore bağımlılığı vardır!
 *
 *  UYARI: textarea'dan sonra alttaki span ve div mutlaka olmalıdır! Aksi takdirde kalan karaketer
 *    sayısının gösterildiği div sorun çıkarabiliyor. (ref: jquery.charCount.js içindeki getNextCharLeftInformation fonksiyonu)
 *    <span id="bb">bb</span><div class="charleft"></div>
 *
 *
 *  @param Attributes:
 *    - consoleLog: opsiyonel, default=false. "true" verilirse console'a log yazilir.
 *    - maxCharacterSize: zorunlu
 *    - originalStyle: opsiyonel, default='input-large'
 *    - warningStyle: opsiyonel, default='warning input-large'
 *    - warningNumber: opsiyonel, default=0
 *    - displayFormat: opsiyonel, default='Toplam:#max, Girilen:#input, Kalan:#left'
 *
 *
 *  @example
 *     <textarea
 *        id="myTextarea1"
 *        tc-textarea-limited
 *        max-character-size="9"
 *        warning-number="0"
 *        original-style="input-very-large"
 *        display-format="total:#max, input count:#input, remaining count:#left"
 *        ng-model="myMessage"
 *        console-log="true"></textarea>
 *     <span id="aa">bir span</span>
 *     <div class="charleft"></div>
 *
 *     <textarea
 *        id="myTextarea2"
 *        tc-textarea-limited
 *        max-character-size="tolga"
 *        ng-model="myMessage2"
 *        console-log="true"></textarea>
 *     <span id="aa">bir span</span><div class="charleft"></div>
 *
 *
 * @author: tolga
 *
 */
module.exports = ng.module('directives-tcTextareaLimited',[]).directive('tcTextareaLimited', function() {
    /**
     * verilen deger trueString'e esitse 'true', falseString'e esitse 'false', bunlardan ikisine de esit degilse default degeri doner
     */
    function toBooleanWithDefault (str, trueString, falseString, defaultValue) {
        if(_.isEqual(str, trueString)){
            return true;
        }
        else if(_.isEqual(str, falseString)){
            return false;
        }
        else{
            return defaultValue;
        }
    };

    /**
     * 'true','false' string to boolean
     */
    function stringTrueFalseToBooleanWithDefault (str, defaultValue) {
        return toBooleanWithDefault(str, 'true', 'false', defaultValue);
    };

    /**
     * optionValue null veya undefined ise defaultValue degerini doner.
     */
    function getOptionValue (optionValue, defaultValue) {

        if (_.isNull(optionValue) || _.isUndefined(optionValue)) {
            return defaultValue;
        } else {
            return optionValue;
        }
    };


    function link(scope, element, attrs) {
        var maxCharSize = 0;
        if (_.isNaN(attrs.maxCharacterSize * 1)) {
            maxCharSize = scope.$parent[attrs.maxCharacterSize];
        } else {
            maxCharSize = attrs.maxCharacterSize;
        }

        // maxCharacterSize disaridan set edilmelidir
        var options = {
            'consoleLog': stringTrueFalseToBooleanWithDefault(attrs.consoleLog, false),
            'maxCharacterSize': maxCharSize * 1,
            'originalStyle': getOptionValue(attrs.originalStyle, 'input-large' ),
            'warningStyle': getOptionValue(attrs.warningStyle, 'warning input-large'),
            'warningNumber': getOptionValue(attrs.warningNumber, 0),
            'displayFormat': getOptionValue(attrs.displayFormat, 'Toplam:#max, Girilen:#input, Kalan:#left')
        };

        scope.$parent.$watch(attrs.maxCharacterSize, function(value) {
            options.maxCharacterSize = value * 1;
            element.textareaCount(options);
            element.attr('maxlength', options.maxCharacterSize);
        });

        if(options.consoleLog) {
            console.log('scope:');
            console.log(scope);
            console.log('attrs:');
            console.log(attrs);
            console.log('element:');
            console.log(element);
            console.log('options:');
            console.log(options);
        }

        // maxCharacterSize kontrolü
        if( _.isNaN(options.maxCharacterSize*1) && !(options.maxCharacterSize*1 >= 0) ){
            alert('"tcTextareaLimited" directive kullanım hatası: attrs.maxCharacterSize degeri 0 veya daha buyuk bir sayı olmalı. Bu şekilde kullanılamayacağı için "alert" ile hata mesajı verildi. Element id: '+element.context.id);
        }

        element.textareaCount(options);
        element.attr('maxlength', options.maxCharacterSize);

    };

    return {
        restrict: 'A',
        scope: {
            /* isolated scope */
        },
        // templateUrl: 'js/app/directives/tcTextareaLimited.html',
        link: link
    };
});
```
