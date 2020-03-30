---
title: Uppdatera Media Services efter åtkomstnycklar för rullande lagring | Microsoft-dokument
description: I de här artiklarna får du vägledning om hur du uppdaterar Media Services efter att du har rullat åtkomstnycklar för lagring.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 2a0d1c5af572c88dc11bed950b46706f0a2f081f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981965"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Uppdatera Media Services efter återställning av lagringsåtkomstnycklar 

När du skapar ett nytt AMS-konto (Azure Media Services) uppmanas du också att välja ett Azure Storage-konto som används för att lagra ditt medieinnehåll. Du kan lägga till fler än ett lagringskonton i ditt Media Services-konto. Den här artikeln visar hur du roterar lagringsnycklar. Den visar också hur du lägger till lagringskonton i ett mediekonto. 

Om du vill utföra de åtgärder som beskrivs i den här artikeln bör du använda [Azure Resource Manager API:er](/rest/api/media/operations/azure-media-services-rest-api-reference) och [Powershell](https://docs.microsoft.com/powershell/module/az.media).  Mer information finns i [Så här hanterar du Azure-resurser med PowerShell och Resource Manager](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När ett nytt lagringskonto skapas genererar Azure två 512-bitars lagringsåtkomstnycklar som används för att autentisera åtkomst till ditt lagringskonto. För att hålla dina lagringsanslutningar säkrare rekommenderar vi att du regelbundet återskapar och roterar åtkomstnyckeln för lagring. Två åtkomstnycklar (primära och sekundära) tillhandahålls för att du ska kunna underhålla anslutningar till lagringskontot med en åtkomstnyckel medan du återskapar den andra åtkomstnyckeln. Den här proceduren kallas även för "rullande åtkomstnycklar".

Medietjänster är beroende av en lagringsnyckel som tillhandahålls. De positionerare som används för att strömma eller hämta dina tillgångar beror särskilt på den angivna åtkomstnyckeln för lagring. När ett AMS-konto skapas krävs ett beroende av den primära åtkomstnyckeln för lagring som standard, men som användare kan du uppdatera lagringsnyckeln som AMS har. Du måste se till att Media Services vet vilken nyckel som ska användas genom att följa stegen som beskrivs i den här artikeln.  

>[!NOTE]
> Om du har flera lagringskonton utför du den här proceduren med varje lagringskonto. I vilken ordning du roterar lagringsnycklar är inte fast. Du kan rotera den sekundära nyckeln först och sedan primärnyckeln eller vice versa.
>
> Innan du kör steg som beskrivs i den här artikeln på ett produktionskonto ska du testa dem på ett förproduktionskonto.
>

## <a name="steps-to-rotate-storage-keys"></a>Steg för att rotera lagringsnycklar 
 
 1. Ändra primärnyckel för lagringskontot via powershell-cmdlet eller [Azure-portalen.](https://portal.azure.com/)
 2. Ring Sync-AzMediaServiceStorageKeys cmdlet med lämpliga params för att tvinga mediekontot att hämta lagringskontonycklar
 
    I följande exempel visas hur du synkroniserar nycklar till lagringskonton.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Vänta en timme eller så. Kontrollera att direktuppspelningsscenarierna fungerar.
 4. Ändra sekundärnyckel för lagringskonto via powershell-cmdlet eller Azure-portalen.
 5. Ring Sync-AzMediaServiceStorageKeys powershell med lämpliga params för att tvinga mediekontot att hämta nya lagringskontonycklar. 
 6. Vänta en timme eller så. Kontrollera att direktuppspelningsscenarierna fungerar.
 
### <a name="a-powershell-cmdlet-example"></a>Ett powershell cmdlet-exempel 

Följande exempel visar hur du hämtar lagringskontot och synkroniserar det med AMS-kontot.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Steg för att lägga till lagringskonton i ditt AMS-konto

Följande artikel visar hur du lägger till lagringskonton i ditt AMS-konto: [Bifoga flera lagringskonton till ett Media Services-konto](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Tack
Vi vill uppmärksamma följande personer som bidragit till att skapa detta dokument: Cenk Dingiloglu, Milan Gada, Seva Titov.
