---
title: Integrera Data Lake Storage Gen1 med Azure Data Catalog
description: Lär dig hur du registrerar data från Azure Data Lake Storage Gen1 i Azure Data Catalog för att göra data synliga i din organisation.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 66191a52c6ef1f3d19afd2a47356487b07e9eff4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692343"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registrera data från Azure Data Lake Storage Gen1 i Azure Data Catalog
I den här artikeln får du lära dig hur du integrerar Azure Data Lake Storage Gen1 med Azure Data Catalog för att göra dina data synliga i en organisation genom att integrera den med Data Catalog. Mer information om att katalogisera data finns i [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). För att förstå scenarier där du kan använda Data Catalog, se [Azure Data Catalog vanliga scenarier](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivera din Azure-prenumeration** för data Lake Storage gen1. Se [instruktioner](data-lake-store-get-started-portal.md).
* **Ett data Lake Storage gen1 konto**. Följ anvisningarna i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md). I den här självstudien skapar du ett Data Lake Storage Gen1-konto med namnet **datacatalogstore**.

    När du har skapat kontot överför du en exempel data uppsättning till den. I den här självstudien får vi ladda upp alla CSV-filer under mappen **AmbulanceData** i [Azure Data Lake git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Du kan använda olika klienter, till exempel [Azure Storage Explorer](https://storageexplorer.com/), för att ladda upp data till en BLOB-behållare.
* **Azure Data Catalog**. Din organisation måste redan ha ett Azure Data Catalog skapat för din organisation. Endast en katalog tillåts för varje organisation.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrera Data Lake Storage Gen1 som källa för Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Gå till `https://azure.microsoft.com/services/data-catalog`och klicka på **Kom igång**.
1. Logga in på Azure Data Catalog Portal och klicka på **publicera data**.

    ![Registrera en datakälla](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrera en datakälla")
1. Klicka på **starta program**på nästa sida. Program manifest filen laddas ned på datorn. Starta programmet genom att dubbelklicka på manifest filen.
1. På Välkomst sidan klickar du på **Logga**in och anger dina autentiseringsuppgifter.

    ![Välkomstskärmen](./media/data-lake-store-with-data-catalog/welcome.screen.png "Välkomstskärmen")
1. På sidan Välj en data källa väljer du **Azure Data Lake Store**och klickar sedan på **Nästa**.

    ![Välja datakälla](./media/data-lake-store-with-data-catalog/select-source.png "Välja datakälla")
1. På nästa sida anger du det Data Lake Storage Gen1 konto namn som du vill registrera i Data Catalog. Lämna de andra alternativen som standard och klicka sedan på **Anslut**.

    ![Anslut till datakälla](./media/data-lake-store-with-data-catalog/connect-to-source.png "Anslut till datakälla")
1. Nästa sida kan delas in i följande segment.

    a. Rutan **Server-hierarki** representerar mappstrukturen för data Lake Storage gen1-kontot. **$Root** representerar data Lake Storage gen1 konto roten och **AmbulanceData** representerar mappen som skapats i roten för data Lake Storage gen1-kontot.

    b. I rutan **tillgängliga objekt** visas filerna och mapparna under mappen **AmbulanceData** .

    c. Rutan **objekt som ska registreras** visar de filer och mappar som du vill registrera i Azure Data Catalog.

    ![Visa data struktur](./media/data-lake-store-with-data-catalog/view-data-structure.png "Visa data struktur")
1. I den här självstudien bör du registrera alla filer i katalogen. Klicka på knappen (![Flytta objekt](./media/data-lake-store-with-data-catalog/move-objects.png "Flytta objekt")) för att flytta alla filer till **objekt som ska registreras** .

    Eftersom data ska registreras i en organisations omfattande data katalog, är det en metod för att lägga till vissa metadata som du senare kan använda för att snabbt hitta data. Du kan till exempel lägga till en e-postadress för data ägaren (till exempel en som laddar upp data) eller lägga till en tagg för att identifiera data. Skärm bilden nedan visar en tagg som du lägger till i data.

    ![Visa data struktur](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Visa data struktur")

    Klicka på **Registrera**.
1. Följande skärm bild anger att data har registrerats i Data Catalog.

    ![Registreringen är klar](./media/data-lake-store-with-data-catalog/registration-complete.png "Visa data struktur")
1. Klicka på **Visa Portal** för att gå tillbaka till Data Catalog Portal och kontrol lera att du nu kan komma åt registrerade data från portalen. Om du vill söka efter data kan du använda den tagg som du använde när du registrerade data.

     ![Sök efter data i katalogen](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Sök efter data i katalogen")
1. Nu kan du utföra åtgärder som att lägga till anteckningar och dokumentation till data. Mer information finns i följande länkar.

    * [Kommentera data källor i Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentera data källor i Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Se även
* [Kommentera data källor i Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentera data källor i Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrera Data Lake Storage Gen1 med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md)
