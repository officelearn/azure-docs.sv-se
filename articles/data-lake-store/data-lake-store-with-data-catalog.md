---
title: Registrera data från Azure Data Lake Storage Gen1 i Azure Data Catalog | Microsoft Docs
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
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877892"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registrera data från Azure Data Lake Storage Gen1 i Azure Data Catalog
I den här artikeln får du lära dig hur du integrerar Azure Data Lake Storage Gen1 med Azure Data Catalog för att göra data synlig inom en organisation genom att integrera dem med Data Catalog. Mer information om katalogiserar data finns i [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Information om scenarier där du kan använda Data Catalog finns i [vanliga scenarier för Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivera din Azure-prenumeration** för Data Lake Storage Gen1. Se [instruktioner](data-lake-store-get-started-portal.md).
* **Ett konto för Data Lake Storage Gen1**. Följ anvisningarna på [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md). Den här självstudien skapar du ett Data Lake Storage Gen1 konto med namnet **datacatalogstore**.

    När du har skapat kontot, ladda upp en exempeldatauppsättning till den. I den här självstudien kan vi överför CSV-filer under den **AmbulanceData** mapp i den [Azure Data Lake Git-lagringsplats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Du kan använda olika klienter, till exempel [Azure Storage Explorer](https://storageexplorer.com/), för att ladda upp data till en blob-behållare.
* **Azure Data Catalog**. Din organisation måste redan ha en Azure Data Catalog som skapats för din organisation. Endast en katalog är tillåten för varje organisation.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrera Data Lake Storage Gen1 som källa för Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Gå till `https://azure.microsoft.com/services/data-catalog`, och klicka på **börjar**.
1. Logga in på Azure Data Catalog-portalen och klicka på **publicera data**.

    ![Registrera en datakälla](./media/data-lake-store-with-data-catalog/register-data-source.png "registrerar en datakälla")
1. På nästa sida klickar du på **starta program**. Detta hämtar program-manifestfilen på datorn. Dubbelklicka på manifestfilen om du vill starta programmet.
1. På sidan Välkommen **logga in**, och ange dina autentiseringsuppgifter.

    ![Välkomstskärmen](./media/data-lake-store-with-data-catalog/welcome.screen.png "välkomstskärmen")
1. I Välj en datakälla sida **Azure Data Lake Store**, och klicka sedan på **nästa**.

    ![Välj datakälla](./media/data-lake-store-with-data-catalog/select-source.png "Välj datakälla")
1. Ange kontonamnet för Data Lake Storage Gen1 som du vill registrera i Data Catalog på nästa sida. Lämna de andra alternativen som standard och klicka sedan på **Connect**.

    ![Anslut till datakällan](./media/data-lake-store-with-data-catalog/connect-to-source.png "Anslut till datakällan")
1. Nästa sida kan delas in i följande segment.

    a. Den **Serverhierarki** ruta representerar mappstrukturen för Data Lake Storage Gen1 konto. **$Root** representerar roten för Data Lake Storage Gen1 konto, och **AmbulanceData** representerar den mapp som skapas i roten för Data Lake Storage Gen1-konto.

    b. Den **tillgängliga objekt** box Listar filerna och mapparna under den **AmbulanceData** mapp.

    c. **Objekt som ska registreras** rutan visas de filer och mappar som du vill registrera i Azure Data Catalog.

    ![Visa datastruktur](./media/data-lake-store-with-data-catalog/view-data-structure.png "visa datastruktur")
1. I den här självstudiekursen ska du registrera alla filer i katalogen. För det, klickar du på den (![flytta objekt](./media/data-lake-store-with-data-catalog/move-objects.png "flytta objekt")) för att flytta alla filer och **objekt som ska registreras** box.

    Eftersom data kommer att registreras i en organisationsomfattande data catalog, är det en rekommenderad metod för att lägga till vissa metadata som du senare kan använda för att snabbt hitta data. Du kan till exempel lägga till en e-postadress för dataägaren (till exempel en som överför data) eller lägga till en tagg för att identifiera vilka data. Skärmdumpen nedan visar en tagg för att du lägger till data.

    ![Visa datastruktur](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "visa datastruktur")

    Klicka på **registrera**.
1. Följande skärmdump anger att data har registrerats i Data Catalog.

    ![Registreringen har slutförts](./media/data-lake-store-with-data-catalog/registration-complete.png "visa datastruktur")
1. Klicka på **visa Portal** att gå tillbaka till Data Catalog-portalen och verifiera att du kan nu komma åt registrerade data från portalen. Om du vill söka efter data, kan du använda taggen som du använde när du registrerar data.

     ![Söka efter data i katalogen](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "söka efter data i katalogen")
1. Du kan nu utföra åtgärder som att lägga till anteckningar och dokumentation om data. Mer information finns i följande länkar.

    * [Kommentera datakällor i Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentera datakällor i Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Se också
* [Kommentera datakällor i Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentera datakällor i Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrera Data Lake Storage Gen1 med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md)
