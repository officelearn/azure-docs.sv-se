---
title: Kom igång med Data Lake Store | Microsoft Docs
description: Använd portalen för att skapa ett Data Lake Store-konto och utför grundläggande åtgärder i Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: paulettm
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: nitinme

---
# Kom igång med Azure Data Lake Store med hjälp av Azure Portal
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Lär dig mer om att använda Azure Portal för att skapa ett Azure Data Lake Store-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake Store finns i [Översikt över Azure Data Lake Store](data-lake-store-overview.md).

## Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="signup"></a>Aktivera din Azure-prenumeration för en förhandsversion av Data Lake Store
Du måste först begära att din Azure-prenumeration aktiveras för Data Lake Store-förhandsversionen. Följ stegen nedan.

1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Klicka på **NY**, klicka på **Data + lagring** och klicka sedan på **Azure Data Lake Store**.
3. På bladet **Ny Data Lake Store**, klickar du på **Registrera dig för förhandsversion**. Läs informationen och klicka sedan på **OK**. Du får ett e-postmeddelande när prenumerationen har aktiverats för förhandsversion.
   
    ![Registrera dig för förhandsversion](./media/data-lake-store-get-started-portal/preview-signup.png "Create a new Azure Data Lake account")

## Lär du dig snabbt med videor?
Titta på följande videor för att komma igång med Data Lake Store.

* [Skapa ett Data Lake Store-konto](https://mix.office.com/watch/1k1cycy4l4gen)
* [Hantera data i Data Lake Store med hjälp av Data Explorer](https://mix.office.com/watch/icletrxrh6pc)

## Skapa ett Azure Data Lake Store-konto
1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Klicka på **NY**, klicka på **Data + lagring** och klicka sedan på **Azure Data Lake Store**. Läs informationen på bladet **Azure Data Lake Store** och klicka sedan på **Skapa** i nedre vänstra hörnet på bladet.
3. På bladet **Ny Data Lake Store**, anger du de värden som visas på skärmdumpen nedan:
   
    ![Skapa ett nytt Azure Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Create a new Azure Data Lake account")
   
   * **Prenumeration**. Välj den prenumeration under vilken du vill skapa ett nytt Data Lake Store-konto.
   * **Resursgrupp**. Välj en befintlig resursgrupp eller klicka på **Skapa en resursgrupp** för att skapa en. En resursgrupp är en behållare som innehåller relaterade resurser för ett program. Mer information finns i [Resursgrupper i Azure](../resource-group-overview.md#resource-groups).
   * **Plats**: Välj en plats där du vill skapa Data Lake Store-kontot.
4. Välj **Fäst på Startsidan** om du vill att Data Lake Store-kontot ska vara tillgängligt från Startsidan.
5. Klicka på **Skapa**. Om du väljer att fästa kontot på startsidan, tas du tillbaka till startsidan där du kan se förloppet för Data Lake Store-kontoetableringen. När Data Lake Store-kontot har etablerats visas kontobladet.
6. Expandera listrutan **Essentials** om du vill se information om ditt Data Lake Store-konto, såsom vilken resursgrupp det är en del av, platsen, osv. Klicka på ikonen **Snabbstart** för att se länkar till andra resurser som är relaterade till Data Lake Store.
   
    ![Ditt Azure Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Your Azure Data Lake account")

## <a name="createfolder"></a>Skapa mappar i Azure Data Lake Store-konto
Du kan skapa mappar under Data Lake Store-kontot för att hantera och lagra data.

1. Öppna Data Lake Store-kontot som du nyss skapade. I den vänstra rutan klickar du på **Bläddra**, klicka på **Data Lake Store** och från Data Lake Store-bladet klicka på kontonamnet där du vill skapa mappar. Om du fäst kontot på startsidan klickar du på kontoikonen.
2. I ditt Data Lake Store-kontoblad klickar du på **Data Explorer**.
   
    ![Skapa mappar i ett Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Create folders in Data Lake Store account")
3. I ditt Data Lake Store-kontoblad klickar du på **Ny mapp**, ange ett namn på den nya mappen och klicka sedan på **OK**.
   
    ![Skapa mappar i ett Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Create folders in Data Lake Store account")
   
    Den nya mappen visas i bladet **Data Explorer**. Du kan skapa kapslade mappar upp till valfri nivå.
   
    ![Skapa mappar i Data Lake-konto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Create folders in Data Lake account")

## <a name="uploaddata"></a>Ladda upp data till Azure Data Lake Store-konto
Du kan ladda upp data till ett Azure Data Lake Store-konto direkt på rotnivå eller till en mapp som du har skapat i kontot. Följ stegen i skärmdumpen nedan för att ladda upp en fil till en undermapp från bladet **Data Explorer**. I den här skärmdumpen laddas filen upp till en undermapp som visas i länkarna (markerade med en röd ram).

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Ladda upp data](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Upload data")

## <a name="properties"></a>Egenskaper och åtgärder som är tillgängliga för lagrade data
Klicka på den nyligen tillagda filen för att öppna bladet **Egenskaper**. De egenskaper som är associerade med filen och de åtgärder du kan utföra i filen är tillgängliga i det här bladet. Du kan också kopiera den fullständiga sökvägen till filen i ditt Azure Data Lake Store-konto, markerat i den röda rutan i skärmdumpen nedan.

![Egenskaper för data](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Properties on the data")

* Klicka på **Förhandsgranska** för att förhandsgranska filen direkt från webbläsaren. Du kan också ange format för förhandsgranskningen. Klicka på **Förhandsgranska**, klicka på **Format**, på bladet **Filförhandsgranskning** och på **Format på filförhandsgranskning** anger du alternativ såsom antal rader som ska visas, kodning som ska användas, avgränsare som ska användas osv.
  
  ![Format för filförhandsgranskning](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "File preview format")
* Klicka på **Hämta** för att ladda ned filen till datorn.
* Klicka på **Byt namn på filen** för att byta namn på filen.
* Klicka på **Ta bort filen** för att ta bort filen.

## Skydda dina data
Du kan skydda data som lagras i ditt Azure Data Lake Store-konto med hjälp av Azure Active Directory och åtkomstkontroll (ACL). Mer information om hur du gör finns i [Skydda data i Azure Data Lake Store](data-lake-store-secure-data.md).

## Ta bort Azure Data Lake Store-konto
Om du vill ta bort ett Azure Data Lake Store-konto från dina Data Lake Store-blad, klicka på **Ta bort**. För att bekräfta åtgärden uppmanas du att ange namnet på det konto som du vill ta bort. Ange namnet på kontot och klicka sedan på **Ta bort**.

![Ta bort Data Lake-konto](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Delete Data Lake account")

## Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Åtkomst till diagnostikloggar för Data Lake Store](data-lake-store-diagnostic-logs.md)

<!--HONumber=sep16_HO1-->


