---
title: 'Snabbstart: Skapa Apache Hadoop-kluster i Azure HDInsight med hjälp av Resource Manager-mallen'
description: I den här snabbstarten skapar du Apache Hadoop-kluster i Azure HDInsight med hjälp av Resource Manager-mallen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: ae0f29b8085bd9637f527f2a58229dd89ce6933b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064680"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snabbstart: Skapa Apache Hadoop-kluster i Azure HDInsight med hjälp av Resource Manager-mallen

I den här snabbstarten använder du en Azure Resource Manager-mall för att skapa ett [Apache Hadoop-kluster](./apache-hadoop-introduction.md) i Azure HDInsight. Hadoop var det ursprungliga ramverket med öppen källkod för distribuerad bearbetning och analys av stordatauppsättningar i kluster. Hadoop-ekosystemet innehåller relaterad programvara och verktyg, inklusive Apache Hive, Apache HBase, Spark, Kafka och många andra.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
För närvarande innehåller HDInsight [sju olika klustertyper](../hdinsight-overview.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över stödda komponenter som hanteras i HDInsight finns i [Vad är nytt i de Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json" range="1-148":::


Två Azure-resurser definieras i mallen:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): skapa ett Azure Storage-konto.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): skapa ett HDInsight-kluster.

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj knappen **Distribuera till Azure** nedan om du vill logga in på Azure och öppna Resource Manager-mallen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Ange eller välj följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Prenumeration|Välj den Azure-prenumeration som används för klustret i listrutan.|
    |Resursgrupp|Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny**.|
    |Location|Värdet fylls i automatiskt med den plats som används för resursgruppen.|
    |Klusternamn|Ange ett globalt unikt namn. För den här mallen använder du bara gemener och siffror.|
    |Typ av kluster | Välj **hadoop**. |
    |Användarnamn för klusterinloggning|Ange användarnamn, standard är **admin**.|
    |Lösenord för klusterinloggning|Ange ett lösenord. Lösenordet måste vara minst 10 tecken långt och innehålla minst en siffra, en versal och en gemen, ett icke-alfanumeriskt tecken (utom tecknen ' " ' ). |
    |Ssh-användarnamn|Ange användarnamn, standard är **sshuser**|
    |Ssh lösenord|Ange lösenordet.|

    Vissa egenskaper har hårdkodats i mallen.  Du kan konfigurera dessa värden från mallen. Fler förklaringar av dessa egenskaper finns i [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > De värden som du anger måste vara unika och bör följa riktlinjerna för namngivning. Mallen utför inte några verifieringskontroller. Om de värden som du anger redan används, eller inte följer riktlinjerna, får du ett felmeddelande när du har skickat mallen.  

    ![HDInsight Linux kommer igång Resource Manager-mall på portalen](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuera Hadoop-kluster i HDInsight med hjälp av Azure-portalen och en resursgruppshanteraremall")

1. Läs igenom **villkoren**. Välj sedan **jag godkänner de villkor som anges ovan,** sedan **Köp**. Du får ett meddelande om att distributionen pågår. Det tar cirka 20 minuter att skapa ett kluster.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När klustret har skapats får du ett **meddelande om distribution** med länken Gå till **resurs.** På sidan Resursgrupp visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster har ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake Storage-kontoberoende.](../hdinsight-hadoop-use-data-lake-store.md) Det kallas standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste samlokaliseras i samma Azure-region. Om du tar bort kluster tas inte lagringskontot bort.

> [!NOTE]  
> Andra metoder för att skapa kluster och förstå de egenskaper som används i den här snabbstarten finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

> [!NOTE]  
> Om du *omedelbart* fortsätter till nästa kurs för att lära dig hur man kör ETL-åtgärder med Hadoop på HDInsight vill du kanske låta klustret köra vidare. Detta beror på att du i självstudien måste skapa ett Hadoop-kluster igen. Men om du inte tänker göra nästa kurs direkt måste du ta bort klustret nu.

Navigera till klustret på Azure-portalen och välj **Ta bort**.

![HDInsight ta bort kluster från portalen](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight ta bort kluster från portalen")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resursgruppen tar du bort både HDInsight-klustret och standardlagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Apache Hadoop-kluster i HDInsight med hjälp av en Resource Manager-mall. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med interaktiv fråga på HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
