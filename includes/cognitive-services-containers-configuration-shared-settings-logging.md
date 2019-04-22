---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886845"
---
Den `Logging` inställningar hantera ASP.NET Core loggningsstöd för din behållare. Du kan använda samma konfigurationsinställningar och värden för din behållare som du använder för ett ASP.NET Core-program. 

Följande loggning providers som stöds av behållaren:

|Leverantör|Syfte|
|--|--|
|[Konsol](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Felsökning](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Disk](#disk-logging)|Loggningsprovider JSON. Den här loggningsprovider skriver loggdata till utdata mount.|

Det här kommandot för behållaren lagrar loggningsinformation i JSON-format till utdata mount:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Detta container-kommando visar felsökningsinformation, prefixet `dbug`, medan behållaren körs:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Disk-loggning

Den `Disk` loggningsprovider stöder följande konfigurationsinställningar:  

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Format` | Sträng | Utdataformat för loggfiler.<br/> **Obs!** Det här värdet måste anges till `json` att aktivera loggning-providern. Ett fel inträffar om det här värdet anges utan att också ange en utdata mount vid instansiering en behållare. |
| `MaxFileSize` | Integer | Den maximala storleken i megabyte (MB) på en loggfil. När storleken på den aktuella loggfilen uppfyller eller överskrider detta värde, har en ny loggfil startats med loggningsprovider. Om -1 anges begränsas storleken på loggfilen bara av den maximala filstorleken för utdata mount. Standardvärdet är 1. |

Läs mer om hur du konfigurerar ASP.NET Core loggningsstöd [inställningar filkonfiguration](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

