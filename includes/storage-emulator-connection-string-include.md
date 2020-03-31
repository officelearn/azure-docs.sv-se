---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187547"
---
Lagringsemulatorn stöder ett enda fast konto och en välkänd autentiseringsnyckel för autentisering av delad nyckel. Det här kontot och nyckeln är de enda autentiseringsuppgifter för delad nyckel som tillåts för användning med lagringsemmulatorn. De är:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Autentiseringsnyckeln som stöds av lagringsemulatorn är endast avsedd för testning av funktionerna i klientautentiseringskoden. Det tjänar inte något säkerhetssyfte. Du kan inte använda ditt produktionslagringskonto och nyckel med lagringsemulatorn. Du bör inte använda utvecklingskontot med produktionsdata.
> 
> Lagringsemulatorn stöder endast anslutning via HTTP. HTTPS är dock det rekommenderade protokollet för åtkomst till resurser i ett Azure-lagringskonto för produktion.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ansluta till emulatorkontot med en genväg
Det enklaste sättet att ansluta till lagringsemulatorn från ditt program är att konfigurera en `UseDevelopmentStorage=true`anslutningssträng i programmets konfigurationsfil som refererar till genvägen . Här är ett exempel på en anslutningssträng till lagringsemulatorn i en *app.config-fil:* 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Anslut till emulatorkontot med det välkända kontonamnet och nyckeln
Om du vill skapa en anslutningssträng som refererar till emulatorkontonamnet och nyckeln måste du ange slutpunkterna för var och en av de tjänster som du vill använda från emulatorn i anslutningssträngen. Detta är nödvändigt så att anslutningssträngen refererar till emulatorslutpunkterna, som skiljer sig från dem för ett produktionslagringskonto. Värdet på anslutningssträngen ser till exempel ut så här:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Det här värdet är identiskt `UseDevelopmentStorage=true`med genvägen som visas ovan.

#### <a name="specify-an-http-proxy"></a>Ange en HTTP-proxy
Du kan också ange en HTTP-proxy som ska användas när du testar tjänsten mot lagringsemulatorn. Detta kan vara användbart för att observera HTTP-begäranden och svar medan du felsöker åtgärder mot lagringstjänsterna. Om du vill ange `DevelopmentStorageProxyUri` en proxy lägger du till alternativet i anslutningssträngen och anger dess värde till proxy-URI.To specify a proxy, add the option to the connection string, and set its value to the proxy URI. Här är till exempel en anslutningssträng som pekar på lagringsemulatorn och konfigurerar en HTTP-proxy:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

