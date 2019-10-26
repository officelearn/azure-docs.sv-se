---
title: Ta bort användningen av TLS 1,0 och 1,1 med Azure cache för Redis | Microsoft Docs
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
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901903"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Ta bort användningen av TLS 1,0 och 1,1 med Azure cache för Redis

Det finns en företagsomfattande push-överföring mot användning av TLS 1,2 eller högre. TLS-versionerna 1,0 och 1,1 är kända för att vara mottagliga för attacker som BEAST och POODLE och har andra vanliga sårbarheter och exponeringar (CVE). De har inte heller stöd för moderna krypterings metoder och chiffersviter som rekommenderas av PCI-kompatibla standarder. Den här [TLS-säkerhetsbloggen](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) förklarar några av dessa sårbarheter i mer information.

Även om inga av dessa medför omedelbara problem bör du överväga att flytta bort från att använda TLS 1,0 och 1,1 så tidigt som möjligt. Azure cache för Redis kommer att sluta stödja dessa TLS-versioner från och med den 31 mars 2020. Ditt program måste använda minst TLS 1,2 för att kunna kommunicera med ditt cacheminne efter det här datumet.

Den här artikeln innehåller allmänna råd om hur du identifierar och tar bort dessa beroenden från ditt program.

## <a name="check-if-your-application-is-already-compliant"></a>Kontrol lera om programmet redan är kompatibelt

Det enklaste sättet att ta reda på om ditt program kommer att fungera med TLS 1,2 är att ange den lägsta TLS-versionen på ett test-eller mellanlagrings-cache som används för TLS 1,2. Du kan se inställningen för minsta TLS-version i de [avancerade inställningarna](cache-configure.md#advanced-settings) för din cache-instans i Azure Portal. Om programmet fortsätter att fungera som förväntat efter den här ändringen är det troligt vis kompatibelt. Vissa redis-klient bibliotek som används av vårt program kan behöva konfigureras för att aktivera TLS 1,2 för att kunna ansluta till Azure cache för Redis över detta säkerhets protokoll.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurera ditt program så att det använder TLS 1,2

De flesta program använder Redis-klient bibliotek för att hantera kommunikation med sina cacheminnen. Nedan visas instruktioner om hur du konfigurerar några av de populära klient biblioteken i olika programmeringsspråk och ramverk för att använda TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Redis .NET-klienter använder den lägsta TLS-versionen som standard på .NET Framework 4.5.2 eller under den högsta TLS-versionen på 4,6 eller senare. Om du använder en äldre version av .NET Framework kan du Aktivera TLS 1,2 manuellt:

* StackExchange. Redis: Ange `ssl=true` och `sslprotocls=tls12` i anslutnings strängen.
* ServiceStack. Redis: Följ [de här anvisningarna](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Redis .NET Core-klienter använder den högsta TLS-versionen som standard.

### <a name="java"></a>Java

Redis Java-klienter använder TLS 1,0 på Java version 6 eller senare. Jedis, sallat och Radisson kommer inte att kunna ansluta till Azure cache för Redis om TLS 1,0 är inaktive rad i cacheminnet. Det finns för närvarande ingen känd lösning.

På Java 7 eller senare använder Redis-klienter inte TLS 1,2 som standard, men kan konfigureras för det. Sallat och Radisson har inte stöd för detta just nu. De kommer att brytas om cachen bara accepterar TLS 1,2-anslutningar. Med Jedis kan du ange de underliggande TLS-inställningarna med följande kodfragment:

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

Predis på PHP 7 fungerar inte eftersom den senare bara har stöd för TLS 1,0. På PHP-7.2.1 eller nedan använder Predis TLS 1,0 eller 1,1 som standard. Du kan ange TLS 1,2 när du instansierar klienten:

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
