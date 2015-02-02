---
layout: post
title: AngularJs
---

AngularJs'de tüm button'lara işlem öncesi "Emin misin?" gibi bir kontrol eklemek için aşağıda detayı verilen tcReallyClick adını verdiğim directive'i kullanabilirsiniz.
Bu directive'i ngReallyClick directive'inden uyarladım: [https://gist.github.com/asafge/7430497](https://gist.github.com/asafge/7430497).
Bu şekilde tüm butonlara html ile ayarlanabilen basit bir html tag ile standart bir kontrol ekleyebilirsiniz.

Örnek kullanım:

```HTML
 <button tc-really-click="taskDelete()"
             tc-really-click-message="Task ve bağlı tüm item'lar silinecek. Silmek istediğinden emin misin?"
             tc-really-click-ok-btn-message="Evet, sil"
             tc-really-click-cancel-btn-message="Hayır iptal"
             class="btn btn-sm btn-danger ng-click-active">Sil
 </button>
```

Parametreler:

- tc-really-click: zorunlu. callback ile çağrılacak fnc verilmelidir.
- tc-really-click-message: zorunlu. Ekrana verilecek uyarı mesajı
- tc-really-click-ok-btn-message: opsiyonel. Default "Tamam"
- tc-really-click-cancel-btn-message: opsiyonel. Default "İptal"

reference: ngReallyClick directive'inden uyarlandı: https://gist.github.com/asafge/7430497


**tc-really-click.html:**

```html
<div class="modal-body">
    <span ng-bind="message"></span>
</div>
<div class="modal-footer">
    <button class="btn btn-primary" ng-click="okGo()"><span ng-bind="okBtnMessage"></span></button>
    <button class="btn btn-default" ng-click="cancelModal()"><span ng-bind="cancelBtnMessage"></span></button>
</div>
```


**tc-really-click-ctrl.js:**

```
'use strict';

var ng = require('angular');

/**
 *  tcReallyClick modal controller.
 *
 * @author: tolga
 *
 */
module.exports = function ($scope, $modalInstance, message, okBtnMessage, cancelBtnMessage) {

    $scope.message = message;
    $scope.okBtnMessage = okBtnMessage ? okBtnMessage : 'Tamam';
    $scope.cancelBtnMessage = cancelBtnMessage ? cancelBtnMessage : 'İptal';

    // iptal
    $scope.cancelModal = function () {
        $modalInstance.dismiss('cancel');
    };

    // tamam
    $scope.okGo = function () {
        // $scope.$apply(callback);
        $modalInstance.close('ok');
    };

};
```

**tc-really-click-directive.js:**

```javascript
'use strict';

var ng = require('angular');

/**
 *  @name tcReallyClick
 *
 *  @description
 *   Click sırasında "confirmation" özelliği katar.
 *
 *  @dependency
 *   "angular-ui modal"
 *
 *  @param Attributes:
 *    - tc-really-click: zorunlu. callback ile çağrılacak fnc verilmelidir.
 *    - tc-really-click-message: zorunlu. Ekrana verilecek uyarı mesajı
 *    - tc-really-click-ok-btn-message: opsiyonel. Default "Tamam"
 *    - tc-really-click-cancel-btn-message: opsiyonel. Default "İptal"
 *
 *  @example
 *    <button tc-really-click="taskDelete()"
 *            tc-really-click-message="Task ve bağlı tüm item'lar silinecek. Silmek istediğinden emin misin?"
 *            tc-really-click-ok-btn-message="Evet, sil"
 *            tc-really-click-cancel-btn-message="Hayır iptal"
 *            class="btn btn-sm btn-danger ng-click-active">Sil
 *    </button>
 *
 * @reference: ngReallyClick directive'inden uyarlandı: https://gist.github.com/asafge/7430497
 *
 * @author: tolga
 *
 */
module.exports = function ($modal) {

    function link(scope, element, attrs) {

        element.bind('click', function () {
            var tcReallyClickMessage = attrs.tcReallyClickMessage;
            var modalInstance = $modal.open({
                templateUrl: 'js/directives/tcreallyclick/tc-really-click.html',
                controller: 'TcReallyClickCtrl',
                size: 'sm',
                backdrop: true,
                windowClass: 'tcReallyClick-modal-window',
                resolve: {
                    message: function () {
                        return attrs.tcReallyClickMessage
                    },
                    okBtnMessage: function () {
                        return attrs.tcReallyClickOkBtnMessage
                    },
                    cancelBtnMessage: function () {
                        return attrs.tcReallyClickCancelBtnMessage
                    }

                }
            });

            modalInstance.result.then(function (modalDummyMessage) {
                // setTimeout kullanilmazsa scope digest hatasi veriyor
                setTimeout(function () {
                    scope.$apply(attrs.tcReallyClick);
                }, 0);
            }, function () {
                //console.log('Modal dismissed at: ' + new Date());
            });

        });
    };

    return {
        restrict: 'A',
        link: link
    };
};
```

**index.js:**

```javascript
module.exports = ng.module('directives-tcReallyClick-directive', [])
    .controller('TcReallyClickCtrl', require('./tc-really-click-ctrl'))
    .directive('tcReallyClick', require('./tc-really-click-directive'));
```