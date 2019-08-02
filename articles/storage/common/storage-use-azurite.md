---
title: Använd Azurite-emulatorn med öppen källkod för utveckling och testning av Blob Storage (för hands version)
description: Azurite-emulatorn med öppen källkod (för hands version) tillhandahåller en kostnads fri lokal miljö för att testa dina Azure Blob Storage-program.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/12/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: ebecd6cf9af5395e4da2b395ca9b2ff974a75409
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721702"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>Använd Azurite-emulatorn med öppen källkod för utveckling och testning av Blob Storage (för hands version)

Azurite version 3-emulator med öppen källkod (för hands version) tillhandahåller en kostnads fri lokal miljö för att testa dina Azure Blob Storage-program. När du är nöjd med hur ditt program fungerar lokalt växlar du till att använda ett Azure Storage konto i molnet. Emulatorn ger plattforms oberoende stöd för Windows, Linux och MacOS. Azurite v3 stöder API: er som implementeras av Azure-Blob Service.

Azurite är den framtida Storage mula-plattformen. Azurite ersätter Azure Storage- [emulatorn](storage-use-emulator.md). Azurite kommer fortfarande att uppdateras för att stödja de senaste versionerna av Azure Storage-API: er.

Det finns flera olika sätt att installera och köra Azurite på det lokala systemet:

  1. [Installera och kör kod tillägget för Visual Studio-Azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Installera och kör Azurite med NPM](#install-and-run-azurite-by-using-npm)
  1. [Installera och köra Azurite Docker-avbildningen](#install-and-run-the-azurite-docker-image)
  1. [Klona, skapa och köra Azurite från GitHub-lagringsplatsen](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Installera och kör kod tillägget för Visual Studio-Azurite

I Visual Studio Code väljer du rutan **tillägg** och söker efter *Azurite* i tilläggen **: Marketplace**.

![Visual Studio Code Extensions Marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

Du kan också navigera till [vs Code Extension marknad](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) i din webbläsare. Välj knappen **Installera** för att öppna Visual Studio Code och gå direkt till Azurite-tilläggs sidan.

Du kan snabbt starta eller stänga Azurite genom att klicka på **Azurite BLOB service** i vs Code-statusfältet eller genom att utfärda följande kommandon i vs Code-kommando paletten. Öppna kommando paletten genom att trycka på **F1** i vs Code.

Tillägget har stöd för följande Visual Studio Code-kommandon:

   * **Azurite: Starta** alla Azurite-tjänster
   * **Azurite: Stäng** alla Azurite-tjänster
   * **Azurite: Rensa** alla Azurite Services persistency-data
   * **Azurite: Start** -BLOB-starta blob-tjänst
   * **Azurite: Avsluta** BLOB-Stäng BLOB service
   * **Azurite: Rensa** Blob-tjänsten rensa BLOB

Om du vill konfigurera Azurite i Visual Studio Code väljer du fönstret tillägg och högerklickar på **Azurite**. Välj **Konfigurera tilläggs inställningar**.

![Azurite konfigurera tilläggs inställningar](media/storage-use-azurite/azurite-configure-extension-settings.png)

Följande inställningar stöds:

   * **Azurite: BLOB-** värd – den BLOB service lyssnar slut punkten. Standardvärdet är 127.0.0.1.
   * **Azurite: BLOB-** port – den BLOB service lyssnings porten. Standard porten är 10000.
   * **Azurite: Felsök** – utdata från fel söknings loggen till Azurite-kanalen. Standardvärdet är **FALSKT**.
   * **Azurite: Plats** – sökvägen till arbets ytans plats. Standardvärdet är Visual Studio Code-arbetsmappen.
   * **Azurite: Tyst** -tyst läge inaktiverar åtkomst loggen. Standardvärdet är **FALSKT**.

## <a name="install-and-run-azurite-by-using-npm"></a>Installera och kör Azurite med NPM

Den här installations metoden kräver att du har [Node. js version 8,0 eller senare](https://nodejs.org) installerad. **NPM** är paket hanterings verktyget som ingår i varje Node. js-installation. När du har installerat Node. js kör du följande **NPM** -kommando för att installera Azurite.

```console
npm install -g azurite
```

När du har installerat Azurite kan du läsa [Kör Azurite från en kommando rad](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Installera och köra Azurite Docker-avbildningen

Använd [DockerHub](https://hub.docker.com/) för att hämta den [senaste Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) -avbildningen med hjälp av följande kommando:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Kör Azurite Docker**-avbildningen:

Följande kommando kör Azurite Docker-avbildningen. `-p 10000:10000` Parametern omdirigerar begär Anden från värd datorns port 10000 till Docker-instansen.

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**Ange arbets ytans plats**:

I följande exempel `-v c:/azurite:/data` anger `c:/azurite` parametern som Azurite-sparade data platser.

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Ange alla Azurite-parametrar**:

Det här exemplet visar hur du ställer in alla kommando rads parametrar. Alla parametrar nedan ska placeras på en enda kommando rad.

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

Se [kommando rads alternativ](#command-line-options) för mer information om hur du konfigurerar Azurite vid start.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Klona, skapa och köra Azurite från GitHub-lagringsplatsen

Den här installations metoden kräver att du har [git](https://git-scm.com/) installerat. Klona [GitHub](https://github.com/azure/azurite) -lagringsplatsen för Azurite-projektet med hjälp av följande konsol kommando.

```console
git clone https://github.com/Azure/Azurite.git
```

När du har klonat käll koden kör du följande kommandon från roten av den klonade lagrings platsen för att bygga och installera Azurite.

```console
npm install
npm run build
npm install -g
```

När du har installerat och skapat Azurite kan du läsa [Kör Azurite från en kommando rad](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Köra Azurite från en kommando rad

> [!NOTE]
> Azurite kan inte köras från kommando raden om du bara har installerat Visual Studio Code-tillägget. Använd i stället VS Code-kommando paletten. Mer information finns i [Installera och köra Azurite Visual Studio Code Extension](#install-and-run-the-azurite-visual-studio-code-extension).

Kom igång direkt med kommando raden genom att skapa en katalog med namnet **c:\azurite**och sedan starta Azurite genom att utfärda följande kommando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Det här kommandot instruerar Azurite att lagra alla data i en viss katalog, **c:\azurite**. Om alternativet **--location** utelämnas, används den aktuella arbets katalogen.

## <a name="command-line-options"></a>Kommando rads alternativ

Det här avsnittet innehåller information om kommando rads växlar som är tillgängliga när du startar Azurite. Alla kommando rads växlar är valfria.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

Växeln **-l** är en genväg för **--location**, **-s** är en genväg för **--Silent**och **-d** är en genväg för-- **Debug**.

### <a name="listening-host"></a>Lyssna värd

**Valfritt** Som standard lyssnar Azurite till 127.0.0.1 som den lokala servern. Använd växeln **--blobHost** för att ange adressen till dina krav.

Acceptera endast begär Anden på den lokala datorn:

```console
azurite --blobHost 127.0.0.1
```

Tillåt fjärrbegäranden:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Att tillåta fjärrbegäranden kan göra systemet sårbart för externa attacker.

### <a name="listening-port-configuration"></a>Konfiguration av lyssnings port

**Valfritt** Som standard lyssnar Azurite efter Blob Service på port 10000. Använd växeln **--blobPort** för att ange den lyssnings port som du behöver.

> [!NOTE]
> När du har använt en anpassad port måste du uppdatera anslutnings strängen eller motsvarande konfiguration i Azure Storage verktyg eller SDK: er.

Anpassa Blob Service lyssnings port:

```console
azurite --blobPort 8888
```

Låt systemet automatiskt välja en tillgänglig port:

```console
azurite --blobPort 0
```

Porten som används visas vid start av Azurite.

### <a name="workspace-path"></a>Sökväg till arbets ytan

**Valfritt** Azurite lagrar data till den lokala disken under körningen. Använd växeln **--location** för att ange en sökväg som arbets ytans plats. Som standard används den aktuella process arbets katalogen.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Åtkomst logg

**Valfritt** Som standard visas åtkomst loggen i konsol fönstret. Inaktivera visningen av åtkomst loggen med hjälp av **--Silent-** växeln.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Fel söknings logg

**Valfritt** Fel söknings loggen innehåller detaljerad information om varje begäran och stack spårning för undantag. Aktivera fel söknings loggen genom att ange en giltig lokal fil Sök väg till växeln **--Debug** .

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Auktorisering för verktyg och SDK: er

Anslut till Azurite från Azure Storage SDK: er eller verktyg, t. ex. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), genom att använda valfri strategi för autentisering. Autentisering krävs. Azurite stöder auktorisering med delad nyckel och signaturer för delad åtkomst (SAS). Azurite stöder även anonym åtkomst till offentliga behållare.

### <a name="well-known-storage-account-and-key"></a>Välkänt lagrings konto och nyckel

Du kan använda följande konto namn och nyckel med Azurite. Detta är samma välkända konto och nyckel som används av den äldre Azure Storage-emulatorn.

* Konto namn:`devstoreaccount1`
* Konto nyckel:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Förutom SharedKey-autentisering stöder Azurite konto-och tjänstens SAS-autentisering. Anonym åtkomst är också tillgängligt när en behållare är inställd att tillåta offentlig åtkomst.

### <a name="connection-string"></a>Anslutningssträng

Det enklaste sättet att ansluta till Azurite från ditt program är att konfigurera en anslutnings sträng i programmets konfigurations fil som refererar till genvägen *UseDevelopmentStorage = True*. Här är ett exempel på en anslutnings sträng i en *app. config* -fil:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Mer information finns i [Konfigurera anslutnings strängar för Azure Storage](storage-configure-connection-string.md).

### <a name="storage-explorer"></a>Storage Explorer

I Azure Storage Explorer ansluter du till Azurite genom att klicka på ikonen **Lägg till konto** och väljer sedan Anslut **till en lokal emulator** och klickar på **Anslut**.

## <a name="differences-between-azurite-and-azure-storage"></a>Skillnader mellan Azurite och Azure Storage

Det finns funktions skillnader mellan en lokal instans av Azurite och ett Azure Storage konto i molnet.

### <a name="endpoint-and-connection-url"></a>Slut punkt och anslutnings-URL

Tjänstens slut punkter för Azurite skiljer sig från slut punkterna för ett Azure Storage konto. Den lokala datorn utför inte domän namns matchning, vilket kräver att Azurite-slutpunkter är lokala adresser.

När du adresserar en resurs i ett Azure Storage konto är konto namnet en del av URI-värdnamnet. Resursen som ska adresseras är en del av URI-sökvägen:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Följande URI är en giltig adress för en BLOB i ett Azure Storage-konto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Eftersom den lokala datorn inte har domän namns matchning, är konto namnet en del av URI-sökvägen i stället för värd namnet. Använd följande URI-format för en resurs i Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Följande adress kan användas för att få åtkomst till en BLOB i Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skalning och prestanda

Azurite är inte en skalbar lagrings tjänst och har inte stöd för ett stort antal samtidiga klienter. Det finns ingen garanti för prestanda. Azurite är avsett för utvecklings-och testnings ändamål.

### <a name="error-handling"></a>Felhantering

Azurite justeras med logik för Azure Storage fel hantering, men det finns skillnader. Fel meddelanden kan till exempel vara olika, medan fel status koderna justeras.

### <a name="ra-grs"></a>RA-GRS

Azurite stöder Geo-redundant replikering med Läs åtkomst (RA-GRS). För lagrings resurser går du till den sekundära platsen genom att lägga till **-sekundär** till konto namnet. Följande adress kan till exempel användas för att få åtkomst till en blob med hjälp av den skrivskyddade sekundära Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite är öppen källkod

Bidrag och förslag för Azurite är välkommen. Gå till Azurite [GitHub Project](https://github.com/Azure/Azurite/projects) Page eller [GitHub-problem](https://github.com/Azure/Azurite/issues) för mil stolpar och arbets objekt vi håller på att spåra för kommande funktioner och fel korrigeringar. Detaljerade arbets objekt spåras också i GitHub.

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md) av dokument i den äldre Azure Storage-emulatorn, som ersätts av Azurite.
* [Konfigurera Azure Storage anslutnings strängar](storage-configure-connection-string.md) förklarar hur du sätter samman en giltig Azure Storage-anslutningssträng.
