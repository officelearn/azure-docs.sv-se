---
title: Använda Azure Blob Storage för modellkonvertering
description: Beskriver vanliga steg för att ställa in och använda blob-lagring för modellkonvertering.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681654"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Använda Azure Blob Storage för modellkonvertering

[Modellkonverteringstjänsten](model-conversion.md) kräver åtkomst till Azure blob-lagring så att den kan hämta indata och lagra utdata. I den här artikeln beskrivs hur du gör de vanligaste stegen.

## <a name="prepare-azure-storage-accounts"></a>Förbereda Azure Storage-konton

- Skapa ett lagringskonto (StorageV2)
- Skapa en indatablolobbehållare i lagringskontot (till exempel "arrinput")
- Skapa en utdatablolobbehållare i lagringskontot (till exempel "arroutput")

> [!TIP]
> Stegvisa instruktioner för hur du konfigurerar ditt lagringskonto finns i [Snabbstart: Konvertera en modell för rendering](../../quickstarts/convert-model.md)

Skapandet av lagringskontot och blob-behållarna kan göras med något av följande verktyg:

- [Azure Portal](https://portal.azure.com)
- [az kommandorad](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/)
- SDK (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Se till att Azure Remote Rendering kan komma åt ditt lagringskonto

Azure Remote Rending måste hämta modelldata från ditt lagringskonto och skriva tillbaka data till det.

Du kan bevilja Azure Remote Rendering åtkomst till ditt lagringskonto på följande två sätt:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Ansluta ditt Azure Storage-konto till ditt Azure Remote Rendering-konto

Följ stegen i avsnittet [Skapa ett konto.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Hämta SAS för lagringsbehållarna

Sas (Stored Access Signatures) används för att bevilja läsbehörighet för indata och skrivåtkomst för utdata. Vi rekommenderar att du skapar nya URI:er varje gång en modell konverteras. Eftersom URI upphör att gälla efter en viss tid kan det riskera att bryta programmet oväntat.

Information om SAS finns i [SAS dokumentation](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

En SAS URI kan genereras med hjälp av en av:

- az PowerShell-modul
  - se [exemplet powershell-skript](../../samples/powershell-example-scripts.md)
- [az kommandorad](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/)
  - högerklicka på behållaren "Få delad åtkomst signatur" (läsa, lista tillgång för indatabehållare, skrivåtkomst för utdatabehållare)
- SDK (C#, Python ... )

Ett exempel på hur du använder signaturer för delad åtkomst i tillgångskonvertering visas i Conversion.ps1 i [Powershell-exempelskripten](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Ladda upp en indatamodell

Om du vill börja konvertera en modell måste du ladda upp den med något av följande alternativ:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) – ett praktiskt användargränssnitt för att ladda upp/ladda ned/hantera filer på azure blob storage
- [Kommandoraden Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - se [PowerShell-exempelskripten](../../samples/powershell-example-scripts.md)
- [Använda en lagring SDK (Python, C # ... )](https://docs.microsoft.com/azure/storage/)
- [Använda AZURE Storage REST API:er](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Ett exempel på hur du laddar upp data för konvertering finns i Conversion.ps1 i [Powershell Example Scripts](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Skaffa en SAS-URI för den konverterade modellen

Det här steget påminner om att [hämta SAS för lagringsbehållarna](#retrieve-sas-for-the-storage-containers). Men den här gången måste du hämta en SAS URI för modellfilen, som skrevs till utdatabehållaren.

Om du till exempel vill hämta en SAS-URI via [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)högerklickar du på modellfilen och väljer "Hämta signature för delad åtkomst".

En SAS -signatur (Shared Access Signature) som ska läsas in om du inte har anslutit ditt lagringskonto till ditt Azure Remote Rendering-konto. Du kan läsa om hur du ansluter ditt konto i [Skapa ett konto](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera modellkonverteringen](configure-model-conversion.md)
- [REST-API för modellkonvertering](conversion-rest-api.md)
