语言切换根本解决办法，就是在文字被渲染到页面之前就该输入对应语言的文字。

好比当前在英文状态下的 '你好！世界。'，我们就应该在显示到页面之前就需要将它`解析`为 'Hello The world.' 。

而这个 `解析` 需要做的事，就是需要判断当前的语言，并且返回对应语言的文字如：

      
        //fnLanSwitch( '需翻译的字段' )
        function fnLanSwitch( _txt ){
            // 为了开发方便，开发时默认字体为中文简体。所以这个键直接使用需要翻译的文字
            var _JSON = {
                '你好！世界。': {
                    'en-us': 'Hello The world.'
                    // 这里添加更多语言
                }
                // 其他文字
            };

            if( _txt in _JSON ){
                // 获取当前浏览器语言
                var _lan = (navigator.language || navigator.browserLanguage).toLowerCase();

                if( _lan !== 'zh-cn' ){
                    // 判断是否有对应的翻译
                    if( _JSON[ _txt ][ _lan ] ){
                            return _JSON[ _txt ][ _lan ];
                    }else{
                        return _txt;
                    }
                }else{
                    return _txt;
                }
            }else{
                return _txt;
            }
        }

应用内有手动切换语言，只需要做一个 localstorage 本地缓存即可：

localstorage.setItem( 'fnLanSwitch', 'en-us' );

然后在 fnLanSwitch 方法获取语言时，将本地设置的语言优先级放到最高：

        ……
        // 获取当前浏览器语言
        var _lan = (localstorage.getItem( 'fnLanSwitch' ) || navigator.language || navigator.browserLanguage).toLowerCase();
        ……

使用方法：

1. 使用 HTML 字符串拼接：

        var _html = '<div>'+fnLanSwitch( '你好！世界。' )+'</div>';

2. 使用 doT 模版引擎（ 推荐使用 ）：

    HTML

            <script type="text/template" id="doT-Template">
                <div>{{=fnLanSwitch( '你好！世界。' )}}</div>
            </script>
            <div id="doT-Html"></div>

    JS

            <script type="text/javascript">
                document.querySelector( '#doT-Html' ).innerHTML = doT.template( document.querySelector( '#doT-Template' ).innerHTML )();
            </script>

3. 使用 angularJS：

    HTML

        <div>{{'你好！世界。' | fLanguage}}</div>

    JS

        app.filter('fLanSwitch', ['$sce', function ($sce) {
            return function( _txt ) {
                return fnLanSwitch( _txt );
            };
        }]);
    
doT 实例[DEMO](https://github.com/apicloudcom/Demo-LanguageSwitching)