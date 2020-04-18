---
title: 'Snabbstart: Skapa ML Services-kluster med mallen - Azure HDInsight'
description: Den här snabbstarten visar hur du använder Resource Manager-mall för att skapa ett ML Services-kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: cde8d6932400966ae22720b1e86f3c5164f25b30
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603432"
---
# <a name="quickstart-create-ml-services-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snabbstart: Skapa ML Services-kluster i Azure HDInsight med hjälp av Resource Manager-mallen

I den här snabbstarten använder du en Azure Resource Manager-mall för att skapa ett [ML Services-kluster](./r-server-overview.md) i Azure HDInsight. Microsoft Machine Learning Server är tillgängligt som ett distributionsalternativ när du skapar HDInsight-kluster i Azure. Klustertypen som tillhandahåller det här alternativet kallas ML-tjänster. Den här funktionen ger datavetare, statistiker och R-programmerare åtkomst på begäran till skalbara, distribuerade analysmetoder på HDInsight.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-ml-services-cluster"></a>Skapa ett ML Services-kluster

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-rserver).

:::code language="json" source="~/quickstart-templates/101-hdinsight-rserver/azuredeploy.json" range="1-171":::


Två Azure-resurser definieras i mallen:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): skapa ett Azure Storage-konto.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): skapa ett HDInsight-kluster.

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj knappen **Distribuera till Azure** nedan om du vill logga in på Azure och öppna Resource Manager-mallen.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

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

    ![Resurshanterarens mall HBase](./media/quickstart-resource-manager-template/resource-manager-template-rserver.png)

1. Läs igenom **villkoren**. Välj sedan **jag godkänner de villkor som anges ovan,** sedan **Köp**. Du får ett meddelande om att distributionen pågår. Det tar cirka 20 minuter att skapa ett kluster.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När klustret har skapats får du ett **meddelande om distribution** med länken Gå till **resurs.** På sidan Resursgrupp visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster har ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake Storage-kontoberoende.](../hdinsight-hadoop-use-data-lake-store.md) Det kallas standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste samlokaliseras i samma Azure-region. Om du tar bort kluster tas inte lagringskontot bort.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Navigera till klustret på Azure-portalen och välj **Ta bort**.

![Resurshanterarens mall HBase](./media/quickstart-resource-manager-template/azure-portal-delete-rserver.png)

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resursgruppen tar du bort både HDInsight-klustret och standardlagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett ML Services-kluster i HDInsight med hjälp av en Resource Manager-mall. I nästa artikel får du lära dig hur du kör ett R-skript med RStudio Server som demonstrerar med Spark för distribuerade R-beräkningar..

> [!div class="nextstepaction"]
> [Köra ett R-skript i ett ML Services-kluster i Azure HDInsight med RStudio Server](./machine-learning-services-quickstart-job-rstudio.md)
