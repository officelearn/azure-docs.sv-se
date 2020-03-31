---
title: Komma igång med Azure Data Lake Storage Gen1 – portal
description: Använd Azure-portalen för att skapa ett Data Lake Storage Gen1-konto och utföra grundläggande åtgärder i kontot.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 1bfb9362aa0e893a8bf1a758e5a0069e85ab227b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265589"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Komma igång med Azure Data Lake Storage Gen1 med Azure-portalen

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Lär dig hur du använder Azure-portalen för att skapa ett Data Lake Storage Gen1-konto och utföra grundläggande åtgärder som att skapa mappar, ladda upp och ladda ned datafiler, ta bort ditt konto osv. Mer information finns i [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudiekursen behöver du följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto

1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Klicka på **Skapa en resurs > lagring > datasjölagringgend1**.
3. Ange de värden som visas i följande skärmbild i bladet **New Data Lake Storage Gen1:**

    ![Skapa ett nytt Data Lake Storage Gen1-konto](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Skapa ett nytt Data Lake Storage Gen1-konto")

   * **Namn**. Ange ett unikt namn för datasjölagringsgenm1-kontot.
   * **Prenumeration**. Välj den prenumeration som du vill skapa ett nytt Data Lake Storage Gen1-konto under.
   * **Resursgrupp**. Välj en befintlig resursgrupp eller klicka på **Skapa en resursgrupp** för att skapa en. En resursgrupp är en container som innehåller relaterade resurser för ett program. Mer information finns i [Resursgrupper i Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Plats**: Välj en plats där du vill skapa datasjölagringsgenm1-kontot.
   * **Krypteringsinställningar**. Det finns tre alternativ:

     * **Aktivera inte kryptering**.
     * **Använd nycklar som hanteras av Data Lake Storage Gen1**om du vill att Gen1 för datasjölagring ska hantera krypteringsnycklarna.
     * **Välj nycklar från ditt eget nyckelvalv**. Du kan välja ett befintligt Azure Key Vault eller skapa ett nytt nyckelvalv. Om du vill använda nycklarna från ett Key Vault måste du tilldela behörigheter för Data Lake Storage Gen1-kontot för att komma åt Azure Key Vault. Anvisningar finns i [Tilldela behörigheter till Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Gen1-kryptering för datasjölagring](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Gen1-kryptering för datasjölagring")

        Klicka på **OK** i bladet **krypteringsinställningar**.

        Mer information finns [i Kryptering av data i Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Klicka på **Skapa**. Om du väljer att fästa kontot på instrumentpanelen tas du tillbaka till instrumentpanelen och du kan se förloppet för din Data Lake Storage Gen1-kontoetablering. När Data Lake Storage Gen1-kontot har etablerats visas kontobladet.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Tilldela behörigheter till Azure Key Vault

Om du använde nycklar från ett Azure Key Vault för att konfigurera kryptering på Data Lake Storage Gen1-kontot måste du konfigurera åtkomst mellan Data Lake Storage Gen1-kontot och Azure Key Vault-kontot. Utför följande steg för att göra det.

1. Om du använde nycklar från Azure Key Vault visas en varning högst upp på bladet för datasjölagringsgenm1. Klicka på varningen för att öppna **kryptering**.

    ![Gen1-kryptering för datasjölagring](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Gen1-kryptering för datasjölagring")
2. Bladet visar två alternativ för att konfigurera åtkomst.

    ![Gen1-kryptering för datasjölagring](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Gen1-kryptering för datasjölagring")

   * Klicka på **Ge behörighet** på första alternativet för att konfigurera åtkomst. Det första alternativet är endast aktiverat när användaren som skapade Data Lake Storage Gen1-kontot också är administratör för Azure Key Vault.
   * Ett annat alternativ är att köra PowerShell-cmdleten som visas på bladet. Du måste vara ägare till Azure Key Vault eller ha möjlighet att bevilja behörighet för Azure Key Vault. När du har kört cmdlet:en, gå tillbaka till bladet och klickar på **Aktivera** att konfigurera åtkomst.

> [!NOTE]
> Du kan också skapa ett Data Lake Storage Gen1-konto med Azure Resource Manager-mallar. Mallarna är tillgängliga från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Utan datakryptering: [Distribuera Azure Data Lake Storage Gen1-konto utan datakryptering](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Med datakryptering med Data Lake Storage Gen1: [Deploy Data Lake Storage Gen1-konto med kryptering(Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Med datakryptering med Azure Key Vault: [Deploy Data Lake Storage Gen1-konto med kryptering(Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Skapa mappar

Du kan skapa mappar under ditt DataSjölagringsgend1-konto för att hantera och lagra data.

1. Öppna kontot DataSjölagring Gen1 som du har skapat. Klicka på Alla **resurser**i den vänstra rutan och klicka sedan på det kontonamn som du vill skapa mappar under från bladet **Alla resurser.** Om du fäst kontot på startsidan klickar du på kontoikonen.
2. Klicka på **Data Explorer**i kontobladet Datasjölagringsgenm1.

    ![Skapa mappar i ett Gen1-konto för lagring av datasjö](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Skapa mappar i ett Gen1-konto för lagring av datasjö")
3. I bladet i Datautforskaren klickar du på **Ny mapp**, ange ett namn på den nya mappen och klicka sedan på **OK**.

    ![Skapa mappar i ett Gen1-konto för lagring av datasjö](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Skapa mappar i ett Gen1-konto för lagring av datasjö")

    Den nya mappen visas i bladet **Datautforskaren**. Du kan skapa kapslade mappar upp till valfri nivå.

    ![Skapa mappar i ett DataSjökonto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Skapa mappar i ett DataSjökonto")

## <a name="upload-data"></a><a name="uploaddata"></a>Ladda upp data

Du kan ladda upp dina data till ett Data Lake Storage Gen1-konto direkt på rotnivå eller till en mapp som du skapade i kontot.

1. I bladet i **Datautforskaren** klickar du på **Ladda upp**.
2. I bladet **Ladda upp filer** går du till de filer du vill ladda upp och klickar sedan på **Lägg till valda filer**. Du kan också välja mer än en fil att ladda upp.

    ![Ladda upp data](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Ladda upp data")

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Åtgärder som är tillgängliga för lagrade data

Klicka på ellipsikonen mot en fil och klicka i popup-menyn på den åtgärd du vill utföra på data.

![Egenskaper för data](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Egenskaper för data")

## <a name="secure-your-data"></a>Skydda dina data

Du kan skydda data som lagras i ditt Data Lake Storage Gen1-konto med Hjälp av Azure Active Directory och åtkomstkontroll (ACL: er). Instruktioner om hur du gör det finns [i Skydda data i Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Ta bort ditt konto

Om du vill ta bort ett DataSjölagringsgenm1-konto klickar du på **Ta bort**från bladet DataSjölagringsgenm1. För att bekräfta åtgärden uppmanas du att ange namnet på det konto som du vill ta bort. Ange namnet på kontot och klicka sedan på **Ta bort**.

![Ta bort datasjölagringsgenm1-konto](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Ta bort Data Lake-konto")

## <a name="next-steps"></a>Nästa steg

* [Använda Azure Data Lake Storage Gen1 för stordatakrav](data-lake-store-data-scenarios.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
