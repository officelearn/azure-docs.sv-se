---
title: Skapa ett importjobb för Azure Import/Export | Microsoft Docs
description: Lär dig hur du skapar en import för tjänsten Microsoft Azure Import/Export.
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: ''
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: a80d2169f346238f997c727f0e9d82666897b608
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Skapa ett importjobb för tjänsten Azure Import/Export

Skapa ett importjobb för tjänsten Microsoft Azure Import/Export med hjälp av REST API omfattar följande steg:

-   Förbereda enheter med verktyget Azure Import/Export.

-   Hämta den plats som ska levereras på enheten.

-   Skapa importjobbet.

-   Leverera enheter till Microsoft via en stöds operatör-tjänst.

-   Uppdaterar importjobbet med leverans information.

 Se [med hjälp av tjänsten Microsoft Azure Import/Export för att överföra Data till Blob Storage](storage-import-export-service.md) för en översikt över Import/Export-tjänsten och en genomgång som visar hur du använder den [Azure-portalen](https://portal.azure.com/) att skapa och hantera importera och exportera jobben.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Förbereda enheter med verktyget Azure Import/Export

Stegen för att förbereda enheter för importen är samma oavsett om du skapar jobvia portalen eller via REST API.

Nedan finns en kort översikt över förberedelse av enheten. Referera till den [Azure Import ExportTool referens](storage-import-export-tool-how-to-v1.md) fullständiga instruktioner. Du kan ladda ned verktyget Azure Import/Export [här](http://go.microsoft.com/fwlink/?LinkID=301900).

Förbereda enheten omfattar:

-   Identifiera data som ska importeras.

-   Identifiera mål-blobbar i Windows Azure Storage.

-   Med verktyget Azure Import/Export för att kopiera data till en eller flera hårddiskar.

 Verktyget Azure Import/Export kommer även att skapa en manifestfil för var och en av enheterna som den är redo. En manifestfil innehåller:

-   En uppräkning av alla filer som är avsedd för överföringen och mappningar från dessa filer till BLOB.

-   Kontrollsummor för segmenten i varje fil.

-   Information om metadata och egenskaper som ska associeras med varje blob.

-   En lista över vad som ska göras om en blob som överförs har samma namn som en befintlig blob-behållare. Möjliga alternativ är: a) över blob med filen, b) skydda befintlig blob och hoppa över överföring av filen, c) lägga till ett suffix till namnet så att den inte står i konflikt med andra filer.

## <a name="obtaining-your-shipping-location"></a>Hämta din leverans plats

Innan du skapar ett importjobb, måste du skaffa ett leverans platsnamn och adress genom att anropa den [listan platser](/rest/api/storageimportexport/listlocations) igen. `List Locations` Returnerar en lista över platser och deras e-postadresser. Du kan välja en plats från listan över returnerade och leverera din hårddiskar till adressen. Du kan också använda den `Get Location` åtgärden att direkt hämta leveransadress för en viss plats.

 Följ stegen nedan för att hämta leveransplatsen för:

-   Identifiera namnet på platsen för ditt lagringskonto. Det här värdet kan hittas den **plats** på storage-konto **instrumentpanelen** i Azure portal eller efterfrågade för med hjälp av service management API-åtgärd [hämta Lagringskontoegenskaperna](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Hämta den plats som är tillgängliga för att bearbeta det här lagringskontot genom att anropa den `Get Location` igen.

-   Om den `AlternateLocations` -egenskapen för platsen innehåller själva platsen och sedan går bra att använda den här platsen. Annars kan anropa den `Get Location` igen med en av de alternativa platserna. Den ursprungliga platsen kan tillfälligt stängt för underhåll.

## <a name="creating-the-import-job"></a>Skapa importjobbet
Om du vill skapa importjobbet anropa den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) igen. Du måste ange följande information:

-   Ett namn för jobbet.

-   Namnet på lagringskontot.

-   Leverans platsnamnet, hämtas från föregående steg.

-   En jobbtyp (importera).

-   Returadressen där enheterna som ska skickas när importjobbet har slutförts.

-   Lista över enheter i jobbet. För varje enhet, måste du inkludera följande information som erhölls vid förberedelsen av enheten:

    -   Enhetens Id

    -   BitLocker-nyckel

    -   Den relativa sökvägen manifestfilen på hårddisken

    -   Base16 kodad manifestfilen MD5-hash

## <a name="shipping-your-drives"></a>Leverera dina enheter
Du måste skicka dina enheter till den adress som du fick från föregående steg och du måste ange tjänsten Import/Export med spårning antal paketet.

> [!NOTE]
>  Du måste leverera enheter via en stöds operatör-tjänst som tillhandahåller ett spårningsnummer för paketet.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Uppdaterar importjobbet med din leveransinformation
När du har Spårningsnumret till din kan anropa den [uppdatera jobbegenskaper](/api/storageimportexport/jobs#Jobs_Update) uppdateringen leverans operatör namn, Spårningsnumret för projektet och kontonummer operatör för returnerade leverans. Alternativt kan du ange antalet enheter och den leverans.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av tjänsten Import/Export REST API](storage-import-export-using-the-rest-api.md)
