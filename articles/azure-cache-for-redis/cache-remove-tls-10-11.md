---
title: Ta bort TLS 1,0 och 1,1 från användning med Azure cache för Redis
description: Lär dig hur du tar bort TLS 1,0 och 1,1 från ditt program vid kommunikation med Azure cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 2f6203deb5e06ba69a3b4d06297d5e702992c79d
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708064"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Ta bort TLS 1,0 och 1,1 från användning med Azure cache för Redis

Det finns en företagsomfattande push-överföring mot exklusiv användning av Transport Layer Security (TLS) version 1,2 eller senare. TLS-versionerna 1,0 och 1,1 är kända för angrepp som BEAST och POODLE, och har andra vanliga svagheter och exponeringar (CVE). De har inte heller stöd för moderna krypterings metoder och chiffersviter som rekommenderas av krav för betal korts bransch (PCI). Den här [TLS-säkerhetsbloggen](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) förklarar några av dessa sårbarheter mer detaljerat.

Som en del av den här ansträngningen gör vi följande ändringar i Azure cache för Redis:

* Från och med 13 januari 2020 kommer vi att konfigurera den lägsta standard-TLS-versionen som ska vara 1,2 för nyligen skapade cache-instanser.  Befintliga instanser av cachen uppdateras inte just nu.  Du får [ändra den lägsta TLS-versionen](cache-configure.md#access-ports) tillbaka till 1,0 eller 1,1 för bakåtkompatibilitet, om det behövs.  Den här ändringen kan göras via Azure Portal eller andra hanterings-API: er.
* Från och med den 31 mars 2020 kommer vi att sluta stödja TLS-versionerna 1,0 och 1,1. Efter den här ändringen måste ditt program använda TLS 1,2 eller senare för att kommunicera med din cache.

Som en del av den här ändringen kommer vi dessutom att ta bort stöd för äldre, osäkra chiffer-paket.  Våra chiffer-paket som stöds är begränsade till följande när cachen är konfigurerad med en lägsta TLS-version på 1,2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Den här artikeln innehåller allmänna råd om hur du identifierar beroenden för dessa tidigare TLS-versioner och hur du tar bort dem från ditt program.

## <a name="check-whether-your-application-is-already-compliant"></a>Kontrol lera om programmet redan är kompatibelt

Det enklaste sättet att ta reda på om ditt program fungerar med TLS 1,2 är att ange det **lägsta TLS** -värdet till TLS 1,2 på ett test-eller mellanlagrings-cache som används. Inställningen för **minsta TLS-version** finns i de [avancerade inställningarna](cache-configure.md#advanced-settings) för din cache-instans i Azure Portal. Om programmet fortsätter att fungera som förväntat efter den här ändringen är det antagligen kompatibelt. Du kan behöva konfigurera vissa redis-klient bibliotek som används av ditt program specifikt för att aktivera TLS 1,2, så att de kan ansluta till Azure cache för Redis över detta säkerhets protokoll.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurera ditt program så att det använder TLS 1,2

De flesta program använder Redis-klient bibliotek för att hantera kommunikation med deras cacheminnen. Här följer instruktioner för hur du konfigurerar några av de populära klient biblioteken, i olika programmeringsspråk och ramverk, för att använda TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis .NET-klienter använder den tidigaste TLS-versionen som standard på .NET Framework 4.5.2 eller tidigare och använder den senaste TLS-versionen på .NET Framework 4,6 eller senare. Om du använder en äldre version av .NET Framework kan du Aktivera TLS 1,2 manuellt:

* **Stackexchange. Redis:** Ange `ssl=true` och `sslprotocols=tls12` i anslutnings strängen.
* **ServiceStack. Redis:** Följ [anvisningarna för ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Redis .NET Core-klienter använder den senaste TLS-versionen som standard.

### <a name="java"></a>Java

Redis Java-klienter använder TLS 1,0 på Java version 6 eller tidigare. Jedis, sallat och Redisson kan inte ansluta till Azure cache för Redis om TLS 1,0 är inaktiverat i cacheminnet. Uppgradera Java Framework för att använda nya TLS-versioner.

För Java 7 använder Redis-klienter inte TLS 1,2 som standard, men kan konfigureras för det. Med Jedis kan du ange de underliggande TLS-inställningarna med följande kodfragment:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Sallat-och Redisson-klienterna har ännu inte stöd för att ange TLS-versionen, så de kommer att brytas om cachen bara accepterar TLS 1,2-anslutningar. Korrigeringar för de här klienterna granskas, så kontrol lera med dessa paket för en uppdaterad version med detta stöd.

I Java 8 används TLS 1,2 som standard och bör inte kräva uppdateringar av klient konfigurationen i de flesta fall. Testa ditt program för att vara säkert.

### <a name="nodejs"></a>Node.js

Node Redis och IORedis använder TLS 1,2 som standard.

### <a name="php"></a>PHP

Predis på PHP 7 fungerar inte eftersom PHP 7 endast stöder TLS 1,0. På PHP-7.2.1 eller tidigare använder Predis TLS 1,0 eller 1,1 som standard. Du kan ange TLS 1,2 när du skapar klient instansen:

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

I PHP 7,3 eller senare använder Predis den senaste TLS-versionen.

PhpRedis stöder inte TLS i valfri PHP-version.

### <a name="python"></a>Python

Redis-py använder TLS 1,2 som standard.

### <a name="go"></a>GO

Redigo använder TLS 1,2 som standard.

## <a name="additional-information"></a>Ytterligare information

- [Så här konfigurerar du Azure cache för Redis](cache-configure.md)
