---
title: Azure Application Insights OpenCensus distribuerad spårning av lokala vidarebefordrare | Microsoft docs
description: Vidarebefordra OpenCensus distribuerad spårning och sträcker sig från språk som Python och gå till Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: 39d6b586158ce292f3697af0f7241a3858842374
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722232"
---
# <a name="local-forwarder"></a>Lokal vidarebefordrare

Lokala vidarebefordraren är en agent som samlar in Application Insights eller [OpenCensus](https://opencensus.io/) telemetri från en mängd olika SDK: er och skickar det till Application Insights. Det kan köras under Windows och Linux. Du kan också att kunna köra denna under macOS, men som officiellt stöds inte just nu.

## <a name="running-local-forwarder"></a>Kör lokala vidarebefordrare

Lokala vidarebefordraren är en [projekt med öppen källkod på GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Det finns en mängd olika sätt att köra lokala vidarebefordraren på flera plattformar.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows-tjänst

Det enklaste sättet för att köra lokala vidarebefordrare under Windows är genom att installera den som en Windows-tjänst. Versionen levereras med en körbar fil för Windows (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) som enkelt kan registreras med operativsystemet.

> [!NOTE]
> Den lokala vidarebefordrartjänsten kräver minst .NET Framework 4.7. Om du inte har .NET Framework 4.7 tjänsten startar installation, men inte. Åtkomst till den senaste versionen av .NET Framework **[gå till hämtningssidan för .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)**.

1. Ladda ned LF. WindowsServiceHost.zip filen från den [lokala vidarebefordrare versionen sidan](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) på GitHub.

    ![Skärmbild av lokala vidarebefordrare hämtningssidan för versionen](.\media\opencensus-local-forwarder\001-local-forwarder-windows-service-host-zip.png)

2. I det här exemplet för att underlätta demonstration extraherar vi bara ZIP-filen till sökvägen `C:\LF-WindowsServiceHost`.

    Registrera tjänsten och konfigurera den för att starta vid systemstart kör du följande från Kommandotolken som administratör:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Du bör få svaret:
    
    `[SC] CreateService SUCCESS`
    
    Undersöka din nya tjänst via tjänster GUI-typ ``services.msc``
        
     ![Skärmbild av lokala vidarebefordrartjänst](.\media\opencensus-local-forwarder\002-services.png)

3. **Högerklicka på** nya lokala vidarebefordrare och välj **starta**. Tjänsten kommer nu att ange tillståndet.

4. Som standard skapas tjänsten utan några åtgärder för återställning. Du kan **högerklickar du på** och välj **egenskaper** > **Recovery** att konfigurera automatiska svar till ett tjänstfel.

    Eller om du vill ange alternativ för automatisk återställning programmässigt för när fel uppstår, kan du använda:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. På samma plats som din ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` fil, som i det här exemplet är ``C:\LF-WindowsServiceHost`` det finns en fil med namnet ``LocalForwarder.config``. Det här är en XML-baserade-fil där du kan justera konfigurationen av din localforwader och ange instrumenteringsnyckeln för Application Insights-resurs som du vill att distribuerad spårningsdata som vidarebefordras. 

    När du har redigerat den ``LocalForwarder.config`` filen om du vill lägga till din instrumentationsnyckel måste du starta om den **lokala Vidarebefordrartjänst** så att ändringarna ska börja gälla.
    
6. Bekräfta att inställningarna är uppfyllda och att den lokala vidarebefordraren lyssnar för spårningsdata som förväntade Kontrollera den ``LocalForwarder.log`` filen. Du bör se resultat som liknar bilden nedan längst ned i filen:

    ![Skärmbild av LocalForwarder.log fil](.\media\opencensus-local-forwarder\003-log-file.png)

#### <a name="console-application"></a>Konsolprogram

För vissa användningsfall, kan det vara bra att köra lokala vidarebefordrare som ett konsolprogram. Versionen levereras med följande körbara versioner av konsolvärden:
* en framework-beroende .NET Core binär */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. När du kör den här binärfilen måste en .NET Core runtime ska installeras. referera till den här nedladdningen [sidan](https://www.microsoft.com/net/download/dotnet-core/2.1) mer information.
```batchfile
E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```
* en fristående .NET Core uppsättning binärfiler för x86 och x64 plattformar. Dessa kräver inte .NET Core runtime ska köras. */ConsoleHost/Win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
```batchfile
E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
```

### <a name="linux"></a>Linux

Precis som med Windows, kommer versionen med följande körbara versioner av konsolvärden:
* en framework-beroende .NET Core binär */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. När du kör den här binärfilen måste en .NET Core runtime ska installeras. referera till den här nedladdningen [sidan](https://www.microsoft.com/net/download/dotnet-core/2.1) mer information.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* en självständig .NET Core uppsättning binärfiler för linux-64. Du behöver den här .NET Core runtime ska köras. */ConsoleHost/Linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Många Linux-användare vill köra lokala vidarebefordrare som en daemon. Linux-system levereras med en mängd olika lösningar för tjänsthantering som Upstart, sysv eller systemd. Vad din specifika version är kan använda du den för att köra lokala vidarebefordraren på ett sätt som passar bäst för ditt scenario.

Exempelvis kan vi skapa en daemontjänst med systemd. Vi använder framework-beroende-version, men kan göra samma sak för en självständig samt.

* Skapa följande service-fil med namnet *localforwarder.service* och placera den i */lib/systemd/system*.
Det här exemplet förutsätter vi ditt användarnamn är SAMPLE_USER och du har kopierat lokala vidarebefordrare framework-beroende binärfiler (från */ConsoleHost/publicera*) till */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

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

* Kör följande kommando för att instruera systemd att starta lokal vidarebefordrare på varje start

```
systemctl enable localforwarder
```

* Kör följande kommando för att instruera systemd att starta vidarebefordrare för lokala direkt

```
systemctl start localforwarder
```

* Övervaka tjänsten genom att kontrollera **.log* filer i katalogen /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Lokala vidarebefordrare kan fungera med macOS, men det är för närvarande inte stöds officiellt.

### <a name="self-hosting"></a>Lokal värd
Lokala vidarebefordrare distribueras också som en .NET Standard NuGet-paketet, så att du kan lägga upp den i ditt eget .NET-program.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Konfigurera lokala vidarebefordrare

* När du kör en av lokala vidarebefordrarens egna värdarna (Konsolvärden eller värden för Windows-tjänsten), hittar du **LocalForwarder.config** placeras bredvid den binära filen.
* När lokal värd för lokala vidarebefordraren NuGet, konfigurationen av samma format måste anges i koden (se avsnittet på lokal värd). Configuration-syntax finns i [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) i GitHub-lagringsplatsen. 

> [!NOTE]
> Konfigurationen kan ändra från version till version, så ta hänsyn till vilken version du använder.

## <a name="monitoring-local-forwarder"></a>Övervaka lokala vidarebefordrare

Spårningar skrivs till filsystemet bredvid den körbara filen som kör lokala vidarebefordrare (leta efter **.log* filer). Du kan placera en fil med namnet *NLog.config* bredvid den körbara filen för din egen konfiguration i stället för standardvärdet. Se [dokumentation](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) beskrivning av formatet.

Om ingen konfigurationsfil anges (som är standard), lokala vidarebefordrare använder standardkonfigurationen, som du hittar [här](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Nästa steg

* [Öppna inventering](https://opencensus.io/)
