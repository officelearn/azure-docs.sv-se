---
title: Skapa ett importjobb för Azure Import/Export | Microsoft Docs
description: Lär dig hur du skapar en import för Microsoft Azure Import/Export-tjänsten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: c1effeba7e4b0750d5af3a5a82b755704379af68
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021854"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Skapa ett importjobb för tjänsten Azure Import/Export

Skapa ett importjobb för Microsoft Azure Import/Export-tjänsten med hjälp av REST-API omfattar följande steg:

-   Förbereder enheter med verktyget Azure Import/Export.

-   Hämta den plats som ska skicka hårddisken.

-   Skapa importjobbet.

-   Levererade enheter till Microsoft via en stöds operatör-tjänst.

-   Uppdaterar importjobbet med leveransinformationen.

 Se [med Microsoft Azure Import/Export-tjänsten för att överföra Data till Blob Storage](storage-import-export-service.md) för en översikt över Import/Export-tjänsten och en genomgång som visar hur du använder den [Azure-portalen](https://portal.azure.com/) att skapa Hantera importera och exportera jobb.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Förbereda enheter med verktyget Azure Import/Export

Stegen för att förbereda enheter för ett importjobb är samma oavsett om du skapar jobvia portalen eller via REST API.

Nedan visas en kort översikt över förberedelsen av enheten. Referera till den [Azure Import ExportTool referens](storage-import-export-tool-how-to-v1.md) fullständiga anvisningar. Du kan ladda ned verktyget Azure Import/Export [här](http://go.microsoft.com/fwlink/?LinkID=301900).

Förbereda enheten omfattar:

-   Identifiera data som ska importeras.

-   Identifiera mål-blobar i Windows Azure Storage.

-   Med verktyget Azure Import/Export för att kopiera data till en eller flera hårddiskar.

 Azure Import/Export-verktyget genererar även en manifestfil för var och en av enheterna medan den förbereds. En manifestfil innehåller:

-   En uppräkning av alla filer som är avsedd för uppladdning och mappningar från dessa filer till BLOB.

-   Kontrollsummor för segmenten i varje fil.

-   Information om metadata och egenskapers ska associeras med varje blob.

-   En lista över åtgärden som ska vidtas om en blob överförs har samma namn som en befintlig blob i behållaren. Möjliga alternativen är: a) över bloben med filen, (b) Behåll befintlig blob och hoppa över överföra filen, c) lägga till ett suffix till namnet så att det inte står i konflikt med andra filer.

## <a name="obtaining-your-shipping-location"></a>Hämta din plats för leverans

Innan du skapar ett importjobb, måste du skaffa ett endash platsnamn och adress genom att anropa den [List Locations](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) igen. `List Locations` Returnerar en lista över platser och deras e-postadresser. Du kan välja en plats från den returnerade listan och skicka dina hårddiskar till adressen. Du kan också använda den `Get Location` åtgärden att hämta leveransadressen för en specifik plats direkt.

 Följ stegen nedan för att hämta leveranser plats:

-   Identifiera namnet på platsen för ditt lagringskonto. Det här värdet finns under den **plats** på lagringskontots **instrumentpanelen** i Azure portal eller efterfrågade för med hjälp av service management API-åtgärden [hämta Storage-konto Egenskaper för](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Hämta den plats som är tillgängliga för att bearbeta det här lagringskontot genom att anropa den `Get Location` igen.

-   Om den `AlternateLocations` egenskapen för platsen innehåller själva platsen, så är det bra att använda den här platsen. Annars kan anropa den `Get Location` utföra åtgärden igen med en av de alternativa platserna. Den ursprungliga platsen kan vara tillfälligt stängd för underhåll.

## <a name="creating-the-import-job"></a>Skapa importjobbet
Om du vill skapa importjobbet anropa den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) igen. Du måste ange följande information:

-   Ett namn för jobbet.

-   Namnet på lagringskontot.

-   Det endash platsnamnet som hämtas från föregående steg.

-   En jobbtyp (Import).

-   Returadressen där enheterna som ska skickas när importjobbet har slutförts.

-   Lista över enheter i jobbet. För varje enhet, måste du inkludera följande information som erhölls vid förberedelsen för enheten:

    -   Id för enheten

    -   BitLocker-nyckel

    -   Den relativa sökvägen manifestfilen på hårddisken

    -   Base16 kodad manifestfilen MD5-hash

## <a name="shipping-your-drives"></a>Levererade enheter
Du måste skicka dina enheter till den adress som du hämtade i föregående steg och du måste ange Import/Export-tjänsten med spårningsnummer för paketet.

> [!NOTE]
>  Du måste skicka dina enheter via en stöds operatör-tjänst som tillhandahåller en spårningsnummer för ditt paket.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Uppdaterar importjobbet med din fraktinformation
När du har din spårningsnummer kan anropa den [uppdatera jobbegenskaper](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) uppdateringsåtgärd endash operatör namn, spårningsnummer för jobbet och transportföretagets kontonummer för returfrakt. Du kan du ange hur många enheter och den leverans.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
