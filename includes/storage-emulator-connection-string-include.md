---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67187547"
---
Storage-emulatorn stöder ett enda fast konto och en välkänd autentiseringsnyckel för autentisering med delad nyckel. Det här kontot och nyckeln är de enda autentiseringsuppgifterna för delad nyckel som tillåts för användning med Storage-emulatorn. De är:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Den autentiseringsnyckel som stöds av lagringsprovidern är endast avsedd för att testa funktionen hos din klientautentisering. Det fungerar inte i något säkerhets syfte. Du kan inte använda ditt produktions lagrings konto och din nyckel med Storage-emulatorn. Du bör inte använda utvecklings kontot med produktions data.
> 
> Storage-emulatorn stöder endast anslutning via HTTP. HTTPS är dock det rekommenderade protokollet för att få åtkomst till resurser i ett Azure Storage-konto för produktion.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ansluta till emulator-kontot med hjälp av en genväg
Det enklaste sättet att ansluta till lagrings emulatorn från ditt program är att konfigurera en anslutnings sträng i programmets konfigurations fil som refererar till genvägen `UseDevelopmentStorage=true`. Här är ett exempel på en anslutnings sträng till Storage-emulatorn i en *app. config* -fil: 

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
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Värdet är identiskt med genvägen som visas ovan `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Ange en HTTP-proxy
Du kan också ange en HTTP-proxy som ska användas när du testar tjänsten mot Storage-emulatorn. Detta kan vara användbart för att observera HTTP-begäranden och svar när du felsöker åtgärder mot lagrings tjänsterna. Om du vill ange en proxy lägger `DevelopmentStorageProxyUri` du till alternativet i anslutnings strängen och anger värdet till proxy-URI: n. Här är till exempel en anslutnings sträng som pekar på Storage-emulatorn och konfigurerar en HTTP-proxy:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

