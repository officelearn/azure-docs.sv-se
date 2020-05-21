---
title: Snabb start – skapa en arbets yta
description: Skapa en Azure Synapse Analytics-arbetsyta genom att följa stegen i den här hand boken.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: df7753983cea2ca2cc285f04cd32da6ed065f415
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656159"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Snabb start: skapa en Azure Synapse Analytics-arbetsyta (för hands version)

I den här snabb starten beskrivs stegen för att skapa en Azure dataSynapses-arbetsyta med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Krav

- [Azure Data Lake Storage Gen2 lagrings konto](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="register-azure-synapse-resource-provider"></a>Registrera Azure Synapse Resource Provider
Registrera Azure Synapse Resource Provider om du inte redan har gjort det. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Information om hur du registrerar Azure Synapse Resource Provider finns i [Azure Resource providers och types](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types). Välj *Microsoft. Synapse* i listan över resurs leverantörer när du registrerar dig.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Skapa en Azure dataSynapses-arbetsyta med hjälp av Azure Portal

1. I rutan Microsoft Azure Sök anger du **Synapse-arbetsyta** och väljer sedan den här tjänsten.
![Azure Portal Sök fältet med Azure Synapse-arbetsytor som anges i.](media/quickstart-create-synapse-workspace/workspace-search.png)
2. På sidan **Synapse-arbetsytor** klickar du på **+ Lägg till**.
![Kommando för att skapa en ny Azure dataSynapses-arbetsyta är markerat.](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Fyll i formuläret för **Azure DataSynapses-arbetsyta** med följande information:

    | Inställningen | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Prenumeration** | *Din prenumeration* | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
    | **Resursgrupp** | *Alla resurs grupper* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Namn på arbetsyta** | mysampleworkspace | Anger namnet på arbets ytan, som också kommer att användas för anslutnings slut punkter.|
    | **Region** | USA, östra 2 | Anger arbets ytans plats.|
    | **Data Lake Storage Gen2** | Redovisning`storage account name` </br> Fil system:`root file system to use` | Anger ADLS Gen2 lagrings konto namn som ska användas som primär lagring och fil systemet som ska användas.|
    ||||

    ![Arbets ytans etablerings flöde – grundläggande flik.](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    Lagrings kontot kan väljas från:
    - En lista med ADLS Gen2 konton som är tillgängliga i din prenumeration
    - Anges manuellt med konto namnet

    > [!IMPORTANT]
    > Azure Synapse-arbetsytan måste kunna läsa och skriva till det valda ADLS Gen2 kontot. För alla lagrings konton som du länkar som primärt lagrings konto måste du dessutom ha aktiverat **hierarkiskt namn område** för att skapa lagrings kontot.
    >
    > Under ADLS Gen2 urvals fälten finns en anteckning om att den hanterade identiteten för arbets ytan kommer att tilldelas rollen **Storaqe BLOB data Contributor** i det valda data Lake Storage Gen2-filsystemet som ger fullständig åtkomst.

4. Valfritt Ändra något av fliken **standardvärden säkerhet + nätverk** :
5. Valfritt Lägg till taggar på fliken **taggar** .
6. Fliken **Sammanfattning** kör de nödvändiga verifieringarna för att se till att arbets ytan kan skapas. När verifieringen har slutförts trycker du på **skapa** ![ arbets ytan etablerings flöde – bekräftelse-fliken.](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. När resurs etablerings processen har slutförts visas en post för den skapade arbets ytan i listan över Synapse-arbetsytor. ![Lista över Synapse-arbetsytor som visar den nyligen etablerade arbets ytan.](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Azure dataSynapses-arbetsytan.
> [!WARNING]
> Om du tar bort en Azure Synapse-arbetsyta tas analys motorerna och de data som lagras i databasen med SQL-pooler och metadata för arbets ytan bort. Det går inte längre att ansluta till SQL-slutpunkterna Apache Spark slut punkter. Alla kod artefakter tas bort (frågor, antecknings böcker, jobb definitioner och pipeliner).
>
> Att ta bort arbets ytan påverkar **inte** data i den data Lake Store Gen2 som är länkad till arbets ytan.

Om du vill ta bort arbets ytan för Azure-Synapse utför du följande steg:

1. Gå till arbets ytan Azure Synapse för att ta bort.
1. Tryck på **ta bort** i kommando fältet.
 ![Översikt över Azure Synapse-arbetsyta – kommandot Ta bort är markerat.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Bekräfta borttagningen och tryck på knappen **ta bort** .
 ![Översikt över Azure Synapse-arbetsyta – bekräftelse dialog rutan ta bort arbets yta.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. När processen har slutförts visas inte längre Azure Synapse-arbetsytan i listan över arbets ytor.

## <a name="next-steps"></a>Nästa steg

Sedan kan du [skapa SQL-pooler](quickstart-create-sql-pool-studio.md) eller [skapa Apache Spark pooler](quickstart-create-apache-spark-pool-studio.md) för att börja analysera och utforska dina data.
