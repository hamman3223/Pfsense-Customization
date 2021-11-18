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

Backend реализация:
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


