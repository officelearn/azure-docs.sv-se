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
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 4d3a5c0c49b881db69a9d5cccc65406322212a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Kom igång med Azure Data Lake Store med Azure Portal
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

Lär dig mer om att använda Azure Portal för att skapa ett Azure Data Lake Store-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och ladda ned filer, ta bort ditt konto. Mer information finns i [Översikt över Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Krav
Innan du börjar den här självstudiekursen behöver du följande:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-azure-data-lake-store-account"></a>Skapa ett Azure Data Lake Store-konto

1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Klicka på **NY**, klicka på **Data + lagring** och klicka sedan på **Azure Data Lake Store**. Läs informationen på bladet **Azure Data Lake Store** och klicka sedan på **Skapa** i nedre vänstra hörnet på bladet.
3. På bladet **Ny Data Lake Store**, anger du de värden som visas på följande skärmbild:
   
    ![Skapa ett nytt Azure Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Skapa ett nytt Azure Data Lake-konto")
   
   * **Namn**. Ange ett unikt namn Data Lake Store-kontot.
   * **Prenumeration**. Välj den prenumeration under vilken du vill skapa ett nytt Data Lake Store-konto.
   * **Resursgrupp**. Välj en befintlig resursgrupp eller klicka på **Skapa en resursgrupp** för att skapa en. En resursgrupp är en behållare som innehåller relaterade resurser för ett program. Mer information finns i [Resursgrupper i Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Plats**: Välj en plats där du vill skapa Data Lake Store-kontot.
   * **Krypteringsinställningar**. Det finns tre alternativ:
     
     * **Aktivera inte kryptering**.
     * **Använd nycklar som hanteras av Azure Data Lake**.  om du vill att dina krypteringsnycklar ska hanteras av Azure Data Lake Store.
     * **Välj nycklar från ditt eget nyckelvalv**. Du kan välja ett befintligt Azure Key Vault eller skapa ett nytt nyckelvalv. Om du vill använda nycklarna från ett nyckelvalv måste du tilldela behörigheter för Azure Data Lake Store-konto för åtkomst till Azure Key Vault. Anvisningar finns i [Tilldela behörigheter till Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Data Lake Store-kryptering](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Store-kryptering")
       
        Klicka på **OK** i bladet **krypteringsinställningar**.

        Mer information finns i [Kryptering av data i Azure Data Lake Store](./data-lake-store-encryption.md).

4. Klicka på **Skapa**. Om du väljer att fästa kontot på startsidan, tas du tillbaka till startsidan där du kan se förloppet för Data Lake Store-kontoetableringen. När Data Lake Store-kontot har etablerats visas kontobladet.

### <a name="assign-permissions-to-azure-key-vault"></a>Tilldela behörigheter till Azure Key Vault
Om du använder nycklar från ett Azure Key Vault för att konfigurera kryptering för ett Data Lake Store-konto, måste du konfigurera åtkomst mellan Data Lake Store-kontot och Azure Key Vault-kontot. Utför följande steg för att göra det.

1. Om du använder nycklar från Azure Key Vault visar bladet för Data Lake Store-kontot en varning överst på sidan. Klicka på varningen för att öppna **kryptering**.
   
    ![Data Lake Store-kryptering](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Store-kryptering")
2. Bladet visar två alternativ för att konfigurera åtkomst.

    ![Data Lake Store-kryptering](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Store-kryptering")
   
   * Klicka på **Ge behörighet** på första alternativet för att konfigurera åtkomst. Det första alternativet är endast aktiverat när användaren som skapade kontot Data Lake Store också är administratör för Azure Key Vault.
   * Ett annat alternativ är att köra PowerShell-cmdleten som visas på bladet. Du måste vara ägare till Azure Key Vault eller ha möjlighet att bevilja behörighet för Azure Key Vault. När du har kört cmdlet:en, gå tillbaka till bladet och klickar på **Aktivera** att konfigurera åtkomst.

> [!NOTE]
> Du kan också skapa ett Data Lake Store-konto med hjälp av Azure Resource Manager-mallar. Mallarna är tillgängliga från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
    - Utan datakryptering: [Distribuera Azure Data Lake Store-konto utan datakryptering](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
    - Med datakryptering med hjälp av Data Lake Store: [Distribuera Data Lake Store-konto med kryptering (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
    - Med datakryptering med hjälp av Azure Key Vault: [Distribuera Data Lake Store-konto med kryptering (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Skapa mappar i Azure Data Lake Store-konto
Du kan skapa mappar under Data Lake Store-kontot för att hantera och lagra data.

1. Öppna det Data Lake Store-konto som du har skapat. I den vänstra rutan klickar du på **Bläddra**, klicka på **Data Lake Store** och från Data Lake Store-bladet klicka på kontonamnet där du vill skapa mappar. Om du fäst kontot på startsidan klickar du på kontoikonen.
2. I ditt Data Lake Store-kontoblad klickar du på **Data Explorer**.
   
    ![Skapa mappar i Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Skapa mappar i Data Lake Store-konto")
3. I bladet i Datautforskaren klickar du på **Ny mapp**, ange ett namn på den nya mappen och klicka sedan på **OK**.
   
    ![Skapa mappar i Data Lake Store-konto](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Skapa mappar i Data Lake Store-konto")
   
    Den nya mappen visas i bladet **Datautforskaren**. Du kan skapa kapslade mappar upp till valfri nivå.
   
    ![Skapa mappar i Data Lake-konto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Skapa mappar i Data Lake-konto")

## <a name="uploaddata"></a>Ladda upp data till Azure Data Lake Store-konto
Du kan ladda upp data till ett Azure Data Lake Store-konto direkt på rotnivå eller till en mapp som du har skapat i kontot. 

1. I bladet i **Datautforskaren** klickar du på **Ladda upp**. 
2. I bladet **Ladda upp filer** går du till de filer du vill ladda upp och klickar sedan på **Lägg till valda filer**. Du kan också välja mer än en fil att ladda upp.

    ![Överföra data](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Överföra data")

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Åtgärder som är tillgängliga för lagrade data
Klicka på ellipsikonen mot en fil och klicka i popup-menyn på den åtgärd du vill utföra på data.

![Egenskaper för data](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Egenskaper för data") 

## <a name="secure-your-data"></a>Skydda dina data
Du kan skydda data som lagras i ditt Azure Data Lake Store-konto med hjälp av Azure Active Directory och åtkomstkontroll (ACL). Mer information om hur du gör finns i [Skydda data i Azure Data Lake Store](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Ta bort Azure Data Lake Store-konto
Om du vill ta bort ett Azure Data Lake Store-konto från dina Data Lake Store-blad, klicka på **Ta bort**. För att bekräfta åtgärden uppmanas du att ange namnet på det konto som du vill ta bort. Ange namnet på kontot och klicka sedan på **Ta bort**.

![Ta bort Data Lake-konto](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Ta bort Data Lake-konto")

## <a name="next-steps"></a>Nästa steg
* [Använd Azure Data Lake Store för stordatakrav](data-lake-store-data-scenarios.md) 
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

