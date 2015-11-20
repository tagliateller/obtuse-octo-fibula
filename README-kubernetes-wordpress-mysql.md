https://github.com/kubernetes/kubernetes/tree/master/examples/mysql-wordpress-pd

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysql-pod
  labels:
    name: mysql-pod
    context: docker-k8s-lab
spec:
  containers:
    -
      name: mysql
      image: mysql:latest
      env:
        -
          name: "MYSQL_USER"
          value: "mysql"
        -
          name: "MYSQL_PASSWORD"
          value: "mysql"
        -
          name: "MYSQL_DATABASE"
          value: "sample"
        -
          name: "MYSQL_ROOT_PASSWORD"
          value: "supersecret"
      ports:
        -
          containerPort: 3306
```

```console
kubectl create -f mysql.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    name: mysql-pod
    context: docker-k8s-lab
spec:
  ports:
    # the port that this service should serve on
    - port: 3306
  # label keys and values that must match in order to receive traffic for this service
  selector:
    name: mysql-pod
    context: docker-k8s-lab
```

```console
kubectl create -f mysql-service.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: wordpress
  labels: 
    name: wordpress
spec: 
  containers: 
    - image: wordpress
      name: wordpress
      env:
        - name: WORDPRESS_DB_PASSWORD
          # change this - must match mysql.yaml password
          value: supersecret
      ports: 
        - containerPort: 80
          name: wordpress
```

```console
kubectl create -f wordpress.yaml
```

```console
core@kube-00 ~ $ kubectl get pods
NAME        READY     STATUS    RESTARTS   AGE
mysql-pod   1/1       Running   0          41m
wordpress   1/1       Running   2          22m
core@kube-00 ~ $
```

```yaml
apiVersion: v1
kind: Service
metadata: 
  labels: 
    name: wpfrontend
  name: wpfrontend
spec: 
  ports:
    # the port that this service should serve on
    - port: 80
  # label keys and values that must match in order to receive traffic for this service
  selector: 
    name: wordpress
  type: LoadBalancer
