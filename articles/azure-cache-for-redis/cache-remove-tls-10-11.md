---
title: Ta bort TLS 1.0 och 1.1 från användning med Azure Cache för Redis
description: Lär dig hur du tar bort TLS 1.0 och 1.1 från ditt program när du kommunicerar med Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 809fbe85a9783777d5dbef86357bd5a386bd6f81
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261252"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Ta bort TLS 1.0 och 1.1 från användning med Azure Cache för Redis

Det finns en branschomfattande push mot exklusiv användning av Transport Layer Security (TLS) version 1.2 eller senare. TLS version 1.0 och 1.1 är kända för att vara mottagliga för attacker som BEAST och POODLE, och att ha andra vanliga sårbarheter och exponeringar (CVE) svagheter. De stöder inte heller moderna krypteringsmetoder och chiffersviter som rekommenderas av PCI-efterlevnadsstandarder (Payment Card Industry). Den här [TLS-säkerhetsbloggen](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) förklarar några av dessa sårbarheter mer i detalj.

Som en del av den här satsningen kommer vi att göra följande ändringar i Azure Cache för Redis:

* **Fas 1:** Vi konfigurerar standardminimum TLS-versionen till 1.2 för nyligen skapade cacheinstanser. (Detta brukade vara TLS 1.0.) Befintliga cacheinstanser uppdateras inte just nu. Du får ändra [den minsta TLS-versionen](cache-configure.md#access-ports) tillbaka till 1.0 eller 1.1 för bakåtkompatibilitet, om det behövs. Den här ändringen kan göras via Azure-portalen eller andra hanterings-API:er.
* **Fas 2:** Vi slutar stödja TLS-versionerna 1.0 och 1.1. Efter den här ändringen måste programmet använda TLS 1.2 eller senare för att kommunicera med cacheminnet.

Dessutom, som en del av denna förändring, kommer vi att ta bort stöd för äldre, osäkra cypher sviter.  Våra cypher-sviter som stöds begränsas till följande när cacheminnet konfigureras med en minsta TLS-version av 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Den här artikeln innehåller allmän vägledning om hur du identifierar beroenden på dessa tidigare TLS-versioner och tar bort dem från ditt program.

De datum då ändringarna börjar gälla är:

| Molnet               | Startdatum för fas 1 | Startdatum för fas 2      |
|---------------------|--------------------|-------------------------|
| Azure (globalt)      |  Den 13 januari 2020  | 11 maj 2020 (förlängd) |
| Azure Government    |  Den 13 mars 2020    | Den 11 maj 2020            |
| Azure Tyskland       |  Den 13 mars 2020    | Den 11 maj 2020            |
| Azure Kina         |  Den 13 mars 2020    | Den 11 maj 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Kontrollera om ditt program redan är kompatibelt

Det enklaste sättet att ta reda på om ditt program kommer att fungera med TLS 1.2 är att ställa in **minsta TLS-versionsvärde** till TLS 1.2 på ett test eller mellanlagringscache som används. Inställningen **för minsta TLS-version** finns i [avancerade inställningar](cache-configure.md#advanced-settings) för cacheinstansen i Azure-portalen. Om programmet fortsätter att fungera som förväntat efter den här ändringen är det förmodligen kompatibelt. Du kan behöva konfigurera vissa Redis-klientbibliotek som används av ditt program specifikt för att aktivera TLS 1.2, så att de kan ansluta till Azure Cache för Redis via det säkerhetsprotokollet.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurera programmet så att det använder TLS 1.2

De flesta program använder Redis-klientbibliotek för att hantera kommunikation med sina cacheminnen. Här följer instruktioner för hur du konfigurerar några av de populära klientbiblioteken, i olika programmeringsspråk och ramverk, för att använda TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Redis .NET-klienter använder den tidigaste TLS-versionen som standard på .NET Framework 4.5.2 eller tidigare och använder den senaste TLS-versionen på .NET Framework 4.6 eller senare. Om du använder en äldre version av .NET Framework kan du aktivera TLS 1.2 manuellt:

* **StackExchange.Redis:** Ställ `ssl=true` `sslprotocols=tls12` in och i anslutningssträngen.
* **ServiceStack.Redis:** Följ [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) instruktioner och kräver ServiceStack.Redis v5.6 på ett minimum.

### <a name="net-core"></a>.NET Core

Redis .NET Core-klienter använder som standard den senaste TLS-versionen.

### <a name="java"></a>Java

Redis Java-klienter använder TLS 1.0 på Java version 6 eller tidigare. Jedis, Lettuce och Redisson kan inte ansluta till Azure Cache för Redis om TLS 1.0 är inaktiverat på cacheminnet. Uppgradera java-ramverket så att det använder nya TLS-versioner.

För Java 7 använder Redis-klienter inte TLS 1.2 som standard men kan konfigureras för det. Jedis kan du ange de underliggande TLS-inställningarna med följande kodavsnitt:

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

Lettuce- och Redisson-klienterna har ännu inte stöd för att ange TLS-versionen, så de går sönder om cachen bara accepterar TLS 1.2-anslutningar. Korrigeringar för dessa klienter granskas, så kontrollera med dessa paket för en uppdaterad version med det här stödet.

I Java 8 används TLS 1.2 som standard och bör inte kräva uppdateringar av klientkonfigurationen i de flesta fall. För att vara säker, testa din ansökan.

### <a name="nodejs"></a>Node.js

Nod Redis och IORedis använder TLS 1.2 som standard.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis (predis)
 
* Tidigare versioner än PHP 7: Predis stöder endast TLS 1.0. Dessa versioner fungerar inte med TLS 1.2; måste du uppgradera för att kunna använda TLS 1.2.
 
* PHP 7.0 till PHP 7.2.1: Predis använder endast TLS 1.0 eller 1.1 som standard. Du kan använda följande lösning för att använda TLS 1.2. Ange TLS 1.2 när du skapar klientinstansen:

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

* PHP 7.3 och senare versioner: Predis använder den senaste TLS-versionen.

#### <a name="phpredis"></a>PhpRedis (på en)

PhpRedis stöder inte TLS på någon PHP-version.

### <a name="python"></a>Python

Redis-py använder TLS 1.2 som standard.

### <a name="go"></a>GO

Redigo använder TLS 1.2 som standard.

## <a name="additional-information"></a>Ytterligare information

- [Konfigurera Azure Cache för Redis](cache-configure.md)
