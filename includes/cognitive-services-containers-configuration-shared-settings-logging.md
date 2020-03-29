---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320529"
---
Inställningarna `Logging` hanterar ASP.NET Core-loggningsstöd för din behållare. Du kan använda samma konfigurationsinställningar och värden för din behållare som du använder för ett ASP.NET Core-program. 

Följande loggningsleverantörer stöds av behållaren:

|Leverantör|Syfte|
|--|--|
|[Konsol](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Den ASP.NET Core `Console` loggningsleverantören. Alla ASP.NET core-konfigurationsinställningar och standardvärden för den här loggningsleverantören stöds.|
|[Felsökning](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Den ASP.NET Core `Debug` loggningsleverantören. Alla ASP.NET core-konfigurationsinställningar och standardvärden för den här loggningsleverantören stöds.|
|[Disk](#disk-logging)|JSON-loggningsleverantören. Den här loggningsprovidern skriver loggdata till utdatafästet.|

Med det här behållarkommandot lagras loggningsinformation i JSON-format till utdatafästet:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Det här behållarkommandot visar felsökningsinformation, föregås av `dbug`, medan behållaren körs:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Diskloggning

Loggningsprovidern `Disk` stöder följande konfigurationsinställningar:

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Format` | String | Utdataformatet för loggfiler.<br/> **Anm.:** Det här värdet `json` måste anges så att loggningsprovidern kan aktiveras. Om det här värdet anges utan att också ange en utdatamontering medan en behållare instansieras, uppstår ett fel. |
| `MaxFileSize` | Integer | Den maximala storleken, i megabyte (MB), för en loggfil. När storleken på den aktuella loggfilen uppfyller eller överskrider det här värdet startas en ny loggfil av loggningsleverantören. Om -1 anges begränsas loggfilens storlek endast av den maximala filstorleken, om sådan finns, för utdatafästet. Standardvärdet är 1. |

Mer information om hur du konfigurerar ASP.NET Core loggningsstöd finns i [Inställningar filkonfiguration](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

