---
title: PowerShell-exempelskript
description: Exempel som visar hur du använder klientdelen via PowerShell-skript
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891500"
---
# <a name="example-powershell-scripts"></a>PowerShell-exempelskript

Azure Remote Rendering innehåller följande två REST API:er:

- [REST-API för konvertering](../how-tos/conversion/conversion-rest-api.md)
- [REST-API för session](../how-tos/session-rest-api.md)

[ARR-exempeldatabasen](https://github.com/Azure/azure-remote-rendering) innehåller exempelskript i mappen Skript för interaktion med *TJÄNSTENS* REST-API:er. I den här artikeln beskrivs deras användning.

## <a name="prerequisites"></a>Krav

För att köra exempelskripten behöver du en funktionell installation av [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

1. Installera Azure PowerShell:
    1. Öppna ett PowerShell med administratörsrättigheter
    1. Köra:`Install-Module -Name Az -AllowClobber`

1. Om du får fel om att köra skript kontrollerar du att [körningsprincipen](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) är inställd på rätt sätt:
    1. Öppna ett PowerShell med administratörsrättigheter
    1. Köra:`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Förbereda ett Azure Storage-konto](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Logga in på din prenumeration som innehåller ditt Azure Remote Rendering-konto:
    1. Öppna ett PowerShell
    1. Kör: `Connect-AzAccount` och följ anvisningarna på skärmen.

> [!NOTE]
> Om din organisation har mer än en prenumeration kan du behöva ange argumenten SubscriptionId och Tenant. Hitta information i [Dokumentationen för Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

1. Hämta *mappen Skript* från [Azure Remote Rendering GithHub-databasen](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Konfigurationsfil

Bredvid `.ps1` filerna finns en `arrconfig.json` som du måste fylla i:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Se till att korrekt escape backslashes i LocalAssetDirectoryPath sökvägen\\\\med hjälp av dubbla omvänt snedstreck: " " och använda framåt snedstreck "/" i alla andra banor som inputFolderPath och inputAssetPath.

### <a name="accountsettings"></a>kontoInställningar

För `arrAccountId` `arrAccountKey`och finns i [Skapa ett Azure Remote Rendering-konto](../how-tos/create-an-account.md).
Se `region` [listan över tillgängliga regioner](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Den här strukturen måste fyllas i om du vill köra **RenderingSession.ps1**.

- **vmSize:** Väljer storleken på den virtuella datorn. Välj *standard* eller *premium*. Stäng av renderingssessioner när du inte behöver dem längre.
- **maxLeaseTime:** Den varaktighet som du vill låna den virtuella datorn för. Det kommer att stängas när hyresavtalet löper ut. Hyrestiden kan förlängas senare (se nedan).

### <a name="assetconversionsettings"></a>tillgångKonversionErings

Den här strukturen måste fyllas i om du vill köra **Conversion.ps1**.

Mer information finns i [Förbereda ett Azure Storage-konto](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Skript: RenderingSession.ps1

Det här skriptet används för att skapa, fråga och stoppa renderingssessioner.

> [!IMPORTANT]
> Kontrollera att du har fyllt i *avsnitten accountSettings* och *renderingSessionSettings* i arrconfig.json.

### <a name="create-a-rendering-session"></a>Skapa en återgivningssession

Normal användning med en fullt fylld arrconfig.json:

```PowerShell
.\RenderingSession.ps1
```

Skriptet anropar [REST API:et](../how-tos/session-rest-api.md) för sessionshantering för att skapa en återgivnings-VM med de angivna inställningarna. Vid framgång kommer det att hämta *sessionId*. Sedan kommer det att avsöka sessionsegenskaperna tills sessionen är klar eller ett fel uppstod.

Så här använder du en **alternativ config-fil:**

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Du kan **åsidosätta enskilda inställningar** från konfigurationsfilen:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Om du bara vill **starta en session utan avsökning**kan du använda:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

*SessionId* som skriptet hämtar måste skickas till de flesta andra sessionskommandon.

### <a name="retrieve-session-properties"></a>Hämta sessionsegenskaper

Om du vill hämta en sessions egenskaper kör du:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Använd `-Poll` för att vänta tills sessionen är *klar* eller ett fel uppstod.

### <a name="list-active-sessions"></a>Lista aktiva sessioner

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Stoppa en session

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Ändra sessionsegenskaper

För närvarande stöder vi bara att ändra maxLeaseTime för en session.

> [!NOTE]
> Lånetiden räknas alltid från den tidpunkt då den virtuella sessionen skapades. Så för att förlänga sessionen hyresavtalet med en annan timme, öka *maxLeaseTime* med en timme.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Skript: Conversion.ps1

Det här skriptet används för att konvertera indatamodeller till Azure Remote Rendering-specifika körningsformat.

> [!IMPORTANT]
> Kontrollera att du har fyllt i avsnitten *accountSettings* och *assetConversionSettings* i arrconfig.json.

Skriptet visar de två alternativen för att använda lagringskonton med tjänsten:

- Lagringskonto kopplat till Azure Remote Rendering-konto
- Tillhandahålla åtkomst till lagring via SIGNATURER för delad åtkomst (SAS)

### <a name="linked-storage-account"></a>Länkat lagringskonto

När du har fyllt i arrconfig.json och länkat ett lagringskonto kan du använda följande kommando. Länka ditt lagringskonto beskrivs på [Skapa ett konto](../how-tos/create-an-account.md#link-storage-accounts).

Att använda ett länkat lagringskonto är det bästa sättet att använda konverteringstjänsten eftersom det inte finns något behov av att generera signaturer för delad åtkomst.

```PowerShell
.\Conversion.ps1
```

1. Ladda upp alla filer `assetConversionSettings.modelLocation` som finns i till indatablobbbehållaren under den angivna`inputFolderPath`
1. Anropa [standardkonverterings-REST API](../how-tos/conversion/conversion-rest-api.md) för att starta [modellkonverteringen](../how-tos/conversion/model-conversion.md)
1. Avsök konverteringsstatusen tills konverteringen lyckades eller misslyckades
1. Utdata för den konverterade filplatsen (lagringskonto, utdatabehållare, filsökväg i behållaren)

### <a name="access-to-storage-via-shared-access-signatures"></a>Tillgång till lagring via signaturer för delad åtkomst

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Detta kommer att:

1. Ladda upp den `assetConversionSettings.localAssetDirectoryPath` lokala filen från till indatablobbbehållaren
1. Generera en SAS-URI för indatabehållaren
1. Generera en SAS-URI för utdatabehållaren
1. Anropa [standardkonverterings-REST API](../how-tos/conversion/conversion-rest-api.md) för att starta [modellkonverteringen](../how-tos/conversion/model-conversion.md)
1. Avsök konverteringsstatusen tills konverteringen lyckades eller misslyckades
1. Utdata för den konverterade filplatsen (lagringskonto, utdatabehållare, filsökväg i behållaren)
1. Mata ut en SAS-URI till den konverterade modellen i utdatablobbbehållaren

### <a name="additional-command-line-options"></a>Ytterligare kommandoradsalternativ

Så här använder du en **alternativ config-fil:**

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Om du bara vill **starta modellkonvertering utan avsökning**kan du använda:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Du kan **åsidosätta enskilda inställningar** från konfigurationsfilen med hjälp av följande kommandoradsväxlar:

* **Id:** ConversionId används med GetConversionStatus
* **ArrAccountId:** arrAccountId av accountSettings
* **ArrAccountKey:** åsidosätta för arrAccountKey av accountSettings
* **Region:** åsidosätta för region av kontoInställningar
* **ResourceGroup:** åsidosätta resursGrupp av assetConversionSettings
* **StorageAccountName:** åsidosätta för lagringKontoNamn för tillgångKonversionsettningar
* **BlobInputContainerName:** åsidosätta blobInputContainer av assetConversionSettings
* **LocalAssetDirectoryPath:** åsidosätta localAssetDirectoryPath of assetConversionSettings
* **InputAssetPath:** åsidosätt för inputAssetPath of assetConversionSettings
* **BlobOutputContainerName:** åsidosätta blobOutputContainerName of assetConversionSettings
* **OutputFolderPath:** åsidosätt för utdataMappspath för assetConversionSettings
* **OutputAssetFileName:** åsidosätta för utdataAssetFileName of assetConversionSettings

Du kan till exempel kombinera ett antal av de angivna alternativen så här:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Kör de enskilda konverteringsfaserna

Om du vill köra enskilda steg i processen kan du använda:

Ladda bara upp data från den angivna LocalAssetDirectoryPath

```PowerShell
.\Conversion.ps1 -Upload
```

Starta endast konverteringsprocessen för en modell som redan har överförts till blob-lagring (kör inte Ladda upp, avsök inte konverteringsstatusen) Skriptet returnerar en *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

Och du kan hämta konverteringsstatusen för den här konverteringen med hjälp av:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Använd `-Poll` för att vänta tills konverteringen är klar eller ett fel uppstod.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Återge en modell med Unity](../quickstarts/render-model.md)
- [Snabbstart: Konvertera en modell för rendering](../quickstarts/convert-model.md)
- [Modellkonvertering](../how-tos/conversion/model-conversion.md)
