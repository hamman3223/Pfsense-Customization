# Pfsense Customization 
🔐 A utility tool for user validation by his MAC-address 

# Setup

Link to download Pfsense WorkStation:

https://drive.google.com/drive/folders/1P1TiIz5qcrqkG5ZNhJNShbIdj6qIfbmr?usp=sharing

# Architecture
<p align="center"> 
    <img align="center" alt="Architecture" src="resourses/PFSENSE-Architecture.png" />
</p>

## Backend
<p align="center"> 
    <img align="center" alt="Architecture" src="resourses/PFSENSE-Back-end.png" />
</p>

## Admin panel
<p align="center"> 
    <img align="center" alt="Architecture" src="resourses/PFSENSE-Admin-pannel.png" />
</p>

## Database

База данных находится в директории /var/db/captiveportal%названиеЗоны%
Для экспорта базы данных, можно воспользоваьтся утилитой scp, пример: 
 "scp captiveportal%названиеЗоны% username@172.168.164.1:/Users/username/Desktop" 
 
 ## Geo Location

Существует два варианта реализации сбора информации о гео-локации пользователя: 

### Backend реализация:

Можно раскомментировать следующие строки для реализации: 
В которых присутствуют переменные [$country_region, $country, $region], где $country - массив содержащий значения для $country и $region
https://github.com/YWxtYXoK/Pfsense-Customization/blob/5952dd5e1964d5eb556e38ee317e9b0e36f548d0/src_code/index.php#L196
https://github.com/YWxtYXoK/Pfsense-Customization/blob/5952dd5e1964d5eb556e38ee317e9b0e36f548d0/src_code/index.php#L275

Нужно создать столбец в базе данных, обязательно нужно указать тип значения:
https://github.com/YWxtYXoK/Pfsense-Customization/blame/main/src_code/captiveportal.inc#L1799

И записать их () в базу данных, добавив их в следующие строки
https://github.com/YWxtYXoK/Pfsense-Customization/blame/main/src_code/captiveportal.inc#L2634
https://github.com/YWxtYXoK/Pfsense-Customization/blame/main/src_code/captiveportal.inc#L2636


Данный кусок кода необходимо добавить в captiveportal.inc файл, и провести инициализацию переменной в index.php ( $geo = getLocationInfoByIp($clientip) ). 
После чего необходимо будет толкнуть данную переменную в базу данных, то есть повторить цикл действий, которые были проделаны с $agent в файлах index.php (функция portal_allow(), инициализацию мы уже совершили), captiveportal.inc и statuscaptiveportal.php

Переменная $clienip инициализируется в файле index.php 
https://github.com/pfsense/pfsense/blob/a5fd794bc0f9213a36606c98dafb8835ae2687c7/src/usr/local/captiveportal/index.php#L68

<pre><code>
function getLocationInfoByIp($clientip){
    $ip_data = @json_decode(file_get_contents("http://www.geoplugin.net/json.gp?ip=".$clientip));
    if($ip_data && $ip_data->geoplugin_countryName != null){
        $result['country'] = $ip_data->geoplugin_countryCode;
        $result['city'] = $ip_data->geoplugin_city;
    }
    return $result
}
</code></pre>

### Frontend реализация:

<pre><code>
var options = {
  enableHighAccuracy: true,
  timeout: 5000,
  maximumAge: 0
};

function success(pos) {
  var crd = pos.coords;
};

function error(err) {
  console.warn(`ERROR(${err.code}): ${err.message}`);
};

navigator.geolocation.getCurrentPosition(success, error, options);
</code></pre>
