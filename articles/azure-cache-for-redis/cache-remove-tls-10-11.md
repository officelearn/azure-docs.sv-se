---
title: Ta bort TLS 1,0 och 1,1 från användning med Azure cache för Redis | Microsoft Docs
description: Lär dig hur du tar bort TLS 1,0 och 1,1 från ditt program vid kommunikation med Azure cache för Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 638ce4b07201453d0bb00d3610cb695b72b6d698
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806761"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Ta bort TLS 1,0 och 1,1 från användning med Azure cache för Redis

Det finns en företagsomfattande push-överföring mot exklusiv användning av Transport Layer Security (TLS) version 1,2 eller senare. TLS-versionerna 1,0 och 1,1 är kända för angrepp som BEAST och POODLE, och har andra vanliga svagheter och exponeringar (CVE). De har inte heller stöd för moderna krypterings metoder och chiffersviter som rekommenderas av krav för betal korts bransch (PCI). Den här [TLS-säkerhetsbloggen](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) förklarar några av dessa sårbarheter mer detaljerat.

Även om inget av dessa överväganden innebär ett omedelbart problem, rekommenderar vi att du slutar använda TLS 1,0 och 1,1 snart. Azure cache för Redis kommer att sluta stödja dessa TLS-versioner den 31 mars 2020. Efter det datumet måste ditt program använda TLS 1,2 eller senare för att kommunicera med din cache.

Den här artikeln innehåller allmänna råd om hur du identifierar beroenden för dessa tidigare TLS-versioner och hur du tar bort dem från ditt program.

## <a name="check-whether-your-application-is-already-compliant"></a>Kontrol lera om programmet redan är kompatibelt

Det enklaste sättet att ta reda på om ditt program fungerar med TLS 1,2 är att ange det **lägsta TLS** -värdet till TLS 1,2 på ett test-eller mellanlagrings-cache som används. Inställningen för **minsta TLS-version** finns i de [avancerade inställningarna](cache-configure.md#advanced-settings) för din cache-instans i Azure Portal. Om programmet fortsätter att fungera som förväntat efter den här ändringen är det antagligen kompatibelt. Du kan behöva konfigurera vissa redis-klient bibliotek som används av ditt program specifikt för att aktivera TLS 1,2, så att de kan ansluta till Azure cache för Redis över detta säkerhets protokoll.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurera ditt program så att det använder TLS 1,2

De flesta program använder Redis-klient bibliotek för att hantera kommunikation med deras cacheminnen. Här följer instruktioner för hur du konfigurerar några av de populära klient biblioteken, i olika programmeringsspråk och ramverk, för att använda TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis .NET-klienter använder den tidigaste TLS-versionen som standard på .NET Framework 4.5.2 eller tidigare och använder den senaste TLS-versionen på .NET Framework 4,6 eller senare. Om du använder en äldre version av .NET Framework kan du Aktivera TLS 1,2 manuellt:

* **Stackexchange. Redis:** Ange `ssl=true` och `sslprotocls=tls12` i anslutnings strängen.
* **ServiceStack. Redis:** Följ [anvisningarna för ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Redis .NET Core-klienter använder den senaste TLS-versionen som standard.

### <a name="java"></a>Java

Redis Java-klienter använder TLS 1,0 på Java version 6 eller tidigare. Jedis, sallat och Radisson kan inte ansluta till Azure cache för Redis om TLS 1,0 är inaktiverat i cacheminnet. Det finns för närvarande ingen känd lösning.

På Java 7 eller senare använder Redis-klienterna inte TLS 1,2 som standard, men kan konfigureras för det. Sallat och Radisson har inte stöd för den här konfigurationen just nu. De kommer att brytas om cachen bara accepterar TLS 1,2-anslutningar. Med Jedis kan du ange de underliggande TLS-inställningarna med följande kodfragment:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

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
