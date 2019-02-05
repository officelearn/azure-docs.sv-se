---
title: Skapa en export-jobbet för Azure Import/Export | Microsoft Docs
description: Lär dig hur du skapar ett exportjobb för Microsoft Azure Import/Export-tjänsten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 33234c03a3e691a95e61f825a0351cf481431294
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731402"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Skapa ett exportjobb för tjänsten Azure Import/Export
Skapa ett exportjobb för Microsoft Azure Import/Export-tjänsten med hjälp av REST-API omfattar följande steg:

-   Välja blobbar som ska exporteras.

-   Hämta en leveransplats.

-   Skapa export-jobbet.

-   Leverera dina tom enheter till Microsoft via en stöds operatör-tjänst.

-   Det export-jobbet uppdateras med paketinformationen.

-   Ta emot enheterna från Microsoft.

 Se [med hjälp av tjänsten Windows Azure Import/Export för att överföra Data till Blob Storage](storage-import-export-service.md) för en översikt över Import/Export-tjänsten och en genomgång som visar hur du använder den [Azure-portalen](https://portal.azure.com/) att skapa och Hantera importera och exportera jobb.

## <a name="selecting-blobs-to-export"></a>Välja blobbar som ska exporteras
 Om du vill skapa ett exportjobb, behöver du ange en lista över blobar som du vill exportera från ditt lagringskonto. Det finns ett par olika sätt att välja blobbar exporteras:

-   Du kan använda en relativ blobbsökväg för att välja en enda blob och alla dess ögonblicksbilder.

-   Du kan använda en relativ blobbsökväg för att välja en enda blob, exklusive dess ögonblicksbilder.

-   Du kan använda en relativ blob och sökvägen till en ögonblicksbild-tid för att välja en enskild ögonblicksbild.

-   Du kan använda ett blob-prefix för att välja alla blobar och ögonblicksbilder med det angivna prefixet.

-   Du kan exportera alla blobar och ögonblicksbilder i lagringskontot.

 Läs mer om hur du anger BLOB-och exportera den [placera jobbet](/rest/api/storageimportexport/jobs) igen.

## <a name="obtaining-your-shipping-location"></a>Hämta din plats för leverans
Innan du skapar ett exportjobb, måste du skaffa ett endash platsnamn och adress genom att anropa den [få plats](https://portal.azure.com) eller [List Locations](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) igen. `List Locations` Returnerar en lista över platser och deras e-postadresser. Du kan välja en plats från den returnerade listan och skicka dina hårddiskar till adressen. Du kan också använda den `Get Location` åtgärden att hämta leveransadressen för en specifik plats direkt.

Följ stegen nedan för att hämta leveranser plats:

-   Identifiera namnet på platsen för ditt lagringskonto. Det här värdet finns under den **plats** på lagringskontots **instrumentpanelen** i Azure portal eller efterfrågade för med hjälp av service management API-åtgärden [hämta Storage-konto Egenskaper för](/rest/api/storagerp/storageaccounts).

-   Hämta den plats som är tillgängliga för att bearbeta det här lagringskontot genom att anropa den `Get Location` igen.

-   Om den `AlternateLocations` egenskapen för platsen innehåller själva platsen, så är det bra att använda den här platsen. Annars kan anropa den `Get Location` utföra åtgärden igen med en av de alternativa platserna. Den ursprungliga platsen kan vara tillfälligt stängd för underhåll.

## <a name="creating-the-export-job"></a>Skapa export-jobbet
 Skapa export-jobbet genom att anropa den [placera jobbet](/rest/api/storageimportexport/jobs) igen. Du måste ange följande information:

-   Ett namn för jobbet.

-   Namnet på lagringskontot.

-   Endash platsnamnet, hämtades i föregående steg.

-   En jobbtyp (Export).

-   Returadressen där enheterna som ska skickas när export-jobbet har slutförts.

-   Lista över BLOB-objekt (eller blob-prefix) som ska exporteras.

## <a name="shipping-your-drives"></a>Levererade enheter
 Använd sedan Azure Import/Export-verktyget för att avgöra hur många enheter som du behöver skicka, baserat på de blobar som du har valt för att exporteras och diskens storlek. Se den [referensguiden för Azure Import/Export-verktyget](storage-import-export-tool-how-to-v1.md) mer information.

 Paketera enheter i ett enda paket och skicka dem till den adressen du hämtade i det tidigare steget. Observera spårningsnummer för ditt paket för nästa steg.

> [!NOTE]
>  Du måste skicka dina enheter via en stöds operatör-tjänst som tillhandahåller en spårningsnummer för ditt paket.

## <a name="updating-the-export-job-with-your-package-information"></a>Export-jobbet uppdateras med din Paketinformation
 När du har din spårningsnummer kan anropa den [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs) åtgärden har uppdaterat namnet operatör och spårnings-ID för jobbet. Du kan du ange hur många enheter, avsändaradressen och leveransdatum samt.

## <a name="receiving-the-package"></a>Ta emot paketet
 När din export-jobbet har bearbetats, returneras dina enheter till dig med dina krypterade data. Du kan hämta BitLocker-nyckel för var och en av enheterna genom att anropa den [Get Job](/rest/api/storageimportexport/jobs) igen. Sedan kan du låsa upp enheten med nyckeln. Manifestfilen för enheten på varje enhet innehåller listan över filer på enheten, samt den ursprungliga blob-adressen för varje fil.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
