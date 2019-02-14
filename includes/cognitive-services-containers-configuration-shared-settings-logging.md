---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: aa93fb9779e6cda41df52f7979e42348948544ea
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247186"
---
Den `Logging` inställningar hantera ASP.NET Core loggningsstöd för din behållare. Du kan använda samma konfigurationsinställningar och värden för din behållare som du använder för ett ASP.NET Core-program. 

Följande loggning providers som stöds av behållaren:

|Leverantör|Syfte|
|--|--|
|[Konsol](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Felsökning](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Disk](#disk-logging)|Loggningsprovider JSON. Den här loggningsprovider skriver loggdata till utdata mount.|

### <a name="disk-logging"></a>Disk-loggning

Den `Disk` loggningsprovider stöder följande konfigurationsinställningar:  

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Format` | Sträng | Utdataformat för loggfiler.<br/> **Obs!** Det här värdet måste anges till `json` att aktivera loggning-providern. Ett fel inträffar om det här värdet anges utan att också ange en utdata mount vid instansiering en behållare. |
| `MaxFileSize` | Integer | Den maximala storleken i megabyte (MB) på en loggfil. När storleken på den aktuella loggfilen uppfyller eller överskrider detta värde, har en ny loggfil startats med loggningsprovider. Om -1 anges begränsas storleken på loggfilen bara av den maximala filstorleken för utdata mount. Standardvärdet är 1. |

Läs mer om hur du konfigurerar ASP.NET Core loggningsstöd [inställningar filkonfiguration](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).
