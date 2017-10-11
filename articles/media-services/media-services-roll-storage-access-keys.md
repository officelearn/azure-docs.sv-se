---
title: "Uppdatera Media Services när du återställer åtkomstnycklar för lagring | Microsoft Docs"
description: "Den här artikeln får du vägledning om hur du uppdaterar Media Services efter rullande åtkomstnycklar för lagring."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: 304e72e0d2d4a7e95df513e6d5481def9eae3f68
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Uppdatera Media Services när du återställer åtkomstnycklar för lagring

Du uppmanas också att välja ett Azure Storage-konto som används för att lagra media innehållet när du skapar ett nytt konto i Azure Media Services (AMS). Du kan lägga till flera lagringskonton Media Services-kontot. Det här avsnittet visar hur du rotera lagringsnycklar. Den visar även hur du lägger till storage-konton till ett konto för media. 

Om du vill utföra de åtgärder som beskrivs i det här avsnittet bör du använda [ARM-API: er](https://docs.microsoft.com/rest/api/media/mediaservice) och [Powershell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Mer information finns i [hantera Azure-resurser med PowerShell och Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Översikt

När ett nytt lagringskonto har skapats, genererar Azure två 512-bitars åtkomstnycklar för lagring, som används för att autentisera åtkomst till ditt lagringskonto. För att skydda din lagringsanslutningar, rekommenderas att återskapa och rotera din lagringsåtkomstnyckel med jämna mellanrum. Två åtkomstnycklar (primära och sekundära) har angetts för att aktivera att upprätthålla anslutningar till lagringskontot som den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln. Den här proceduren är en förkortning av ”rullande snabbtangenter”.

Media Services är beroende av en nyckel för säkerhetslagring angivna. Mer specifikt beror positionerare som används för att strömma eller hämta dina tillgångar på den angivna lagringsåtkomstnyckel. När en AMS-kontot skapas det tar ett beroende på den primära lagringsåtkomstnyckel som standard men som en användare kan du uppdatera lagringsnyckeln som AMS har. Du måste se till att låta Media Services vet vilken nyckel ska användas genom att följa stegen som beskrivs i det här avsnittet.  

>[!NOTE]
> Om du har flera lagringskonton, utför den här proceduren med varje lagringskonto: Den ordning i vilken du roterar lagringsnycklar är inte fast. Du kan rotera den sekundära nyckeln först och den primära nyckeln eller tvärtom.
>
> Se till att testa dem på ett konto i Förproduktion innan du kör stegen som beskrivs i det här avsnittet på ett Produktionskonto.
>

## <a name="steps-to-rotate-storage-keys"></a>Steg för att rotera lagringsnycklar 
 
 1. Ändra lagringskontots primära åtkomstnyckel via powershell-cmdleten eller [Azure](https://portal.azure.com/) portal.
 2. Anropa synkronisering AzureRmMediaServiceStorageKeys cmdleten med lämpliga parametrar för att tvinga media-konto för att hämta nycklar för lagringskonto
 
    I följande exempel visas hur du synkroniserar nycklar till storage-konton.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Vänta en timme. Kontrollera strömmande scenarier fungerar.
 4. Ändra lagringskontots sekundära åtkomstnyckel via powershell-cmdlet eller Azure-portalen.
 5. Anropa synkronisering AzureRmMediaServiceStorageKeys powershell med lämpliga parametrar för att tvinga media-konto för att hämta nya lagringskontonycklarna genereras. 
 6. Vänta en timme. Kontrollera strömmande scenarier fungerar.
 
### <a name="a-powershell-cmdlet-example"></a>Ett powershell-cmdlet-exempel 

Exemplet nedan visar hur du hämtar storage-konto och synkroniseras med AMS-kontot.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Steg för att lägga till storage-konton i AMS-kontot

Följande avsnitt beskriver hur du lägger till storage-konton AMS-kontot: [koppla flera lagringskonton till ett Media Services-konto](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Bekräftelser
Vi vill bekräfta följande personer som har bidragit till att skapa det här dokumentet: Cenk Dingiloglu, Milano Gada Seva Titov.
