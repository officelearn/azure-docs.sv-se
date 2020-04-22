---
title: Snabbstart - skapa en arbetsyta
description: Skapa en Azure Synapse Analytics-arbetsyta genom att följa stegen i den här guiden.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1ccfb1b008a7e3e48f56b1af62087d303522ed73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676731"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Snabbstart: Skapa en Azure Synapse Analytics-arbetsyta (förhandsversion)

Den här snabbstarten beskriver stegen för att skapa en Azure Synapse-arbetsyta med hjälp av Azure-portalen.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Krav

- [Azure Data Lake Storage Gen2-lagringskonto](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Skapa en Azure Synapse-arbetsyta med Azure-portalen

1. I sökfönstret i Microsoft Azure anger du **Synapse-arbetsytan** och väljer sedan den här tjänsten.
![Sökfältet i Azure Portal med Azure Synapse-arbetsytor som skrivits in.](media/quickstart-create-synapse-workspace/workspace-search.png).
2. Klicka på **+ Lägg till**på sidan **Synapse-arbetsytor.**
![Kommando för att skapa nya Azure Synapse-arbetsyta markerad. ](media/quickstart-create-synapse-workspace/create-workspace-02.png).
3. Fyll i **azure synapse-arbetsyteformuläret** med följande information:

    | Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Prenumeration** | *Din prenumeration* | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
    | **Resursgrupp** | *Alla resursgrupper* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Namn på arbetsyta** | mysampleworkspace | Anger namnet på arbetsytan, som också ska användas för anslutningsslutpunkter.|
    | **Regionen** | USA, östra 2 | Anger arbetsytans plats.|
    | **Data Lake Storage Gen2** | Konto:`storage account name` </br> Filsystem:`root file system to use` | Anger adls Gen2-lagringskontonamnet som ska användas som primär lagring och vilket filsystem som ska användas.|
    ||||

    ![Arbetsflöde för etablering av arbetsyta - fliken Grunderna.](media/quickstart-create-synapse-workspace/create-workspace-03.png).

    Lagringskontot kan väljas från:
    - En lista över ADLS Gen2-konton som är tillgängliga i din prenumeration
    - Ange manuellt med kontonamnet

    > [!IMPORTANT]
    > Arbetsytan Azure Synapse måste kunna läsa och skriva till det valda ADLS Gen2-kontot. För alla lagringskonto som du länkar som det primära lagringskontot måste du dessutom ha aktiverat **hierarkiskt namnområde** när lagringskontot skapas.
    >
    > Under ADLS Gen2-urvalsfälten finns en anteckning om att arbetsytans hanterade identitet kommer att tilldelas rollen **Storaqe Blob Data Contributor** i det valda filsystemet Data Lake Storage Gen2 som ger den fullständig åtkomst.

4. (Valfritt) Ändra någon av standardfliken **Säkerhet + nätverk:**
5. (Valfritt) Lägg till eventuella taggar på fliken **Taggar.**
6. Fliken **Sammanfattning** kör nödvändiga valideringar för att säkerställa att arbetsytan kan skapas. När valideringen har **Create** ![slutförts trycker du på Skapa](media/quickstart-create-synapse-workspace/create-workspace-05.png)arbetsflödesetableringsflöde - bekräftelsefliken.
7. När resursetableringsprocessen har slutförts visas en post för den skapade arbetsytan i listan över Synapse-arbetsytor. ![Lista över Synapse-arbetsytor som visar den nyligen etablerade arbetsytan.](media/quickstart-create-synapse-workspace/create-workspace-07.png).

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Arbetsytan Azure Synapse.
> [!WARNING]
> Om du tar bort en Azure Synapse-arbetsyta tas analysmotorerna och data som lagras i databasen för de inneslutna SQL-poolerna och arbetsytans metadata bort. Det kommer inte längre att vara möjligt att ansluta till SQL-slutpunkter, Apache Spark-slutpunkter. Alla kodartefakter tas bort (frågor, anteckningsböcker, jobbdefinitioner och pipelines).
>
> Om du tar bort arbetsytan påverkas **inte** data i DataSjölagergen2 som är länkade till arbetsytan.

Om du vill ta bort arbetsytan Azure Synapse gör du följande:

1. Navigera till Azure Synapse-arbetsytan för att ta bort.
1. Tryck på **delete** i kommandofältet.
 ![Azure Synapse arbetsyta översikt - ta bort kommandot markerat.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Bekräfta borttagningen och tryck på **Knappen Ta bort.**
 ![Azure Synapse arbetsyta översikt - ta bort arbetsytan bekräftelse dialog.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. När processen är klar kommer Arbetsytan Azure Synapse inte längre att visas i listan över arbetsytor.

## <a name="next-steps"></a>Nästa steg

Därefter kan du [skapa SQL-pooler](quickstart-create-sql-pool.md) eller [skapa Apache Spark-pooler](quickstart-create-apache-spark-pool.md) för att börja analysera och utforska dina data.
