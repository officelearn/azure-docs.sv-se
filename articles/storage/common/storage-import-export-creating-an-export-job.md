---
title: "Skapa en export jobb för Azure Import/Export | Microsoft Docs"
description: "Lär dig hur du skapar ett exportjobb för tjänsten Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 70f74b685f4d5b902b6f6780f7c1de4d357164b8
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Skapa ett exportjobb för tjänsten Azure Import/Export
Skapa ett exportjobb för tjänsten Microsoft Azure Import/Export med hjälp av REST API omfattar följande steg:

-   Om du väljer att exportera blobbarna.

-   Hämta en leveransplats.

-   Skapa exportjobbet.

-   Leverera tomt enheter till Microsoft via en stöds operatör-tjänst.

-   Uppdaterar exportjobbet med Paketinformation om.

-   Ta emot enheterna tillbaka från Microsoft.

 Se [med hjälp av tjänsten Windows Azure Import/Export för att överföra Data till Blob Storage](storage-import-export-service.md) för en översikt över Import/Export-tjänsten och en genomgång som visar hur du använder den [Azure-portalen](https://portal.azure.com/) att skapa och Hantera importera och exportera jobben.

## <a name="selecting-blobs-to-export"></a>Välja att exportera blobbarna
 Om du vill skapa ett exportjobb, behöver du ange en lista över blobbar som du vill exportera från ditt lagringskonto. Det finns några sätt att välja blobbar som ska exporteras:

-   Du kan använda en relativ blob-sökväg för att välja en enda blob och alla dess ögonblicksbilder.

-   Du kan använda en relativ blob-sökväg för att välja en enda blob undantag av dess ögonblicksbilder.

-   Du kan använda en relativ blob-sökväg och ett ögonblicksbild för att välja en ögonblicksbild.

-   Du kan använda ett blob-prefix för att markera alla blobbar och ögonblicksbilder med det angivna prefixet.

-   Du kan exportera alla blobbar och ögonblicksbilder i storage-konto.

 Läs mer om hur du anger BLOB att exportera den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) igen.

## <a name="obtaining-your-shipping-location"></a>Hämta din leverans plats
Innan du skapar ett exportjobb, måste du skaffa ett leverans platsnamn och adress genom att anropa den [få plats](https://portal.azure.com) eller [listan platser](/rest/api/storageimportexport/listlocations) igen. `List Locations`Returnerar en lista över platser och deras e-postadresser. Du kan välja en plats från listan över returnerade och leverera din hårddiskar till adressen. Du kan också använda den `Get Location` åtgärden att direkt hämta leveransadress för en viss plats.

Följ stegen nedan för att hämta leveransplatsen för:

-   Identifiera namnet på platsen för ditt lagringskonto. Det här värdet kan hittas den **plats** på storage-konto **instrumentpanelen** i Azure portal eller efterfrågade för med hjälp av service management API-åtgärd [hämta Lagringskontoegenskaperna](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Hämta platsen för att utföra det här lagringskontot genom att anropa den `Get Location` igen.

-   Om den `AlternateLocations` -egenskapen för platsen innehåller själva platsen och sedan går bra att använda den här platsen. Annars kan anropa den `Get Location` igen med en av de alternativa platserna. Den ursprungliga platsen kan tillfälligt stängt för underhåll.

## <a name="creating-the-export-job"></a>Skapa exportjobbet
 Om du vill skapa exportjobbet anropa den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) igen. Du måste ange följande information:

-   Ett namn för jobbet.

-   Namnet på lagringskontot.

-   Leverans platsnamnet, hämtades i föregående steg.

-   En jobbtyp (exportera).

-   Returadressen där enheterna som ska skickas när exportjobbet har slutförts.

-   Lista över blobbar (eller blob-prefix) som ska exporteras.

## <a name="shipping-your-drives"></a>Leverera dina enheter
 Använd sedan verktyget Azure Import/Export för att avgöra hur många enheter som du måste skicka, baserat på blobbar som du har valt exporteras och storleken på hårddisken. Finns det [referens för Azure Import/Export verktyg](storage-import-export-tool-how-to-v1.md) mer information.

 Paketera enheter i ett paket och skicka dem till den adress som du fått i tidigare steg. Observera att spåra antalet paketet för nästa steg.

> [!NOTE]
>  Du måste leverera enheter via en stöds operatör-tjänst som tillhandahåller ett spårningsnummer för paketet.

## <a name="updating-the-export-job-with-your-package-information"></a>Uppdaterar exportjobbet med Paketinformation
 När du har Spårningsnumret till din kan anropa den [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs#Jobs_Update) åtgärden Uppdatera namnet på operatör och spårnings-ID för jobbet. Alternativt kan du ange antalet enheter, returadressen och leveransdatum samt.

## <a name="receiving-the-package"></a>Tar emot paketet
 När din exportjobb har bearbetats returneras enheter till dig med krypterade data. Du kan hämta BitLocker-nyckel för var och en av enheterna genom att anropa den [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) igen. Sedan kan du låsa upp enheten med nyckeln. Enheten manifestfilen på varje enhet innehåller listan med filer på enheten som den ursprungliga blob-adressen för varje fil.

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av tjänsten Import/Export REST API](storage-import-export-using-the-rest-api.md)
