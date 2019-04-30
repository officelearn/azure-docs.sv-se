---
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 10/26/2018
ms.date: 01/14/2019
ms.author: v-jay
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125531"
---
Storage-emulatorn stöder ett enda fast konto och en välkänd autentiseringsnyckel för autentisering med delad nyckel. Kontot och nyckeln är de enda delad nyckel-autentiseringsuppgifter som tillåts för användning med storage-emulatorn. De är:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Den autentiseringsnyckel som stöds av storage-emulatorn är avsedd endast för att testa funktionerna i klientkoden för autentisering. Den hanterar inte några något security syfte. Du kan inte använda din produktion lagringskontot och nyckeln med storage-emulatorn. Du bör inte använda kontot utveckling med produktionsdata.
> 
> Lagringsemulatorn stöder endast en anslutning via HTTP. Men är HTTPS det rekommendera protokollet för åtkomst till resurser i en Azure storage-konto.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Anslut till emulatorn-konto med hjälp av en genväg
Det enklaste sättet att ansluta till storage-emulatorn från ditt program är att konfigurera en anslutningssträng i programmets konfigurationsfil som refererar till genvägen `UseDevelopmentStorage=true`. Här är ett exempel på en anslutningssträng till storage-emulatorn i en *app.config* fil: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Anslut till emulatorn-konto med hjälp av det välkända kontonamnet och nyckeln
Om du vill skapa en anslutningssträng som refererar till emulatorn namn och nyckel, måste du ange slutpunkterna för var och en av de tjänster som du vill använda från emulatorn i anslutningssträngen. Detta är nödvändigt för att anslutningssträngen ska referera till slutpunkter emulator, som skiljer sig från dem för ett lagringskonto för produktion. Värdet för anslutningssträngen ska se ut så här:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Det här värdet är identiska med genvägen ovan kan `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Ange en HTTP-proxy
Du kan också ange en HTTP-proxy ska användas när du testar din tjänst mot storage-emulatorn. Detta kan vara användbart för får HTTP-begäranden och svar medan du felsöker åtgärder mot de storage-tjänsterna. Om du vill ange en proxy, lägger du till den `DevelopmentStorageProxyUri` alternativet på anslutningssträngen och ange värdet till proxy-URI. Här är till exempel en anslutningssträng som pekar på storage-emulatorn och konfigurerar en HTTP-proxy:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

