---
title: "Felsökning Docker-klienten i Windows med hjälp av Visual Studio | Microsoft Docs"
description: "Felsöka problem som kan uppstå när du använder Visual Studio för att skapa och distribuera webbprogram till Docker i Windows med hjälp av Visual Studio."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-visual-studio-docker-development"></a>Felsökning av Visual Studio Docker-utveckling

När du arbetar med Visual Studio Tools för Docker Preview kan vissa problem uppstå på grund av arten av förhandsgranskningen.
Följande är några vanliga problem och lösningar.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux-behållare**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Skapa fel uppstår när du felsöker ett .NET Core webb- eller konsolen program  

Detta kan vara relaterat till inte dela enheten där projektet finns med Docker för Windows.  Du kan få ett felmeddelande som liknar följande:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Så här löser du problemet:

1. Högerklicka på **Docker för Windows** i meddelandefältet och välj sedan **inställningar**.  
2. Välj **delade enheter** och dela den enhet där projektet finns.

### <a name="windows-containers"></a>**Windows-behållare**

Följande är specifika för felsökning av .NET Framework-webb- och konsolen program i Windows-behållare.

#### <a name="prerequisites"></a>Krav

1. Visual Studio 2017 RC (eller senare) med .NET Core och Docker Preview arbetsbelastning måste vara installerad.
2. Uppdatering av Windows 10 årsdagar med den senaste Windows Update korrigeringsfiler. Mer specifikt [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016) måste vara installerad. 
3. [Docker för Windows](https://docs.docker.com/docker-for-windows/) (skapa 1.13.0 eller senare) måste vara installerad.
4. **Växla till Windows-behållare** måste väljas. I meddelandefältet klickar du på **Docker för Windows**, och välj sedan **växla till Windows-behållare**. Se till att den här inställningen sparas när datorn startas om.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>Konsolens utdata visas inte i Visual Studio utdata när du felsöker ett konsolprogram

Detta är ett känt problem med Visual Studio-felsökaren (msvsmon.exe) som för närvarande inte för det här scenariot. Stöd för det här scenariot kan ingå i framtida versioner. Utdata från konsolprogram i Visual Studio, Använd **Docker: starta projektet**, vilket motsvarar **starta utan Debugging**.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>Felsökning av webbprogram med versionen av konfigurationen inte lyckas med (403) förbjuden-fel

Undvik problemet genom att öppna web.release.config i lösningen och kommentera ut eller ta bort följande rader:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux-behållare**

#### <a name="unable-to-validate-volume-mapping"></a>Det gick inte att verifiera mappningen för volym
Mappning av volym krävs för att dela källkoden och binärfilerna för ditt program med app-mappen i behållaren.  Specifika volymen mappningar ingår i docker-compose.dev.debug.yml och docker-compose.dev.release.yml. När filer ändras på värddatorn, de här ändringarna i en liknande mappstruktur behållarna.

Aktivera mappning för volymen:

1. Klicka på **Moby** i meddelandefältet och välj **inställningar**.
2. Välj **delade enheter**.
3. Välj den enhet som är värd för ditt projekt och den enhet där % USERPROFILE % finns.
4. Klicka på **Använd**.

Om du vill testa om volymen mappning fungerar, återskapa och välj F5 från Visual Studio när en eller flera enheter har delat eller kör följande kod från en kommandotolk.

> [!NOTE]
> Det här exemplet förutsätter att mappen användare finns på enhet C och att den har delats.
> Ändra vid behov om du har delat en annan enhet.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Kör följande kod i Linux-behållaren.

```
/ # ls
```

Du bör se en kataloglista från mappen användare och offentliga. Om inga filer visas och mappen /c/Users/Public inte är tom, är volym mappning inte korrekt konfigurerat.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Ändra till katalogen destinationsindikeringen för att visa innehållet i den `/c/Users/Public` directory:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> När du arbetar med virtuella Linux-datorer, är behållare filsystemet skiftlägeskänsligt.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Build: ”PrepareForBuild” aktiviteten misslyckades oväntat

Microsoft.DotNet.Docker.CommandLine.ClientException: Ett fel uppstod vid försök att ansluta.

Kontrollera att standard Docker-värden körs. Öppna en kommandotolk och kör:

```
docker info
```

Om ett fel returneras, försöker starta den **Docker för Windows** skrivbordsapp. Om skrivbordsappen körs sedan **Moby** ska visas i meddelandefältet. Högerklicka på **Moby** och öppna **inställningar**. Klicka på **återställa**, och starta sedan om Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>En feldialogruta uppstår vid försök att lägga till stöd för Docker eller felsöka ett ASP.NET Core program i en behållare för (F5)

När du avinstallerar och installerar tillägg, kan hanteras utökningsbarhet Framework (MEF)-cachen i Visual Studio skadas. När detta inträffar kan det orsaka olika felmeddelanden när du lägga till stöd för Docker och/eller försök att köra eller felsöka (F5) ASP.NET Core programmet. Som en tillfällig lösning kan använda följande steg att ta bort och återskapa MEF-cachen.

1. Stäng alla instanser av Visual Studio.
1. Öppna % USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Ta bort följande mappar:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Öppna Visual Studio.
1. Försök scenariot igen.
