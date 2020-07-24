---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070536"
---
Azurite stöder ett enda fast konto och en välkänd autentiseringsnyckel för autentisering med delad nyckel. Det här kontot och nyckeln är de enda autentiseringsuppgifterna för delad nyckel som tillåts för användning med Azurite. De är:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Den autentiseringsnyckel som stöds av Azurite är endast avsedd för att testa funktionen hos din klientautentisering. Det fungerar inte i något säkerhets syfte. Du kan inte använda ditt produktions lagrings konto och din nyckel med Azurite. Du bör inte använda utvecklings kontot med produktions data.
> 
> Azurite stöder endast anslutning via HTTP. HTTPS är dock det rekommenderade protokollet för att få åtkomst till resurser i ett Azure Storage-konto för produktion.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ansluta till emulator-kontot med hjälp av en genväg
Det enklaste sättet att ansluta till Azurite från ditt program är att konfigurera en anslutnings sträng i programmets konfigurations fil som refererar till genvägen `UseDevelopmentStorage=true` . Här är ett exempel på en anslutnings sträng till Azurite i en *app.config* -fil: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Anslut till emulator-kontot med hjälp av det välkända konto namnet och nyckeln
Om du vill skapa en anslutnings sträng som refererar till emulatorns konto namn och nyckel måste du ange slut punkter för var och en av de tjänster som du vill använda från emulatorn i anslutnings strängen. Detta är nödvändigt för att anslutnings strängen ska referera till emulatorns slut punkter, som skiljer sig från dem för ett produktions lagrings konto. Till exempel ser värdet för anslutnings strängen ut så här:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Värdet är identiskt med genvägen som visas ovan `UseDevelopmentStorage=true` .
