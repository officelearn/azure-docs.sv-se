---
title: Registrera data från Azure Data Lake Storage Gen1 i Azure Data Catalog | Microsoft-dokument
description: Registrera data från Azure Data Lake Storage Gen1 i Azure Data Catalog
services: data-lake-store,data-catalog
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd887560c0011fb1ec2141e33f02f7e3d8a39c81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60196612"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registrera data från Azure Data Lake Storage Gen1 i Azure Data Catalog
I den här artikeln får du lära dig hur du integrerar Azure Data Lake Storage Gen1 med Azure Data Catalog för att göra dina data upptäckbara inom en organisation genom att integrera dem med Data Catalog. Mer information om katalogisering av data finns i [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Information om scenarier där du kan använda Data Catalog finns i vanliga scenarier för [Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivera din Azure-prenumeration** för Data Lake Storage Gen1. Se [instruktioner](data-lake-store-get-started-portal.md).
* **Ett Data Lake Storage Gen1-konto**. Följ instruktionerna på [Kom igång med Azure Data Lake Storage Gen1 med Hjälp av Azure Portal](data-lake-store-get-started-portal.md). För den här självstudien skapar du ett Data Lake Storage Gen1-konto som heter **datacatalogstore**.

    När du har skapat kontot överför du en exempeldatauppsättning till det. För den här självstudien, låt oss ladda upp alla CSV-filer under **AmbulanceData-mappen** i [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Du kan använda olika klienter, till exempel [Azure Storage Explorer,](https://storageexplorer.com/)för att överföra data till en blob-behållare.
* **Azure-datakatalog**. Din organisation måste redan ha en Azure Data Catalog skapad för din organisation. Endast en katalog är tillåten för varje organisation.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrera datasjölagringgengen1 som källa för datakatalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Gå `https://azure.microsoft.com/services/data-catalog`till och klicka på **Kom igång.**
1. Logga in på Azure Data Catalog-portalen och klicka på **Publicera data**.

    ![Registrera en datakälla](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrera en datakälla")
1. Klicka på Starta **program**på nästa sida . Detta kommer att ladda ner ansökan manifestfilen på din dator. Dubbelklicka på manifestfilen för att starta programmet.
1. Klicka på Logga **in**på välkomstsidan och ange dina autentiseringsuppgifter.

    ![Välkomstskärmen](./media/data-lake-store-with-data-catalog/welcome.screen.png "Välkomstskärmen")
1. På sidan Välj en datakälla väljer du **Azure Data Lake Store**och klickar sedan på **Nästa**.

    ![Välja datakälla](./media/data-lake-store-with-data-catalog/select-source.png "Välja datakälla")
1. På nästa sida anger du det datasjölagringsgenm1-kontonamn som du vill registrera i datakatalogen. Lämna de andra alternativen som standard och klicka sedan på **Anslut**.

    ![Anslut till datakälla](./media/data-lake-store-with-data-catalog/connect-to-source.png "Anslut till datakälla")
1. Nästa sida kan delas in i följande segment.

    a. Rutan **Serverhierarki** representerar kontomappen DataSjölagring Gen1. **$Root** representerar kontot Data Lake Storage Gen1 och **AmbulanceData** representerar mappen som skapats i roten för datasjölagringsgenm1-kontot.

    b. I rutan **Tillgängliga objekt** visas filerna och mapparna under mappen **AmbulanceData.**

    c. **Objekt som ska registreras** rutan visar de filer och mappar som du vill registrera i Azure Data Catalog.

    ![Visa datastruktur](./media/data-lake-store-with-data-catalog/view-data-structure.png "Visa datastruktur")
1. För den här självstudien bör du registrera alla filer i katalogen. För det klickar du på knappen![(flytta objekt)](./media/data-lake-store-with-data-catalog/move-objects.png "Flytta objekt")för att flytta alla filer till **Objekt som ska registreras** rutan.

    Eftersom data registreras i en organisationsomfattande datakatalog är det en rekommenderad metod för att lägga till några metadata som du senare kan använda för att snabbt hitta data. Du kan till exempel lägga till en e-postadress för dataägaren (till exempel en som överför data) eller lägga till en tagg för att identifiera data. Skärminfånget nedan visar en tagg som du lägger till i data.

    ![Visa datastruktur](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Visa datastruktur")

    Klicka på **Registrera**.
1. Följande skärminsamling anger att data har registrerats i datakatalogen.

    ![Registreringen är klar](./media/data-lake-store-with-data-catalog/registration-complete.png "Visa datastruktur")
1. Klicka på **Visa portal** för att gå tillbaka till datakatalogportalen och kontrollera att du nu kan komma åt registrerade data från portalen. Om du vill söka i data kan du använda taggen du använde när du registrerade data.

     ![Sök data i katalogen](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Sök data i katalogen")
1. Du kan nu utföra åtgärder som att lägga till anteckningar och dokumentation till data. Mer information finns i följande länkar.

    * [Kommentera datakällor i datakatalogen](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentera datakällor i datakatalogen](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Se även
* [Kommentera datakällor i datakatalogen](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentera datakällor i datakatalogen](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrera DataSjölagringgend1 med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md)
