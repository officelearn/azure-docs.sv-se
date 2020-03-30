---
title: Använd Azurite-emulator för lokal Azure Storage-utveckling
description: Azurite emulator med öppen källkod (förhandsversion) ger en kostnadsfri lokal miljö för testning av dina Azure-lagringsprogram.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029917"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Använd Azurite-emulatorn för lokal Utveckling och testning av Azure Storage (förhandsversion)

Azurite version 3.2 emulator med öppen källkod (förhandsversion) ger en kostnadsfri lokal miljö för testning av dina Azure-blob- och kölagringsprogram. När du är nöjd med hur ditt program fungerar lokalt växlar du till att använda ett Azure Storage-konto i molnet. Emulatorn ger plattformsoberoende stöd på Windows, Linux och MacOS. Azurite v3 stöder API:er som implementeras av Azure Blob-tjänsten.

Azurite är den framtida lagringsemulatorplattformen. Azurite ersätter [Azure Storage Emulator](storage-use-emulator.md). Azurite fortsätter att uppdateras för att stödja de senaste versionerna av Azure Storage API:er.

Det finns flera olika sätt att installera och köra Azurite på ditt lokala system:

  1. [Installera och kör azurite-kodtillägget för Visual Studio](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Installera och kör Azurite med hjälp av NPM](#install-and-run-azurite-by-using-npm)
  1. [Installera och kör avbildningen Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Klona, bygga och köra Azurite från GitHub-databasen](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Installera och kör azurite-kodtillägget för Visual Studio

I Visual Studio-kod väljer du fönstret **TILLÄGG** och söker efter *Azurite* i **EXTENSIONS:MARKETPLACE**.

![Marknadsplats för Visual Studio-kodtillägg](media/storage-use-azurite/azurite-vs-code-extension.png)

Du kan också navigera till [VS-kodtilläggsmarknaden](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) i webbläsaren. Välj knappen **Installera** för att öppna Visual Studio-koden och gå direkt till Azurite-tilläggssidan.

Du kan snabbt starta eller stänga Azurite genom att klicka på **[Azurite Blob Service]** eller **[Azurite Queue Service]** i statusfältet VS-kod eller utfärda följande kommandon i kommandopaletten VS-kod. Om du vill öppna kommandopaletten trycker du på **F1** i VS-kod.

Tillägget stöder följande Visual Studio-kodkommandon:

   * **Azurite: Start** - Starta alla Azurite-tjänster
   * **Azurite: Stäng** - Stäng alla Azurite-tjänster
   * **Azurite: Clean** - Återställ alla Azurite-tjänster persistensdata
   * **Azurite: Starta Blob-tjänsten** - Starta blob-tjänsten
   * **Azurite: Stäng Blob Service** - Stäng blob-tjänsten
   * **Azurite: Clean Blob Service** - Clean blob service
   * **Azurite: Starta kötjänst** - Starta kötjänst
   * **Azurite: Stäng kötjänst** - Stäng kötjänst
   * **Azurite: Tjänsten Ren kö** - Rensa kötjänst

Om du vill konfigurera Azurite i Visual Studio-kod markerar du tilläggsfönstret. Välj ikonen **Hantera** (redskap) för **Azurite**. Välj **Konfigurera tilläggsinställningar**.

![Azurite konfigurerar tilläggsinställningar](media/storage-use-azurite/azurite-configure-extension-settings.png)

Följande inställningar stöds:

   * **Azurite: Blob Host** - Slutpunkten för Blob-tjänstens lyssning. Standardinställningen är 127.0.0.1.
   * **Azurite: Blob Port** - Blob-tjänstens lyssnande port. Standardporten är 10000.
   * **Azurite: Debug** - Mata ut felsökningsloggen till Azuritekanalen. Standardvärdet är **falskt**.
   * **Azurite: Plats** - Arbetsytans platssökväg. Standard är arbetsmappen För Visual Studio-kod.
   * **Azurite: Kövärd** - Slutpunkten för kötjänsten lyssning. Standardinställningen är 127.0.0.1.
   * **Azurite: Köport** - Lyssningsporten för kötjänsten. Standardporten är 10001.
   * **Azurite: Tyst** - Tyst läge inaktiverar åtkomstloggen. Standardvärdet är **falskt**.

## <a name="install-and-run-azurite-by-using-npm"></a>Installera och kör Azurite med hjälp av NPM

Den här installationsmetoden kräver att du har [Node.js version 8.0 eller senare](https://nodejs.org) installerad. **npm** är pakethanteringsverktyget som ingår i varje Node.js-installation. När du har installerat Node.js kör du följande **npm-kommando** för att installera Azurite.

```console
npm install -g azurite
```

När du har installerat Azurite, se [Kör Azurite från en kommandorad](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Installera och kör avbildningen Azurite Docker

Använd [DockerHub](https://hub.docker.com/) för att hämta den [senaste Azurite-avbildningen](https://hub.docker.com/_/microsoft-azure-storage-azurite) med följande kommando:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Kör bilden Av Azurite Docker:**

Följande kommando kör bilden azurite Docker. Parametern `-p 10000:10000` omdirigerar begäranden från värddatorns port 10000 till Docker-instansen.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Ange arbetsytans plats:**

I följande exempel `-v c:/azurite:/data` anger parametern *c:/azurite* som azurite-beständig dataplats. Katalogen, *c:/azurite*, måste skapas innan kommandot Docker körs.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Kör bara blob-tjänsten**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Ställ in alla Azurite parametrar:**

Det här exemplet visar hur du ställer in alla kommandoradsparametrar. Alla parametrar nedan ska placeras på en enda kommandorad.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Mer information om hur du konfigurerar Azurite vid uppstart finns i [Kommandoradsalternativ.](#command-line-options)

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klona, bygga och köra Azurite från GitHub-databasen

Den här installationsmetoden kräver att du har [Git](https://git-scm.com/) installerat. Klona [GitHub-databasen](https://github.com/azure/azurite) för Azurite-projektet med hjälp av följande konsolkommando.

```console
git clone https://github.com/Azure/Azurite.git
```

När du har klonat källkoden kör du följande kommandon från roten till den klonade repoen för att bygga och installera Azurite.

```console
npm install
npm run build
npm install -g
```

Efter installation och byggnad Azurite, se [Kör Azurite från en kommandorad](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Kör Azurite från en kommandorad

> [!NOTE]
> Azurite kan inte köras från kommandoraden om du bara har installerat tillägget Visual Studio-kod. Använd i stället kommandopaletten VS-kod. Mer information finns i [Installera och kör tillägget Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Om du vill komma igång omedelbart med kommandoraden skapar du en katalog som heter **c:\azurite**och startar sedan Azurite genom att utfärda följande kommando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Det här kommandot talar om för Azurite att lagra alla data i en viss katalog, **c:\azurite**. Om alternativet **--location** utelämnas används den aktuella arbetskatalogen.

## <a name="command-line-options"></a>Kommandoradsalternativ

I det här avsnittet beskrivs kommandoradsväxlarna som är tillgängliga när Azurite startas. Alla kommandoradsväxlar är valfria.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d** är en genväg för **--debug**, **-l-växeln** är en genväg för **--location**, **-s** är en genväg för **--silent**och **-h** är en genväg för **--help**.

### <a name="blob-listening-host"></a>Blob lyssnar värd

**Valfritt** Som standard kommer Azurite att lyssna på 127.0.0.1 som lokal server. Använd växeln **--blobHost** för att ställa in adressen till dina behov.

Acceptera endast begäranden på den lokala datorn:

```console
azurite --blobHost 127.0.0.1
```

Tillåt fjärrbegäranden:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Om du tillåter fjärrbegäranden kan ditt system bli sårbart för externa attacker.

### <a name="blob-listening-port-configuration"></a>Konfiguration av Blob-lyssningsport

**Valfritt** Som standard lyssnar Azurite efter Blob-tjänsten på port 10000. Använd växeln **--blobPort** för att ange den lyssningsport som du behöver.

> [!NOTE]
> När du har använt en anpassad port måste du uppdatera anslutningssträngen eller motsvarande konfiguration i dina Azure Storage-verktyg eller SDK:er.

Anpassa lyssningsporten för Blob-tjänsten:

```console
azurite --blobPort 8888
```

Låt systemet automatiskt välja en tillgänglig port:

```console
azurite --blobPort 0
```

Porten som används visas under Azurite-start.

### <a name="queue-listening-host"></a>Värd för kölyssning

**Valfritt** Som standard kommer Azurite att lyssna på 127.0.0.1 som lokal server. Använd växeln **--queueHost** för att ange adressen till dina behov.

Acceptera endast begäranden på den lokala datorn:

```console
azurite --queueHost 127.0.0.1
```

Tillåt fjärrbegäranden:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Om du tillåter fjärrbegäranden kan ditt system bli sårbart för externa attacker.

### <a name="queue-listening-port-configuration"></a>Konfiguration av kölyssningsport

**Valfritt** Som standard lyssnar Azurite efter kötjänsten på port 10001. Använd växeln **--queuePort** för att ange den lyssningsport som du behöver.

> [!NOTE]
> När du har använt en anpassad port måste du uppdatera anslutningssträngen eller motsvarande konfiguration i dina Azure Storage-verktyg eller SDK:er.

Anpassa lyssningsporten för kötjänsten:

```console
azurite --queuePort 8888
```

Låt systemet automatiskt välja en tillgänglig port:

```console
azurite --queuePort 0
```

Porten som används visas under Azurite-start.

### <a name="workspace-path"></a>Arbetsytans sökväg

**Valfritt** Azurite lagrar data till den lokala disken under körningen. Använd växeln **--plats** för att ange en sökväg som arbetsyta. Som standard används den aktuella processarbetskatalogen.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Åtkomstlogg

**Valfritt** Som standard visas åtkomstloggen i konsolfönstret. Inaktivera visningen av åtkomstloggen med hjälp av **--silent** switch.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Felsökningslogg

**Valfritt** Felsökningsloggen innehåller detaljerad information om varje begäran och undantag stackspårning. Aktivera felsökningsloggen genom att ange en giltig sökväg till sökvägen **--debug.**

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Löst läge

**Valfritt** Som standard tillämpar Azurite strikt läge för att blockera målhuvuden och parametrar som inte stöds. Inaktivera strikt läge med hjälp av **--lös** switch.

```console
azurite --loose
```

Notera genvägsväxlingen "L":

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Auktorisering för verktyg och SDK:er

Anslut till Azurite från Azure Storage SDK:er eller verktyg, till exempel [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), med hjälp av någon autentiseringsstrategi. Autentisering krävs. Azurite stöder auktorisering med delad nyckel och signaturer för delad åtkomst (SAS). Azurite stöder också anonym åtkomst till offentliga behållare.

### <a name="well-known-storage-account-and-key"></a>Välkänt lagringskonto och nyckel

Du kan använda följande kontonamn och nyckel med Azurite. Detta är samma välkända konto och nyckel som används av den äldre Azure-lagringsemulatorn.

* Kontonamn:`devstoreaccount1`
* Kontonyckel:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Förutom SharedKey-autentisering stöder Azurite SAS-autentisering för konton och tjänster. Anonym åtkomst är också tillgänglig när en behållare är inställd för att ge allmänheten åtkomst.

### <a name="connection-string"></a>Anslutningssträng

Det enklaste sättet att ansluta till Azurite från ditt program är att konfigurera en anslutningssträng i programmets konfigurationsfil som refererar till genvägen *UseDevelopmentStorage=true*. Här är ett exempel på en anslutningssträng i en *app.config-fil:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Mer information finns i [Konfigurera Azure Storage-anslutningssträngar](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Anpassade lagringskonton och nycklar

Azurite stöder anpassade lagringskontonamn `AZURITE_ACCOUNTS` och nycklar genom att `account1:key1[:key2];account2:key1[:key2];...`ange miljövariabeln i följande format: .

Använd till exempel ett anpassat lagringskonto med en nyckel:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Eller använd flera lagringskonton med två nycklar vardera:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite uppdaterar anpassade kontonamn och nycklar från miljövariabeln varje minut som standard. Med den här funktionen kan du rotera kontonyckeln dynamiskt eller lägga till nya lagringskonton utan att starta om Azurite.

> [!NOTE]
> Standardlagringskontot `devstoreaccount1` inaktiveras när du anger anpassade lagringskonton.

> [!NOTE]
> Uppdatera anslutningssträngen i enlighet med detta när du använder anpassade kontonamn och nycklar.

> [!NOTE]
> Använd `export` nyckelordet för att ställa in miljövariabler i en Linux-miljö, använd `set` i Windows.

### <a name="storage-explorer"></a>Storage Explorer

I Azure Storage Explorer ansluter du till Azurite genom att klicka på ikonen **Lägg till konto,** sedan **välj Bifoga till en lokal emulator** och klicka på **Anslut**.

## <a name="differences-between-azurite-and-azure-storage"></a>Skillnader mellan Azurite och Azure Storage

Det finns funktionella skillnader mellan en lokal instans av Azurite och ett Azure Storage-konto i molnet.

### <a name="endpoint-and-connection-url"></a>Url för slutpunkt och anslutning

Tjänstslutpunkterna för Azurite skiljer sig från slutpunkterna för ett Azure Storage-konto. Den lokala datorn gör inte domännamnsmatchning, vilket kräver att Azurite-slutpunkter är lokala adresser.

När du adresserar en resurs i ett Azure Storage-konto är kontonamnet en del av URI-värdnamnet. Resursen som åtgärdas är en del av URI-sökvägen:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Följande URI är en giltig adress för en blob i ett Azure Storage-konto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Eftersom den lokala datorn inte gör domännamnsmatchning är kontonamnet en del av URI-sökvägen i stället för värdnamnet. Använd följande URI-format för en resurs i Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Följande adress kan användas för åtkomst till en blob i Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skalning och prestanda

Azurite är inte en skalbar lagringstjänst och stöder inte ett stort antal samtidiga klienter. Det finns ingen prestandagaranti. Azurite är avsett för utvecklings- och teständamål.

### <a name="error-handling"></a>Felhantering

Azurite är i linje med Azure Storage felhantering logik, men det finns skillnader. Felmeddelanden kan till exempel vara olika, medan felstatuskoder justeras.

### <a name="ra-grs"></a>RA-GRS

Azurite stöder geo redundant replikering av läsåtkomst (RA-GRS). För lagringsresurser öppnar du den sekundära platsen genom att lägga till **-sekundärt** till kontonamnet. Följande adress kan till exempel användas för att komma åt en blob med den skrivskyddade sekundära i Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite är öppen källkod

Bidrag och förslag på Azurite är välkomna. Gå till Azurite [GitHub-projektsidan](https://github.com/Azure/Azurite/projects) eller [GitHub-problem](https://github.com/Azure/Azurite/issues) för milstolpar och arbetsobjekt som vi spårar för kommande funktioner och buggfixar. Detaljerade arbetsobjekt spåras också i GitHub.

## <a name="next-steps"></a>Nästa steg

* [Använd Azure-lagringsemulatorn för utvecklings- och testdokument](storage-use-emulator.md) den äldre Azure-lagringsemulatorn, som ersätts av Azurite.
* [Konfigurera Azure Storage-anslutningssträngar](storage-configure-connection-string.md) förklarar hur du monterar en giltig Azure STorage-anslutningssträng.
