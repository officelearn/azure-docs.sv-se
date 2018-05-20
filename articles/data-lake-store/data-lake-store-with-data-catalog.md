---
title: Registrera data från Data Lake Store i Azure Data Catalog | Microsoft Docs
description: Registrera data från Data Lake Store i Azure Data Catalog
services: data-lake-store,data-catalog
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: ea5d658b8f465b3a527033ef5e9d2126732c7029
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registrera data från Data Lake Store i Azure Data Catalog
I den här artikeln får du lära dig hur du integrerar Azure Data Lake Store med Azure Data Catalog att göra data synlig i en organisation genom att integrera med Data Catalog. Mer information om katalogiserar data finns [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Scenarier där du kan använda Data Catalog finns [vanliga scenarier för Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivera din Azure-prenumeration** för Data Lake Store Public Preview. Se [instruktioner](data-lake-store-get-started-portal.md).
* **Azure Data Lake Store-konto**. Följ instruktionerna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](data-lake-store-get-started-portal.md). Låt oss skapa ett Data Lake Store-konto som kallas för den här kursen **datacatalogstore**.

    När du har skapat kontot, ladda upp exempeldata data till den. I den här självstudien Låt oss överför CSV-filer under den **AmbulanceData** mapp i den [Azure Data Lake Git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Du kan använda olika klienter som [Azure Lagringsutforskaren](http://storageexplorer.com/), för att överföra data till en blob-behållare.
* **Azure Data Catalog**. Din organisation måste redan ha en Azure Data Catalog som skapats för din organisation. Endast en katalog är tillåten för varje organisation.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registrera Data Lake Store som källa för Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Gå till `https://azure.microsoft.com/services/data-catalog`, och klicka på **Kom igång**.
2. Logga in på Azure Data Catalog-portalen och på **publicera data**.

    ![Registrera en datakälla](./media/data-lake-store-with-data-catalog/register-data-source.png "registrera en datakälla")
3. Klicka på nästa sida **starta program**. Detta hämtar programmanifestfilen på datorn. Dubbelklicka på manifestfilen om du vill starta programmet.
4. På sidan Välkommen **logga in**, och ange dina autentiseringsuppgifter.

    ![Välkomstskärmen](./media/data-lake-store-with-data-catalog/welcome.screen.png "välkomstskärmen")
5. På sidan Välj en datakälla sidan Välj **Azure Data Lake**, och klicka sedan på **nästa**.

    ![Välj datakälla](./media/data-lake-store-with-data-catalog/select-source.png "Välj datakälla")
6. Ange Data Lake Store-kontonamn som du vill registrera i Data Catalog på nästa sida. Lämna de andra alternativen som standard och klicka sedan på **Anslut**.

    ![Anslut till datakällan](./media/data-lake-store-with-data-catalog/connect-to-source.png "Anslut till datakällan")
7. Nästa sida kan delas in i följande segment.

    a. Den **Serverhierarki** ruta representerar mappstrukturen för Data Lake Store-konto. **$Root** representerar roten Data Lake Store-konto och **AmbulanceData** representerar den mapp som skapats i roten på Data Lake Store-konto.

    b. Den **tillgängliga objekt** visas i rutan filer och mappar under den **AmbulanceData** mapp.

    c. **Objekt som ska vara registrerad** visar en lista över filer och mappar som du vill registrera i Azure Data Catalog.

    ![Visa datastruktur](./media/data-lake-store-with-data-catalog/view-data-structure.png "visa datastruktur")
8. Du bör registrera alla filer i katalogen för den här självstudiekursen. Klickar du på den (![flytta objekt](./media/data-lake-store-with-data-catalog/move-objects.png "flytta objekt")) för att flytta alla filer och **objekt som ska registreras** rutan.

    Eftersom data registreras i en katalog för hela organisationen, är det en rekommenderade metod för att lägga till vissa metadata som du senare kan använda för att snabbt hitta data. Du kan till exempel lägga till en e-postadress för dataägaren (till exempel en som laddar upp data) eller lägga till en etikett för att identifiera data. Den här skärmbilden nedan visar en tagg att vi lägger till data.

    ![Visa datastruktur](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "visa datastruktur")

    Klicka på **registrera**.
9. Följande skärmdump anger att data har registrerats i Data Catalog.

    ![Registreringen har slutförts](./media/data-lake-store-with-data-catalog/registration-complete.png "visa datastruktur")
10. Klicka på **visa Portal** att gå tillbaka till Data Catalog-portalen och kontrollera att du kan nu komma åt registrerade data från portalen. Du kan använda den tagg som du använde vid registrering av data om du vill söka efter data.

     ![Söka efter data i katalogen](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "söka efter data i katalogen")
11. Du kan nu utföra åtgärder som att lägga till anteckningar och dokumentation data. Mer information finns i följande länkar.

    * [Kommentera datakällor i Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentdatakällor i Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Se också
* [Kommentera datakällor i Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentdatakällor i Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrera Data Lake Store med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md)
