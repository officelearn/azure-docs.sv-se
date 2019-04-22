---
title: Skapa en Azure Databricks-arbetsyta i ett virtuellt nätverk
description: Den här artikeln beskriver hur du distribuerar Azure Databricks till ditt virtuella nätverk.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288208"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Snabbstart: Skapa en Azure Databricks-arbetsyta i ett virtuellt nätverk

Den här snabbstarten visar hur du skapar en Azure Databricks-arbetsyta i ett virtuellt nätverk. Du kan också skapa ett Apache Spark-kluster i den arbetsytan.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. I Azure-portalen väljer du **skapa en resurs** > **nätverk** > **virtuellt nätverk**.

2. Under **skapa virtuellt nätverk**, gäller följande inställningar: 

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Namn|databricks-quickstart|Välj ett namn för det virtuella nätverket.|
    |Adressutrymme|10.1.0.0/16|Det virtuella nätverkets adressintervall i CIDR-format.|
    |Prenumeration|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resursgrupp|databricks-quickstart|Välj **Skapa ny** och ange ett nytt Resursgruppsnamn för ditt konto.|
    |Plats|\<Välj den region som är närmast dina användare\>|Välj en geografisk plats där du kan ha ditt virtuella nätverk. Använd den plats som är närmast dina användare.|
    |Namn på undernät|standard|Välj ett namn för standard-undernätet i det virtuella nätverket.|
    |Undernätsadressintervall|10.1.0.0/24|Undernätets adressintervall i CIDR-notation. Det måste finnas adressutrymmet för det virtuella nätverket. Adressintervallet för ett undernät som används kan inte redigeras.|

    ![Skapa ett virtuellt nätverk i Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. När distributionen är klar, går du till ditt virtuella nätverk och väljer **adressutrymme** under **inställningar**. I rutan *lägga till ytterligare adressintervall*, infoga `10.179.0.0/16` och välj **spara**.

    ![Adressutrymme för virtuellt Azure-nätverk](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

1. I Azure-portalen väljer du **skapa en resurs** > **Analytics** > **Databricks**.

2. Under **Azure Databricks-tjänst**, gäller följande inställningar:

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Namn på arbetsyta|databricks-quickstart|Välj ett namn för Azure Databricks-arbetsytan.|
    |Prenumeration|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resursgrupp|databricks-quickstart|Välj samma resursgrupp som du använde för det virtuella nätverket.|
    |Plats|\<Välj den region som är närmast dina användare\>|Välj samma plats som det virtuella nätverket.|
    |Prisnivå|Välj mellan Standard eller Premium.|Mer information om prisnivåer finns i den [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Distribuera Azure Databricks-arbetsytan i ditt virtuella nätverk|Ja|Den här inställningen kan du distribuera en Azure Databricks-arbetsyta i det virtuella nätverket.|
    |Virtual Network|databricks-quickstart|Välj det virtuella nätverket som du skapade i föregående avsnitt.|
    |Offentliga Undernätsnamn|public-subnet|Använd standardnamnet för den offentliga undernät.|
    |Offentliga CIDR-intervall för undernät|10.179.64.0/18|CIDR-intervall för det här undernätet måste vara mellan /18 och /26.|
    |Namn på privat undernät|private-subnet|Använd standardnamnet för privat undernät.|
    |CIDR-intervall för privata undernät|10.179.0.0/18|CIDR-intervall för det här undernätet måste vara mellan /18 och /26.|

    ![Skapa en Azure Databricks-arbetsyta på Azure-portalen](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. När distributionen är klar kan du gå till Azure Databricks-resursen. Observera att det virtuella nätverkets peering är inaktiverat. Observera också att resursgruppen och den hanterade resursgruppen på översiktssidan. 

    ![Azure Databricks-översikt i Azure-portalen](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Den hanterade resursgruppen innehåller den fysiska platsen för lagringskontot (antingen), worker-sg (nätverkssäkerhetsgrupp), Worker-vnet (virtuellt nätverk). Det är också platsen där virtuella datorer, diskar, IP-adress och nätverksgränssnitt kommer att skapas. Den här resursgruppen är låst som standard. men när ett kluster startas i det virtuella nätverket, skapas ett nätverksgränssnitt mellan det arbetare mellan virtuella nätverket i den hanterade resursgruppen och ”hubbens” virtuella nätverk.

    ![Azure Databricks hanterad resursgrupp](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Skapa ett kluster

> [!NOTE]
> Om du vill använda ett kostnadsfritt konto för att skapa Azure Databricks-klustret ska du innan du skapar klustret gå till din profil och ändra prenumerationen till **betala per användning**. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. Gå tillbaka till din Azure Databricks-tjänst och välj **Starta arbetsyta** på den **översikt** sidan.

2. Välj **kluster** > **+ skapa klustret**. Skapa sedan ett namn, t.ex. *databricks-quickstart-kluster*, och acceptera resten av standardinställningarna. Välj **Skapa kluster**.

    ![Skapa Azure Databricks-kluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. När klustret körs kan du gå tillbaka till den hanterade resursgruppen i Azure-portalen. Lägg märke till den nya virtuella datorer, diskar, IP-adress och nätverksgränssnitt. Ett nätverksgränssnitt har skapats i var och en av de offentliga och privata undernät med IP-adresser.  

    ![Azure Databricks hanterade resursgruppen när klustret har skapats](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Gå tillbaka till Azure Databricks-arbetsytan och välj klustret du skapade. Gå sedan till den **Executors** fliken på den **Spark Användargränssnittet** sidan. Observera att adresserna för drivrutinen och executors är i intervallet privat undernät. I det här exemplet drivrutinen är 10.179.0.6 och executors är 10.179.0.4 och 10.179.0.5. IP-adresser kan vara olika.

    ![Azure Databricks Spark-Användargränssnittet executors](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kan du avsluta klustret. Det gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**. Detta stoppar klustret.

Om du inte manuellt avslutar klustret kommer det att stoppas automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

Om du inte vill återanvända klustret kan du ta bort resursgruppen som du skapade i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du ett Spark-kluster i Azure Databricks som du har distribuerat till ett virtuellt nätverk. Gå vidare till nästa artikel om du vill lära dig hur du frågar en SQL Server Linux Docker-behållare i det virtuella nätverket med hjälp av JDBC från en Azure Databricks-anteckningsbok.  

> [!div class="nextstepaction"]
>[Fråga en SQL Server Linux Docker-behållare i ett virtuellt nätverk från en Azure Databricks-anteckningsbok](vnet-injection-sql-server.md)
