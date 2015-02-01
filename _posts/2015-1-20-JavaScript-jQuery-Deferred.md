---
layout: post
title: jQuery Deferred
---
jQuery Deferred ile alakalı güzel bir makale: http://www.erichynds.com/jquery/using-deferreds-in-jquery/ JQuery Deferred API: http://api.jquery.com/category/deferred-object/ 
deferred.resolve 

```
one.json içeriği: {"my_result":"siyah beyaz", "my_code":"101"}
```

```
    var request = $.ajax({
        type: "get",
        url: "one.json",
        dataType: "json"
    });

    request = request.pipe(function (value) {
        console.log("called:"+ value.my_code);
        if (value.my_code == 101) {
            return value.my_code * 2;
        } else {
            return $.Deferred().reject(value);
        }
    });

    request.then(
        function (response) {
            // Log the success.
            console.log("Success!!!", response);
        },
        function (response) {
            // Log the failure.
            console.log("Fail!!!", response);
        }
    );
```

output:
```
  called:101
  Success!!! 202 
```  
