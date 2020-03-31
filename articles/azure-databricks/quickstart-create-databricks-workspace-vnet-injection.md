---
title: Skapa en Azure Databricks-arbetsyta i din egen snabbstart för virtuella nätverk
description: I den här artikeln beskrivs hur du distribuerar Azure Databricks till ditt virtuella nätverk.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132665"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Snabbstart: Skapa en Azure Databricks-arbetsyta i ditt eget virtuella nätverk

Standarddistributionen av Azure Databricks skapar ett nytt virtuellt nätverk som hanteras av Databricks. Den här snabbstarten visar hur du skapar en Azure Databricks-arbetsyta i ditt eget virtuella nätverk i stället. Du skapar också ett Apache Spark-kluster på arbetsytan. 

Mer information om varför du kan välja att skapa en Azure Databricks-arbetsyta i ditt eget virtuella nätverk finns [i Distribuera Azure Databricks i ditt virtuella Azure-nätverk (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

> [!Note]
> Den här självstudien kan inte utföras med **Azure Free Trial Subscription**.
> Om du har ett gratis konto går du till din profil och ändrar din prenumeration **på användningsbaserad betalning.** Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgiftsgränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)och [begär en kvotökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks-arbetsyta kan du välja prisnivån **Utvärderingsversion (Premium – 14 dagar gratis dbUs)** för att ge arbetsytan åtkomst till kostnadsfria Premium Azure Databricks DBUs i 14 dagar.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. På Portal-menyn i Azure väljer du **Skapa en resurs**. Välj sedan **Nätverk > Virtuellt nätverk**.

    ![Skapa ett virtuellt nätverk på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Använd följande inställningar under **Skapa virtuellt nätverk:** 

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Prenumeration|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resursgrupp|databricks-snabbstart|Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto.|
    |Namn|databricks-snabbstart|Välj ett namn för det virtuella nätverket.|
    |Region|\<Välj den region som är närmast dina användare\>|Välj en geografisk plats där du kan vara värd för ditt virtuella nätverk. Använd den plats som är närmast dina användare.|

    ![Grunderna för ett virtuellt nätverk på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Välj **Nästa: IP-adresser >** och tillämpa följande inställningar. Välj sedan **Granska + skapa**.
    
    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |IPv4-adressutrymme|10.2.0.0/16|Det virtuella nätverkets adressintervall i CIDR-notation. CIDR-området måste vara mellan /16 och /24|
    |Namn på undernät|standard|Välj ett namn på standardundernätet i det virtuella nätverket.|
    |Undernätsadressintervall|10.2.0.0/24|Undernätets adressintervall i CIDR-notation. Det måste finnas i adressutrymmet för det virtuella nätverket. Adressintervallet för ett undernät som används kan inte redigeras.|

    ![Ange IP-konfigurationer för ett virtuellt nätverk på Azure-portalen](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. På fliken **Granska + skapa** väljer du **Skapa** för att distribuera det virtuella nätverket. När distributionen är klar navigerar du till det virtuella nätverket och väljer **Adressutrymme** under **Inställningar**. Infoga `10.179.0.0/16` i rutan Lägg till ytterligare adressintervall i rutan *Lägg till ytterligare adressintervall*och välj **Spara**.

    ![Azure virtuellt nätverksadressutrymme](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

1. På Portal-menyn i Azure väljer du **Skapa en resurs**. Välj sedan **Analytics > Databricks**.

    ![Skapa en Azure Databricks-arbetsyta på Azure-portalen](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. Använd följande inställningar under **Azure Databricks Service:**

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Namn på arbetsyta|databricks-snabbstart|Välj ett namn för din Azure Databricks-arbetsyta.|
    |Prenumeration|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resursgrupp|databricks-snabbstart|Välj samma resursgrupp som du använde för det virtuella nätverket.|
    |Location|\<Välj den region som är närmast dina användare\>|Välj samma plats som det virtuella nätverket.|
    |Prisnivå|Välj mellan Standard och Premium.|Mer information om prisnivåer finns på [statistiksidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Skapa grunderna i en Azure Databricks-arbetsyta](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. När du har angett inställningarna på sidan **Grunderna** väljer du **Nästa: Nätverk >** och använder följande inställningar:

    |Inställning|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Distribuera Azure Databricks-arbetsyta i ditt virtuella nätverk (VNet)|Ja|Med den här inställningen kan du distribuera en Azure Databricks-arbetsyta i ditt virtuella nätverk.|
    |Virtual Network|databricks-snabbstart|Välj det virtuella nätverk som du skapade i föregående avsnitt.|
    |Namn på offentligt undernät|offentliga undernät|Använd standardnamnet för det offentliga undernätet.|
    |CIDR-intervall för offentligt undernät|10.179.64.0/18|Använd ett CIDR-intervall upp till och med /26.|
    |Namn på privat undernät|privat-undernät|Använd standardnamnet för privat undernät.|
    |Privat CIDR-intervall för undernät|10.179.0.0/18|Använd ett CIDR-intervall upp till och med /26.|

    ![Lägga till VNet-information i Azure Databricks-arbetsytan på Azure-portalen](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. När distributionen är klar navigerar du till Azure Databricks-resursen. Observera att virtuell nätverks peering är inaktiverat. Lägg också märke till resursgruppen och den hanterade resursgruppen på översiktssidan. 

    ![Översikt över Azure Databricks i Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Den hanterade resursgruppen kan inte ändras och används inte för att skapa virtuella datorer. Du kan bara skapa virtuella datorer i resursgruppen som du hanterar.

    ![Hanterad resursgrupp för Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    När en arbetsytasdistribution misslyckas skapas arbetsytan fortfarande i ett misslyckat tillstånd. Ta bort den misslyckade arbetsytan och skapa en ny arbetsyta som löser distributionsfelen. När du tar bort den misslyckade arbetsytan tas även den hanterade resursgruppen och alla distribuerade resurser bort.

## <a name="create-a-cluster"></a>Skapa ett kluster

> [!NOTE]
> Om du vill använda ett kostnadsfritt konto för att skapa Azure Databricks-klustret ska du innan du skapar klustret gå till din profil och ändra prenumerationen till **betala per användning**. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. Gå tillbaka till din Azure Databricks-tjänst och välj **Starta arbetsyta** på **översiktssidan.**

2. Välj **kluster** > **+ Skapa kluster**. Skapa sedan ett klusternamn, till exempel *databricks-quickstart-cluster*, och acceptera de återstående standardinställningarna. Välj **Skapa kluster**.

    ![Skapa Azure Databricks-kluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. När klustret körs går du tillbaka till den hanterade resursgruppen i Azure-portalen. Lägg märke till de nya virtuella datorerna, diskarna, IP-adressen och nätverksgränssnitten. Ett nätverksgränssnitt skapas i vart och ett av de offentliga och privata undernäten med IP-adresser.  

    ![Hanterad azure-databricks-hanterad resursgrupp efter att klustret har skapats](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Gå tillbaka till din Azure Databricks-arbetsyta och välj det kluster du skapade. Navigera sedan till fliken **Utförare** på **spark-gränssnittssidan.** Observera att adresserna för drivrutinen och körarna finns i det privata undernätsområdet. I det här exemplet är drivrutinen 10.179.0.6 och utförare är 10.179.0.4 och 10.179.0.5. Dina IP-adresser kan vara annorlunda.

    ![Azure Databricks Spark UI-kördon](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kan du avsluta klustret. Detta gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**. Detta stoppar klustret.

Om du inte avslutar klustret manuellt stoppas det automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_ \_ minuter av inaktivitet** när du skapade klustret. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

Om du inte vill återanvända klustret kan du ta bort resursgruppen som du skapade i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du ett Spark-kluster i Azure Databricks som du har distribuerat till ett virtuellt nätverk. Gå vidare till nästa artikel för att lära dig hur du frågar en SQL Server Linux Docker-behållare i det virtuella nätverket med JDBC från en Azure Databricks-anteckningsbok.  

> [!div class="nextstepaction"]
>[Köra frågor mot en SQL Server Linux Docker-container i ett virtuellt nätverk från en Azure Databricks-notebook-fil](vnet-injection-sql-server.md)
