---
title: 'Snabbstart: Skapa interaktivt frågekluster med mallen - Azure HDInsight'
description: Den här snabbstarten visar hur du använder Resource Manager-mall för att skapa ett interaktivt frågekluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 74b1c25c8bab11c0b2a72510fd419df239e2c23a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603568"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snabbstart: Skapa interaktivt frågekluster i Azure HDInsight med hjälp av Resource Manager-mallen

I den här snabbstarten använder du en Azure Resource Manager-mall för att skapa ett [interaktivt](./apache-interactive-query-get-started.md) frågekluster i Azure HDInsight. Interaktiv fråga (kallas även Apache Hive LLAP eller [Analytisk bearbetning med låg latens)](https://cwiki.apache.org/confluence/display/Hive/LLAP)är en Azure HDInsight-klustertyp . [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-interactive-query-cluster"></a>Skapa ett interaktivt frågekluster

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-interactive-hive).

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json" range="1-158":::


Två Azure-resurser definieras i mallen:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): skapa ett Azure Storage-konto.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): skapa ett HDInsight-kluster.

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj knappen **Distribuera till Azure** nedan om du vill logga in på Azure och öppna Resource Manager-mallen.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Ange eller välj följande värden:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|Välj den Azure-prenumeration som används för klustret i listrutan.|
    |Resursgrupp|Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny**.|
    |Location|Värdet fylls i automatiskt med den plats som används för resursgruppen.|
    |Klusternamn|Ange ett globalt unikt namn. För den här mallen använder du bara gemener och siffror.|
    |Användarnamn för klusterinloggning|Ange användarnamn, standard är **admin**.|
    |Lösenord för klusterinloggning|Ange ett lösenord. Lösenordet måste vara minst 10 tecken långt och innehålla minst en siffra, en versal och en gemen, ett icke-alfanumeriskt tecken (utom tecknen ' " ' ). |
    |Ssh-användarnamn|Ange användarnamn, standard är sshuser|
    |Ssh lösenord|Ange lösenordet.|

    ![Resurshanterarens mall HBase](./media/quickstart-resource-manager-template/resource-manager-template-hive.png)

1. Läs igenom **villkoren**. Välj sedan **jag godkänner de villkor som anges ovan,** sedan **Köp**. Du får ett meddelande om att distributionen pågår. Det tar cirka 20 minuter att skapa ett kluster.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När klustret har skapats får du ett **meddelande om distribution** med länken Gå till **resurs.** På sidan Resursgrupp visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster har ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake Storage-kontoberoende.](../hdinsight-hadoop-use-data-lake-store.md) Det kallas standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste samlokaliseras i samma Azure-region. Om du tar bort kluster tas inte lagringskontot bort.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Navigera till klustret på Azure-portalen och välj **Ta bort**.

![Resurshanterarens mall HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resursgruppen tar du bort både HDInsight-klustret och standardlagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett interaktivt frågekluster i HDInsight med hjälp av en Resource Manager-mall. I nästa artikel får du lära dig hur du använder Apache Zeppelin för att köra Apache Hive-frågor.

> [!div class="nextstepaction"]
> [Köra Apache Hive-frågor i Azure HDInsight med Apache Zeppelin](./hdinsight-connect-hive-zeppelin.md)
