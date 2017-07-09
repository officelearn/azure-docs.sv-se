---
title: "Använda Azure-portalen för att komma igång med Data Lake Store | Microsoft Docs"
description: "Använd Azure-portalen för att skapa ett Data Lake Store-konto och utför grundläggande åtgärder i Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: f03181c727650eb0cfc8648cbe3d3838295cf6ad
ms.contentlocale: sv-se
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Kom igång med Azure Data Lake Store med hjälp av Azure Portal
> [!div class="op_single_selector"]
> * [Portalen](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Lär dig mer om att använda Azure Portal för att skapa ett Azure Data Lake Store-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake Store finns i [Översikt över Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-faster-with-videos"></a>Lär du dig snabbt med videor?
Titta på följande videor för att komma igång med Data Lake Store.

* [Skapa ett Data Lake Store-konto](https://mix.office.com/watch/1k1cycy4l4gen)
* [Hantera data i Data Lake Store med hjälp av Data Explorer](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Skapa ett Azure Data Lake Store-konto
1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Klicka på **NY**, klicka på **Data + lagring** och klicka sedan på **Azure Data Lake Store**. Läs informationen på bladet **Azure Data Lake Store** och klicka sedan på **Skapa** i nedre vänstra hörnet på bladet.
3. På bladet **Ny Data Lake Store**, anger du de värden som visas på skärmdumpen nedan:
   
    ![Skapa ett nytt Azure Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Skapa ett nytt Azure Data Lake-konto")
   
   * **Namn**. Ange ett unikt namn Data Lake Store-kontot.
   * **Prenumeration**. Välj den prenumeration under vilken du vill skapa ett nytt Data Lake Store-konto.
   * **Resursgrupp**. Välj en befintlig resursgrupp eller klicka på **Skapa en resursgrupp** för att skapa en. En resursgrupp är en behållare som innehåller relaterade resurser för ett program. Mer information finns i [Resursgrupper i Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Plats**: Välj en plats där du vill skapa Data Lake Store-kontot.
   * **Krypteringsinställningar**. Du kan välja om du vill kryptera Data Lake Store-kontot. Om du väljer att kryptera kan ange du hur du hanterar masterkrypteringsnyckeln för datakryptering i ditt konto.
     
     * (Valfritt) Välj **Aktivera inte kryptering** från listrutan om du vill välja bort kryptering.
     * (Standard) Välj **Använd nycklar som hanteras av Azure Data Lake** om du vill att Azure Data Lake Store ska hantera dina krypteringsnycklar.
       
         ![Data Lake Store-kryptering](./media/data-lake-store-get-started-portal/adls-encryption-1.png "Data Lake Store-kryptering")
     * (Valfritt) Välj **Välj nycklar från Azure Key Vault** om du vill använda egna nycklar i Azure Key Vault. Med det här alternativet kan du också skapa ett Key Vault-konto och nycklar även om du inte redan har ett konto.
       
         ![Data Lake Store-kryptering](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Store-kryptering")
       
       Klicka på **OK** i bladet **krypteringsinställningar**.
       
       > [!NOTE]
       > Om du använder nycklarna från Azure Key Vault för att konfigurera krypteringen av ett Data Lake Store-konto, måste du tilldela behörigheter för Azure Data Lake Store-konto för åtkomst till Azure Key Vault. Anvisningar för hur du gör detta finns i [Tilldela behörigheter till Azure Key Vault](#assign-permissions-to-the-azure-key-vault)
       > 
       > 
4. Klicka på **Skapa**. Om du väljer att fästa kontot på startsidan, tas du tillbaka till startsidan där du kan se förloppet för Data Lake Store-kontoetableringen. När Data Lake Store-kontot har etablerats visas kontobladet.

## <a name="assign-permissions-to-the-azure-key-vault"></a>Tilldela behörigheter till Azure Key Vault
Om du använder nycklar från ett Azure Key Vault för att konfigurera kryptering för ett Data Lake Store-konto, måste du konfigurera åtkomst mellan Data Lake Store-kontot och Azure Key Vault-kontot. Utför följande steg för att göra det.

1. Om du använder nycklar från Azure Key Vault visar bladet för Data Lake Store-kontot en varning överst på sidan. Klicka på varningen att öppna bladet **Konfigurera behörigheter för Key Vault**.
   
    ![Data Lake Store-kryptering](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Store-kryptering")
2. Bladet visar två alternativ för att konfigurera åtkomst.
   
   * Klicka på **Ge behörighet** på första alternativet för att konfigurera åtkomst. Det första alternativet är endast aktiverat när användaren som skapade kontot Data Lake Store också är administratör för Azure Key Vault.
   * Ett annat alternativ är att köra PowerShell-cmdleten som visas på bladet. Du måste vara ägare till Azure Key Vault eller ha möjlighet att bevilja behörighet för Azure Key Vault. När du har kört cmdlet:en, gå tillbaka till bladet och klickar på **Aktivera** att konfigurera åtkomst.

## <a name="createfolder"></a>Skapa mappar i Azure Data Lake Store-konto
Du kan skapa mappar under Data Lake Store-kontot för att hantera och lagra data.

1. Öppna Data Lake Store-kontot som du nyss skapade. I den vänstra rutan klickar du på **Bläddra**, klicka på **Data Lake Store** och från Data Lake Store-bladet klicka på kontonamnet där du vill skapa mappar. Om du fäst kontot på startsidan klickar du på kontoikonen.
2. I ditt Data Lake Store-kontoblad klickar du på **Data Explorer**.
   
    ![Skapa mappar i Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Skapa mappar i Data Lake Store-konto")
3. I ditt Data Lake Store-kontoblad klickar du på **Ny mapp**, ange ett namn på den nya mappen och klicka sedan på **OK**.
   
    ![Skapa mappar i Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Skapa mappar i Data Lake Store-konto")
   
    Den nya mappen visas i bladet **Data Explorer**. Du kan skapa kapslade mappar upp till valfri nivå.
   
    ![Skapa mappar i Data Lake-konto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Skapa mappar i Data Lake-konto")

## <a name="uploaddata"></a>Ladda upp data till Azure Data Lake Store-konto
Du kan ladda upp data till ett Azure Data Lake Store-konto direkt på rotnivå eller till en mapp som du har skapat i kontot. Följ stegen i skärmdumpen nedan för att ladda upp en fil till en undermapp från bladet **Data Explorer**. I den här skärmdumpen laddas filen upp till en undermapp som visas i länkarna (markerade med en röd ram).

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Överföra data](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Överföra data")

## <a name="properties"></a>Egenskaper och åtgärder som är tillgängliga för lagrade data
Klicka på den nyligen tillagda filen för att öppna bladet **Egenskaper**. De egenskaper som är associerade med filen och de åtgärder du kan utföra i filen är tillgängliga i det här bladet. Du kan också kopiera den fullständiga sökvägen till filen i ditt Azure Data Lake Store-konto, markerat i den röda rutan i skärmdumpen nedan.

![Egenskaper för data](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Egenskaper för data")

* Klicka på **Förhandsgranska** för att förhandsgranska filen direkt från webbläsaren. Du kan också ange format för förhandsgranskningen. Klicka på **Förhandsgranska**, klicka på **Format**, på bladet **Filförhandsgranskning** och på **Format på filförhandsgranskning** anger du alternativ såsom antal rader som ska visas, kodning som ska användas, avgränsare som ska användas osv.
  
  ![Format för filförhandsgranskning](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Format för filförhandsgranskning")
* Klicka på **Hämta** för att ladda ned filen till datorn.
* Klicka på **Byt namn på filen** för att byta namn på filen.
* Klicka på **Ta bort filen** för att ta bort filen.

## <a name="secure-your-data"></a>Skydda dina data
Du kan skydda data som lagras i ditt Azure Data Lake Store-konto med hjälp av Azure Active Directory och åtkomstkontroll (ACL). Mer information om hur du gör finns i [Skydda data i Azure Data Lake Store](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Ta bort Azure Data Lake Store-konto
Om du vill ta bort ett Azure Data Lake Store-konto från dina Data Lake Store-blad, klicka på **Ta bort**. För att bekräfta åtgärden uppmanas du att ange namnet på det konto som du vill ta bort. Ange namnet på kontot och klicka sedan på **Ta bort**.

![Ta bort Data Lake-konto](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Ta bort Data Lake-konto")

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Åtkomst till diagnostikloggar för Data Lake Store](data-lake-store-diagnostic-logs.md)