```

```console
kubectl create -f wordpress-service.yaml
```

```console
core@kube-00 ~ $ kubectl logs wordpress
index.html
WordPress not found in /var/www/html - copying now...
WARNING: /var/www/html is not empty - press Ctrl+C now if this is an error!
+ ls -A
+ sleep 10
Complete! WordPress has been successfully copied to /var/www/html

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known
core@kube-00 ~ $
```

im zweiten Anlauf geht es dann:

```console
core@kube-00 ~ $ kubectl get services
NAME         LABELS                                    SELECTOR                                IP(S)           PORT(S)
kubernetes   component=apiserver,provider=kubernetes   <none>                                  10.16.0.1       443/TCP
mysql        context=docker-k8s-lab,name=mysql-pod     context=docker-k8s-lab,name=mysql-pod   10.31.91.212    3306/TCP
wpfrontend   name=wpfrontend                           name=wordpress                          10.26.208.151   80/TCP
core@kube-00 ~ $ curl -L http://10.26.208.151:80
```

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" lang="en-US" xml:lang="en-US">
<head>
	<meta name="viewport" content="width=device-width" />
	<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
	<title>WordPress &rsaquo; Installation</title>
	<link rel='stylesheet' id='buttons-css'  href='http://10.26.208.151/wp-includes/css/buttons.min.css?ver=4.3.1' type='text/css' media='all' />
<link rel='stylesheet' id='open-sans-css'  href='https://fonts.googleapis.com/css?family=Open+Sans%3A300italic%2C400italic%2C600italic%2C300%2C400%2C600&#038;subset=latin%2Clatin-ext&#038;ver=4.3.1' type='text/css' media='all' />
<link rel='stylesheet' id='install-css'  href='http://10.26.208.151/wp-admin/css/install.min.css?ver=4.3.1' type='text/css' media='all' />
<link rel='stylesheet' id='dashicons-css'  href='http://10.26.208.151/wp-includes/css/dashicons.min.css?ver=4.3.1' type='text/css' media='all' />
</head>
<body class="wp-core-ui language-chooser">
<h1 id="logo"><a href="https://wordpress.org/" tabindex="-1">WordPress</a></h1>

<form id="setup" method="post" action="?step=1"><label class='screen-reader-text' for='language'>Select a default language</label>
<select size='14' name='language' id='language'>
<option value="" lang="en" selected="selected" data-continue="Continue" data-installed="1">English (United States)</option>
<option value="ar" lang="ar" data-continue="المتابعة">العربية</option>
<option value="ary" lang="ar_MA" data-continue="المتابعة">العربية المغربية</option>
<option value="az" lang="az" data-continue="Davam">Azərbaycan dili</option>
<option value="bg_BG" lang="bg" data-continue="Продължение">Български</option>
<option value="bn_BD" lang="bn" data-continue="এগিয়ে চল.">বাংলা</option>
<option value="bs_BA" lang="bs" data-continue="Nastavi">Bosanski</option>
<option value="ca" lang="ca" data-continue="Continua">Català</option>
<option value="cy" lang="cy" data-continue="Parhau">Cymraeg</option>
<option value="da_DK" lang="da" data-continue="Forts&#230;t">Dansk</option>
<option value="de_DE" lang="de" data-continue="Fortfahren">Deutsch</option>
<option value="de_DE_formal" lang="de" data-continue="Fortfahren">Deutsch (Sie)</option>
<option value="de_CH" lang="de" data-continue="Fortfahren">Deutsch (Schweiz)</option>
<option value="el" lang="el" data-continue="Συνέχεια">Ελληνικά</option>
<option value="en_AU" lang="en" data-continue="Continue">English (Australia)</option>
<option value="en_GB" lang="en" data-continue="Continue">English (UK)</option>
<option value="en_NZ" lang="en" data-continue="Continue">English (New Zealand)</option>
<option value="en_CA" lang="en" data-continue="Continue">English (Canada)</option>
<option value="eo" lang="eo" data-continue="Daŭrigi">Esperanto</option>
<option value="es_ES" lang="es" data-continue="Continuar">Español</option>
<option value="es_CL" lang="es" data-continue="Continuar">Español de Chile</option>
<option value="es_CO" lang="es" data-continue="Continuar">Español de Colombia</option>
<option value="es_VE" lang="es" data-continue="Continuar">Español de Venezuela</option>
<option value="es_MX" lang="es" data-continue="Continuar">Español de México</option>
<option value="es_PE" lang="es" data-continue="Continuar">Español de Perú</option>
<option value="et" lang="et" data-continue="Jätka">Eesti</option>
<option value="eu" lang="eu" data-continue="Jarraitu">Euskara</option>
<option value="fa_IR" lang="fa" data-continue="ادامه">فارسی</option>
<option value="fi" lang="fi" data-continue="Jatka">Suomi</option>
<option value="fr_BE" lang="fr" data-continue="Continuer">Français de Belgique</option>
<option value="fr_FR" lang="fr" data-continue="Continuer">Français</option>
<option value="gd" lang="gd" data-continue="Lean air adhart">Gàidhlig</option>
<option value="gl_ES" lang="gl" data-continue="Continuar">Galego</option>
<option value="haz" lang="haz" data-continue="ادامه">هزاره گی</option>
<option value="he_IL" lang="he" data-continue="להמשיך">עִבְרִית</option>
<option value="hi_IN" lang="hi" data-continue="जारी">हिन्दी</option>
<option value="hr" lang="hr" data-continue="Nastavi">Hrvatski</option>
<option value="hu_HU" lang="hu" data-continue="Tovább">Magyar</option>
<option value="hy" lang="hy" data-continue="Շարունակել">Հայերեն</option>
<option value="id_ID" lang="id" data-continue="Lanjutkan">Bahasa Indonesia</option>
<option value="is_IS" lang="is" data-continue="Áfram">Íslenska</option>
<option value="it_IT" lang="it" data-continue="Continua">Italiano</option>
<option value="ja" lang="ja" data-continue="続ける">日本語</option>
<option value="ko_KR" lang="ko" data-continue="계속">한국어</option>
<option value="lt_LT" lang="lt" data-continue="Tęsti">Lietuvių kalba</option>
<option value="my_MM" lang="my" data-continue="ဆက်လက်လုပ်ေဆာင်ပါ။">ဗမာစာ</option>
<option value="nb_NO" lang="nb" data-continue="Fortsett">Norsk bokmål</option>
<option value="nl_NL" lang="nl" data-continue="Doorgaan">Nederlands</option>
<option value="nn_NO" lang="nn" data-continue="Hald fram">Norsk nynorsk</option>
<option value="oci" lang="oc" data-continue="Contunhar">Occitan</option>
<option value="pl_PL" lang="pl" data-continue="Kontynuuj">Polski</option>
<option value="ps" lang="ps" data-continue="دوام">پښتو</option>
<option value="pt_BR" lang="pt" data-continue="Continuar">Português do Brasil</option>
<option value="pt_PT" lang="pt" data-continue="Continuar">Português</option>
<option value="ro_RO" lang="ro" data-continue="Continuă">Română</option>
<option value="ru_RU" lang="ru" data-continue="Продолжить">Русский</option>
<option value="sk_SK" lang="sk" data-continue="Pokračovať">Slovenčina</option>
<option value="sl_SI" lang="sl" data-continue="Nadaljujte">Slovenščina</option>
<option value="sq" lang="sq" data-continue="Vazhdo">Shqip</option>
<option value="sr_RS" lang="sr" data-continue="Настави">Српски језик</option>
<option value="sv_SE" lang="sv" data-continue="Fortsätt">Svenska</option>
<option value="th" lang="th" data-continue="ต่อไป">ไทย</option>
<option value="tl" lang="tl" data-continue="Magpatuloy">Tagalog</option>
<option value="tr_TR" lang="tr" data-continue="Devam">Türkçe</option>
<option value="ug_CN" lang="ug" data-continue="داۋاملاشتۇرۇش">Uyƣurqə</option>
<option value="uk" lang="uk" data-continue="Продовжити">Українська</option>
<option value="vi" lang="vi" data-continue="Tiếp tục">Tiếng Việt</option>
<option value="zh_CN" lang="zh" data-continue="继续">简体中文</option>
<option value="zh_TW" lang="zh" data-continue="繼續">繁體中文</option>
</select>
<p class="step"><span class="spinner"></span><input id="language-continue" type="submit" class="button button-primary button-large" value="Continue" /></p></form><script type="text/javascript">var t = document.getElementById('weblog_title'); if (t){ t.focus(); }</script>
<script type='text/javascript' src='http://10.26.208.151/wp-includes/js/jquery/jquery.js?ver=1.11.3'></script>
<script type='text/javascript' src='http://10.26.208.151/wp-includes/js/jquery/jquery-migrate.min.js?ver=1.2.1'></script>
<script type='text/javascript'>
/* <![CDATA[ */
var _zxcvbnSettings = {"src":"http:\/\/10.26.208.151\/wp-includes\/js\/zxcvbn.min.js"};
/* ]]> */
</script>
<script type='text/javascript' src='http://10.26.208.151/wp-includes/js/zxcvbn-async.min.js?ver=1.0'></script>
<script type='text/javascript'>
/* <![CDATA[ */
var pwsL10n = {"short":"Very weak","bad":"Weak","good":"Medium","strong":"Strong","mismatch":"Mismatch"};
/* ]]> */
</script>
<script type='text/javascript' src='http://10.26.208.151/wp-admin/js/password-strength-meter.min.js?ver=4.3.1'></script>
<script type='text/javascript' src='http://10.26.208.151/wp-includes/js/underscore.min.js?ver=1.6.0'></script>
<script type='text/javascript'>
/* <![CDATA[ */
var _wpUtilSettings = {"ajax":{"url":"\/wp-admin\/admin-ajax.php"}};
/* ]]> */
</script>
<script type='text/javascript' src='http://10.26.208.151/wp-includes/js/wp-util.min.js?ver=4.3.1'></script>
<script type='text/javascript'>
/* <![CDATA[ */
var userProfileL10n = {"warn":"Your new password has not been saved.","show":"Show","hide":"Hide","cancel":"Cancel","ariaShow":"Show password","ariaHide":"Hide password"};
/* ]]> */
</script>
<script type='text/javascript' src='http://10.26.208.151/wp-admin/js/user-profile.min.js?ver=4.3.1'></script>
<script type='text/javascript' src='http://10.26.208.151/wp-admin/js/language-chooser.min.js?ver=4.3.1'></script>
<script type="text/javascript">
jQuery( function( $ ) {
	$( '.hide-if-no-js' ).removeClass( 'hide-if-no-js' );
} );
</script>
</body>
</html>
core@kube-00 ~ $ 
```
