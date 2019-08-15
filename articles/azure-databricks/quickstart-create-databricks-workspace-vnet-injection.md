---
title: Skapa en Azure Databricks arbets yta i en Virtual Network
description: Den här artikeln beskriver hur du distribuerar Azure Databricks till ditt virtuella nätverk.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 12ac5c44a0ee479d84616b138f9e2369a195c275
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976468"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Snabbstart: Skapa en Azure Databricks arbets yta i en Virtual Network

Den här snabb starten visar hur du skapar en Azure Databricks arbets yta i ett virtuellt nätverk. Du kommer också att skapa ett Apache Spark-kluster på arbets ytan.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

> [!Note]
> Den här självstudien kan inte utföras med **Azures kostnads fri utvärderings prenumeration**.
> Om du har ett kostnads fritt konto går du till din profil och ändrar din prenumeration till **betala per**användning. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgifts gränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)och [begär en kvot ökning](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks arbets yta kan du välja pris nivån **utvärdering (Premium-14-dagar gratis DBU)** för att ge arbets ytan åtkomst till kostnads fria Premium Azure Databricks DBU i 14 dagar.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. I Azure Portal väljer du **skapa en resurs** > **nätverk** > **virtuellt nätverk**.

2. Använd följande inställningar under **Skapa virtuellt nätverk**: 

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Namn|databricks-quickstart|Välj ett namn för det virtuella nätverket.|
    |Adressutrymme|10.1.0.0/16|Det virtuella nätverkets adressintervall i CIDR-format.|
    |Subscription|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resource group|databricks-quickstart|Välj **Skapa nytt** och ange ett nytt resurs grupp namn för ditt konto.|
    |Location|\<Välj den region som är närmast dina användare\>|Välj en geografisk plats där du kan vara värd för det virtuella nätverket. Använd den plats som är närmast dina användare.|
    |Undernätsnamn|standard|Välj ett namn för standard under nätet i det virtuella nätverket.|
    |Undernätsadressintervall|10.1.0.0/24|Undernätets adressintervall i CIDR-notation. Det måste finnas i det virtuella nätverkets adress utrymme. Det går inte att redigera adress intervallet för ett undernät som används.|

    ![Skapa ett virtuellt nätverk på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. När distributionen är klar navigerar du till ditt virtuella nätverk och väljer **adress utrymme** under **Inställningar**. I rutan med texten *Lägg till ytterligare adress intervall*, infogar `10.179.0.0/16` och väljer du **Spara**.

    ![Adress utrymme för virtuella Azure-nätverk](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

1. I Azure Portal väljer du **skapa en resurs** > **analys** > **Databricks**.

2. Använd följande inställningar under **Azure Databricks tjänst**:

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Namn på arbetsyta|databricks-quickstart|Välj ett namn för din Azure Databricks-arbetsyta.|
    |Subscription|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resource group|databricks-quickstart|Välj samma resurs grupp som du använde för det virtuella nätverket.|
    |Location|\<Välj den region som är närmast dina användare\>|Välj samma plats som det virtuella nätverket.|
    |Prisnivå|Välj mellan standard eller Premium.|Mer information om pris nivåer finns på [prissättnings sidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Distribuera Azure Databricks arbets ytan i Virtual Network|Ja|Med den här inställningen kan du distribuera en Azure Databricks arbets yta i det virtuella nätverket.|
    |Virtuellt nätverk|databricks-quickstart|Välj det virtuella nätverk som du skapade i föregående avsnitt.|
    |Namn på offentligt undernät|offentligt-undernät|Använd standard namnet för offentliga undernät.|
    |CIDR-intervall för offentliga undernät|10.179.64.0/18|CIDR-intervallet för det här under nätet måste vara mellan/18 och/26.|
    |Namn på privat undernät|privat-undernät|Använd namnet på det privata under nätet som är standard.|
    |CIDR-intervall för privata undernät|10.179.0.0/18|CIDR-intervallet för det här under nätet måste vara mellan/18 och/26.|

    ![Skapa en Azure Databricks arbets yta på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. När distributionen är klar navigerar du till Azure Databricks resursen. Observera att peering av virtuella nätverk är inaktiverat. Observera också resurs gruppen och den hanterade resurs gruppen på sidan Översikt. 

    ![Azure Databricks översikt i Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Den hanterade resurs gruppen innehåller lagrings kontots fysiska plats (DBFS), Worker-SG (nätverks säkerhets grupp), arbetare-VNet (virtuellt nätverk). Det är också den plats där virtuella datorer, disk, IP-adress och nätverks gränssnitt kommer att skapas. Den här resurs gruppen är låst som standard. När ett kluster startas i det virtuella nätverket skapas dock ett nätverks gränssnitt mellan arbets tagarna-VNet i den hanterade resurs gruppen och det virtuella nätverket "hubb".

    ![Azure Databricks hanterad resurs grupp](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Skapa ett kluster

> [!NOTE]
> Om du vill använda ett kostnadsfritt konto för att skapa Azure Databricks-klustret ska du innan du skapar klustret gå till din profil och ändra prenumerationen till **betala per användning**. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. Gå tillbaka till Azure Databrickss tjänsten och välj **Starta arbets yta** på **översikts** sidan.

2. Välj **kluster** >  **+ skapa kluster**. Skapa sedan ett kluster namn som *databricks-snabb start-kluster*och godkänn de återstående standardinställningarna. Välj **Skapa kluster**.

    ![Skapa Azure Databricks kluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. När klustret körs går du tillbaka till den hanterade resurs gruppen i Azure Portal. Lägg märke till de nya virtuella datorerna, diskarna, IP-adresserna och nätverks gränssnitten. Ett nätverks gränssnitt skapas i alla offentliga och privata undernät med IP-adresser.  

    ![Azure Databricks hanterad resurs grupp efter att klustret har skapats](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Gå tillbaka till Azure Databricks arbets ytan och välj det kluster som du skapade. Gå sedan till fliken **körningar** på sidan **Spark UI** . Observera att adresserna för driv rutinen och körningarna finns i intervallet för privata undernät. I det här exemplet är driv rutinen 10.179.0.6 och Executer är 10.179.0.4 och 10.179.0.5. IP-adresserna kan vara olika.

    ![Azure Databricks Spark UI-körningar](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kan du avsluta klustret. Det gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**. Detta stoppar klustret.

Om du inte manuellt avslutar klustret kommer det att stoppas automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

Om du inte vill återanvända klustret kan du ta bort resurs gruppen som du skapade i Azure Portal.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett Spark-kluster i Azure Databricks som du har distribuerat till ett virtuellt nätverk. Fortsätt till nästa artikel och lär dig hur du frågar en SQL Server Linux Docker-behållare i det virtuella nätverket med hjälp av JDBC från en Azure Databricks Notebook.  

> [!div class="nextstepaction"]
>[Fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks Notebook](vnet-injection-sql-server.md)
