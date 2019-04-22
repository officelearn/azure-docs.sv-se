---
title: Använd Azure-portalen för att komma igång med Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använda Azure-portalen för att skapa ett Azure Data Lake Storage Gen1-konto och utföra grundläggande åtgärder i Data Lake Storage Gen1-konto.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: e021d8c056028c03ac71d2a27c9128272f374da6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883589"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Kom igång med Azure Data Lake Storage Gen1 med Azure portal

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Lär dig mer om att använda Azure portal för att skapa ett Azure Data Lake Storage Gen1-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta datafiler, ta bort ditt konto, osv. Mer information finns i [översikt av Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här självstudiekursen behöver du följande:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto

1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Klicka på **skapa en resurs > Storage > Data Lake Storage Gen1**.
3. I den **ny Data Lake Storage Gen1** bladet anger du värden som visas i följande skärmbild:
   
    ![Skapa ett nytt konto för Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "skapar ett nytt Data Lake Storage Gen1-konto")
   
   * **Namn**. Ange ett unikt namn för Data Lake Storage Gen1-kontot.
   * **Prenumeration**. Välj den prenumeration som du vill skapa ett nytt Data Lake Storage Gen1-konto.
   * **Resursgrupp**. Välj en befintlig resursgrupp eller klicka på **Skapa en resursgrupp** för att skapa en. En resursgrupp är en container som innehåller relaterade resurser för ett program. Mer information finns i [Resursgrupper i Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Plats**: Välj en plats där du vill skapa Data Lake Storage Gen1-konto.
   * **Krypteringsinställningar**. Det finns tre alternativ:
     
     * **Aktivera inte kryptering**.
     * **Använd nycklar som hanteras av Data Lake Storage Gen1**om du vill att Data Lake Storage Gen1 att hantera dina krypteringsnycklar.
     * **Välj nycklar från ditt eget nyckelvalv**. Du kan välja ett befintligt Azure Key Vault eller skapa ett nytt nyckelvalv. Om du vill använda nycklarna från ett Nyckelvalv måste du tilldela behörigheter för Data Lake Storage Gen1-konto för åtkomst till Azure Key Vault. Anvisningar finns i [Tilldela behörigheter till Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Kryptering av data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "kryptering för Data Lake Storage Gen1")
       
        Klicka på **OK** i bladet **krypteringsinställningar**.

        Mer information finns i [kryptering av data i Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Klicka på **Skapa**. Om du har valt att fästa kontot på instrumentpanelen, tas du tillbaka till instrumentpanelen och du kan följa förloppet för Data Lake Storage Gen1 kontoetableringen. När Data Lake Storage Gen1-konto har etablerats visas.

## <a name="assign-permissions-to-azure-key-vault"></a>Tilldela behörigheter till Azure Key Vault
Om du använder nycklar från ett Azure Key Vault för att konfigurera kryptering för Data Lake Storage Gen1-konto, måste du konfigurera åtkomst mellan Data Lake Storage Gen1-konto och Azure Key Vault-konto. Utför följande steg för att göra det.

1. Om du använder nycklar från Azure Key Vault visar bladet för Data Lake Storage Gen1 kontot en varning längst upp. Klicka på varningen för att öppna **kryptering**.
   
    ![Kryptering av data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "kryptering för Data Lake Storage Gen1")
2. Bladet visar två alternativ för att konfigurera åtkomst.

    ![Kryptering av data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "kryptering för Data Lake Storage Gen1")
   
   * Klicka på **Ge behörighet** på första alternativet för att konfigurera åtkomst. Det första alternativet aktiveras bara om användaren som skapade kontot Data Lake Storage Gen1 också är administratör för Azure Key Vault.
   * Ett annat alternativ är att köra PowerShell-cmdleten som visas på bladet. Du måste vara ägare till Azure Key Vault eller ha möjlighet att bevilja behörighet för Azure Key Vault. När du har kört cmdlet:en, gå tillbaka till bladet och klickar på **Aktivera** att konfigurera åtkomst.

> [!NOTE]
> Du kan också skapa ett Data Lake Storage Gen1-konto med Azure Resource Manager-mallar. Mallarna är tillgängliga från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> - Utan datakryptering: [Distribuera Azure Data Lake Storage Gen1 konto utan datakryptering](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> - Med datakryptering med hjälp av Data Lake Storage Gen1: [Distribuera Data Lake Storage Gen1-konto med kryptering (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> - Med datakryptering med hjälp av Azure Key Vault: [Distribuera Data Lake Storage Gen1-konto med kryptering (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Skapa mappar i ett Data Lake Storage Gen1-konto
Du kan skapa mappar under Data Lake Storage Gen1 kontot för att hantera och lagra data.

1. Öppna det Data Lake Storage Gen1-konto som du skapade. Klicka på **Alla resurser** i den vänstra rutan, och klicka sedan på det kontonamn under vilket du vill skapa mappar. Om du fäst kontot på startsidan klickar du på kontoikonen.
2. I ditt Data Lake Storage Gen1-kontoblad klickar du på **Datautforskaren**.
   
    ![Skapa mappar i ett Data Lake Storage Gen1 konto](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "skapa mappar i ett Data Lake Storage Gen1-konto")
3. I bladet i Datautforskaren klickar du på **Ny mapp**, ange ett namn på den nya mappen och klicka sedan på **OK**.
   
    ![Skapa mappar i ett Data Lake Storage Gen1 konto](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "skapa mappar i ett Data Lake Storage Gen1-konto")
   
    Den nya mappen visas i bladet **Datautforskaren**. Du kan skapa kapslade mappar upp till en nivå.
   
    ![Skapa mappar i ett Data Lake-konto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "skapa mappar i ett Data Lake-konto")

## <a name="uploaddata"></a>Ladda upp data till ett Data Lake Storage Gen1-konto
Du kan ladda upp data till ett Data Lake Storage Gen1-konto direkt på rotnivå eller till en mapp som du har skapat i kontot. 

1. I bladet i **Datautforskaren** klickar du på **Ladda upp**. 
2. I bladet **Ladda upp filer** går du till de filer du vill ladda upp och klickar sedan på **Lägg till valda filer**. Du kan också välja mer än en fil att ladda upp.

    ![Överföra data](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Överföra data")

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Åtgärder som är tillgängliga för lagrade data
Klicka på ellipsikonen mot en fil och klicka i popup-menyn på den åtgärd du vill utföra på data.

![Egenskaper för data](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Egenskaper för data") 

## <a name="secure-your-data"></a>Skydda dina data
Du kan skydda data som lagras i ditt Data Lake Storage Gen1-konto med Azure Active Directory och åtkomstkontroll (ACL). Anvisningar för hur du gör finns i [skydda data i Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-a-data-lake-storage-gen1-account"></a>Ta bort ett Data Lake Storage Gen1-konto
Om du vill ta bort ett Data Lake Storage Gen1-konto från dina Data Lake Storage Gen1-bladet, klickar du på **ta bort**. För att bekräfta åtgärden uppmanas du att ange namnet på det konto som du vill ta bort. Ange namnet på kontot och klicka sedan på **Ta bort**.

![Ta bort Data Lake Storage Gen1 konto](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "ta bort Data Lake-konto")

## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Storage Gen1 för stordatakrav](data-lake-store-data-scenarios.md) 
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

