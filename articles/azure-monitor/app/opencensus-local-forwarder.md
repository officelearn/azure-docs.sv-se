---
title: Azure Application Insights openinsights Local forwarder (för hands version)
description: Vidarebefordring av fördelade spår i openinsights och sträcker sig från språk som python och gå till Azure Application Insights
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.openlocfilehash: bcf7ba495897eb1c9b40c78f00825e863390b5d1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669972"
---
# <a name="local-forwarder-preview"></a>Lokal vidarebefordrare (förhands granskning)

Lokal vidarebefordrare är en agent som samlar in telemetri för Application Insights eller Open- [räkning](https://opencensus.io/) från flera SDK: er och dirigerar dem till Application Insights. Den kan köras under Windows och Linux. Du kanske också kan köra det under macOS, men det är inte officiellt support för tillfället.

## <a name="running-local-forwarder"></a>Lokal vidarebefordrare körs

Lokal vidarebefordrare är ett [projekt med öppen källkod på GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Det finns flera olika sätt att köra lokal vidarebefordrare på flera plattformar.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows Service

Det enklaste sättet att köra lokal vidarebefordrare under Windows är att installera det som en Windows-tjänst. Versionen levereras med Windows-tjänstens körbara fil (*WindowsServiceHost/Microsoft. LocalForwarder. WindowsServiceHost. exe*) som enkelt kan registreras med operativ systemet.

> [!NOTE]
> Den lokala vidarebefordrings tjänsten kräver minst .NET Framework 4,7. Om du inte har .NET Framework 4,7 kommer tjänsten att installeras, men den startar inte. Du kommer åt den senaste versionen av .NET Framework **[på hämtnings sidan för .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Hämta LF. WindowsServiceHost. zip-fil från den [lokala vidarebefordrare-sidan](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) på GitHub.

    ![Skärm bild av den lokala sidan för versions hämtning för vidarebefordrare](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. I det här exemplet är det enkelt att extrahera. zip-filen till sökvägen `C:\LF-WindowsServiceHost`.

    Om du vill registrera tjänsten och konfigurera den så att den startar vid system start kör du följande från kommando raden som administratör:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Du bör få svar på:
    
    `[SC] CreateService SUCCESS`
    
    För att undersöka den nya tjänsten via användar gränssnittet för tjänster ``services.msc``
        
     ![Skärm bild av lokal vidarebefordrare-tjänst](./media/opencensus-local-forwarder/002-services.png)

3. **Högerklicka på** den nya lokala vidarebefordraren och välj **Starta**. Tjänsten kommer nu att övergå till ett kör tillstånd.

4. Som standard skapas tjänsten utan återställnings åtgärder. Du kan **högerklicka på** och välja **Egenskaper** > **återställning** om du vill konfigurera automatiska svar på ett tjänst haveri.

    Eller om du föredrar att ställa in automatiska återställnings alternativ program mässigt för när fel uppstår kan du använda:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. I samma plats som ``Microsoft.LocalForwarder.WindowsServiceHost.exe``-filen, som i det här exemplet är ``C:\LF-WindowsServiceHost`` finns det en fil med namnet ``LocalForwarder.config``. Det här är en XML-baserad fil som gör att du kan justera konfigurationen för din localforwader och ange Instrumentation-nyckeln för den Application Insights resurs som du vill vidarebefordra dina distribuerade spårnings data till. 

    När du har redigerat ``LocalForwarder.config``-filen för att lägga till din Instrumentation-nyckel, måste du starta om den **lokala vidarebefordraren** för att ändringarna ska börja gälla.
    
6. För att bekräfta att dina önskade inställningar är på plats och att den lokala vidarebefordraren lyssnar efter spårnings data som förväntat kontrollerar du ``LocalForwarder.log`` filen. Du bör se resultat som liknar bilden nedan längst ned i filen:

    ![Skärm bild av filen LocalForwarder. log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Konsol program

För vissa användnings fall kan det vara bra att köra lokal vidarebefordrare som ett konsol program. Versionen levereras med följande körbara versioner av konsol värden:
* en Ramverks beroende .NET Core Binary- */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Körning av denna binärfil kräver att en .NET Core-körning installeras. Mer information finns på den här hämtnings [sidan](https://www.microsoft.com/net/download/dotnet-core/2.1) .
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* en fristående .NET Core-uppsättning binärfiler för x86-och x64-plattformar. De kräver inte att .NET Core runtime körs. */ConsoleHost/Win-x86/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/Win-x64/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Precis som med Windows levereras versionen med följande körbara versioner av konsol värden:
* en Ramverks beroende .NET Core Binary- */ConsoleHost/Publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Körning av denna binärfil kräver att en .NET Core-körning installeras. Mer information finns på den här hämtnings [sidan](https://www.microsoft.com/net/download/dotnet-core/2.1) .

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* en fristående .NET Core-uppsättning binärfiler för Linux-64. Detta kräver inte att .NET Core runtime körs. */ConsoleHost/linux-x64/Publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Många Linux-användare vill köra lokal vidarebefordrare som en daemon. Linux-system har en mängd olika lösningar för Service Management, till exempel uppstarter, SYSV eller system. Oavsett vilken version du har, kan du använda den för att köra en lokal vidarebefordrare på ett sätt som passar bäst för ditt scenario.

Vi kan till exempel skapa en daemon-tjänst med system. Vi använder den Ramverks beroende versionen, men samma kan göras för en självständig och fristående.

* skapa följande tjänst fil med namnet *localforwarder. service* och placera den i */lib/systemd/system*.
Det här exemplet förutsätter att ditt användar namn är SAMPLE_USER och att du har kopierat lokala distributioner som är beroende av lokala vidarebefordrare (från */ConsoleHost/Publish*) till */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Kör följande kommando för att instruera systemet att starta den lokala vidarebefordraren vid varje start

```
systemctl enable localforwarder
```

* Kör följande kommando för att instruera systemet att starta den lokala vidarebefordraren direkt

```
systemctl start localforwarder
```

* Övervaka tjänsten genom att kontrol lera * *. log* -filerna i katalogen/Home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Den lokala vidarebefordraren kan fungera med macOS, men den stöds för närvarande inte officiellt.

### <a name="self-hosting"></a>Egen värd
Den lokala vidarebefordraren distribueras också som ett .NET standard NuGet-paket, så att du kan vara värd för det i ditt eget .NET-program.

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Konfigurera lokal vidarebefordrare

* När du kör en lokal vidarebefordrare egna värdar (konsol värd eller Windows-tjänst värd), kommer du att se att **LocalForwarder. config** placeras bredvid binärfilen.
* När du själv är värd för den lokala vidarebefordraren NuGet måste konfigurationen av samma format anges i kod (se avsnittet om egen värd). För konfigurationens syntax kontrollerar du [LocalForwarder. config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) i GitHub-lagringsplatsen. 

> [!NOTE]
> Konfigurationen kan ändras från lansering till release, så var noga med vilken version du använder.

## <a name="monitoring-local-forwarder"></a>Övervaka lokal vidarebefordrare

Spår skrivs ut till fil systemet bredvid den körbara fil som kör lokal vidarebefordrare (leta efter * *. log* -filer). Du kan placera en fil med namnet *NLog. config* bredvid den körbara filen för att ange en egen konfiguration i stället för standardvärdet. Se [dokumentationen](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) för formatet.

Om ingen konfigurations fil anges (vilket är standard) använder den lokala vidarebefordraren standard konfigurationen som du hittar [här](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Nästa steg

* [Öppen inventering](https://opencensus.io/)
