---
title: Uppdatera Media Services efter återställning av lagringsåtkomstnycklar | Microsoft Docs
description: Den här artikeln ger vägledning om hur du uppdaterar Media Services efter återställning av lagringsåtkomstnycklar.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: e8d8067fcf30b16dd3dbc7f6cf50129d837aa3a5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306864"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Uppdatera Media Services efter återställning av lagringsåtkomstnycklar

När du skapar ett nytt konto i Azure Media Services (AMS) uppmanas du även välja ett Azure Storage-konto som används för att lagra ditt medieinnehåll. Du kan lägga till fler än ett storage-konto till ditt Media Services-konto. Den här artikeln visar hur du rotera lagringsnycklar. Den visar också hur du lägger till storage-konton till ett media-konto. 

Om du vill utföra de åtgärder som beskrivs i den här artikeln bör du använda [Azure Resource Manager API: er](https://docs.microsoft.com/rest/api/media/mediaservice) och [Powershell](https://docs.microsoft.com/powershell/module/azurerm.media).  Mer information finns i [så här hanterar du Azure-resurser med PowerShell och Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md)

## <a name="overview"></a>Översikt

När ett nytt lagringskonto har skapats, genererar Azure två 512-bitars åtkomstnycklar för lagring, som används för att autentisera åtkomsten till ditt lagringskonto. För att skydda din lagringsanslutningar, rekommenderar vi att du återskapa och rotera din lagringsåtkomstnyckel med jämna mellanrum. Två åtkomstnycklar (primära och sekundära) tillhandahålls för att kunna gör att du kan upprätthålla anslutningar till det lagringskonto som den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln. Den här proceduren kallas även ”löpande åtkomstnycklar”.

Media Services är beroende av en lagringsnyckeln som angetts för den. Mer specifikt beror positionerare som används för att strömma eller hämta dina tillgångar på den angivna lagringsåtkomstnyckeln. När ett AMS-konto skapas, det tar ett beroende på åtkomstnyckeln för primär lagring som standard men du kan uppdatera lagringsnyckeln som AMS har som en användare. Du måste se till att låta medietjänster vet vilken nyckel som ska användas genom att följa stegen som beskrivs i den här artikeln.  

>[!NOTE]
> Om du har flera lagringskonton, utför den här proceduren med varje lagringskonto: Den ordning i vilken du rotera lagringsnycklar löses inte. Du kan rotera den sekundära nyckeln först och sedan på den primära nyckeln eller tvärtom.
>
> Se till att testa dem på ett konto i Förproduktion innan du kör stegen som beskrivs i den här artikeln på ett Produktionskonto.
>

## <a name="steps-to-rotate-storage-keys"></a>Steg för att rotera lagringsnycklar 
 
 1. Ändra lagringskontots primära åtkomstnyckel via powershell-cmdleten eller [Azure](https://portal.azure.com/) portal.
 2. Anropa synkronisering AzureRmMediaServiceStorageKeys cmdleten med lämpliga parametrar för att tvinga media-konto för att hämta lagringskontonycklar
 
    I följande exempel visas hur du synkroniserar nycklar till lagringskonton.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Vänta ungefär en timme. Kontrollera de strömmande scenarierna fungerar.
 4. Ändra lagringskontots sekundära åtkomstnyckel via powershell-cmdlet eller Azure-portalen.
 5. Anropa Sync-AzureRmMediaServiceStorageKeys powershell med lämpliga parametrar för att tvinga media-konto för att hämta nya storage-kontonycklar. 
 6. Vänta ungefär en timme. Kontrollera de strömmande scenarierna fungerar.
 
### <a name="a-powershell-cmdlet-example"></a>Ett powershell-cmdlet-exempel 

I följande exempel visar hur du hämtar storage-konto och synkronisera den med AMS-konto.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Steg för att lägga till lagringskonton på ditt AMS-konto

I följande artikel visar hur du lägger till storage-konton till AMS-kontot: [koppla flera lagringskonton till ett Media Services-konto](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Bekräftelser
Vi hoppas att du är medveten om följande personer som har bidragit till att skapa det här dokumentet: Cenk Dingiloglu, Milano Gada Seva Titov.
