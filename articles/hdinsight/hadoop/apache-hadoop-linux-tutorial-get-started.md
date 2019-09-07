---
title: 'Snabbstart: Skapa Apache Hadoop Clusters Resource Manager – Azure HDInsight'
description: I den här snabb starten skapar du Apache Hadoop kluster i Azure HDInsight med Resource Manager-mall
keywords: hadoop komma igång, hadoop linux, hadoop Snabbstart, komma igång, hive hive-Snabbstart
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: b65e642e19a9dd34c0f64f2d311fda0c70d4af72
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736154"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snabbstart: Skapa Apache Hadoop kluster i Azure HDInsight med Resource Manager-mall

I den här snabb starten får du lära dig hur du skapar ett Apache Hadoop kluster i Azure HDInsight med hjälp av en Resource Manager-mall.

Liknande mallar kan visas i [Azures snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). Mallreferensen finns [här](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).  Du kan också skapa ett kluster med hjälp av [Azure Portal](apache-hadoop-linux-create-cluster-get-started-portal.md).  

För närvarande innehåller HDInsight [sju olika klustertyper](../hdinsight-overview.md#cluster-types-in-hdinsight). Varje typ av kluster har stöd för olika komponentuppsättningar. Samtliga klustertyper stöder Hive. En lista över stödda komponenter som hanteras i HDInsight finns i [Vad är nytt i de Hadoop-klusterversioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)  

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Skapa ett Hadoop-kluster

1. Välj knappen **distribuera till Azure** nedan för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange eller välj följande värden:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Prenumeration**     |  Välj din Azure-prenumeration. |
    |**Resursgrupp**     | Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en container med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. |
    |**Location**     | Välj en Azure-plats där du vill skapa klustret.  Välj en plats närmare så får du bättre prestanda. |
    |**Klusternamn**     | Ange ett namn för Hadoop-klustret. Eftersom alla kluster i HDInsight delar samma DNS-namnområde måste namnet vara unikt. Namnet får bara innehålla gemena bokstäver, siffror och bindestreck och måste börja med en bokstav.  Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.  Namnet måste också innehålla mellan 3 och 59 tecken. |
    |**Klustertyp**     | Välj **Hadoop**. |
    |**Inloggningsnamn och lösenord för klustret**     | Standardinloggningsnamnet är **admin**. Lösenordet måste bestå av minst 10 tecken och måste innehålla åtminstone en siffra, en versal, en gemen, ett alfanumeriskt tecken (förutom tecknen ' " ` \). Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |**SSH-användarnamn och lösenord**     | Standardanvändarnamnet är **sshuser**.  Du kan byta namn på SSH-användarnamn.  SSH-användarlösenordet har samma krav som lösenordet för klusterinloggning.|

    Vissa egenskaper har hårdkodats i mallen.  Du kan konfigurera dessa värden från mallen. Fler förklaringar av dessa egenskaper finns i [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > De värden som du anger måste vara unika och bör följa riktlinjerna för namngivning. Mallen utför inte några verifieringskontroller. Om de värden som du anger redan används, eller inte följer riktlinjerna, får du ett felmeddelande när du har skickat mallen.  

    ![HDInsight Linux startar Resource Manager-mall på portalen](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuera Hadoop-kluster i HDInsight med hjälp av Azure Portal och en mall för resurs grupp hanteraren")

3. Välj **Jag godkänner villkoren som anges ovan** och välj sedan **Köp**. Du får ett meddelande om att distributionen pågår.  Det tar cirka 20 minuter att skapa ett kluster.

4. När klustret har skapats får du ett meddelande om att **distributionen har lyckats** tillsammans med en länk för att **gå till resursgruppen**.  På sidan **Resursgrupp** visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake Storage-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas Storage-konto av standardtyp. HDInsight-klustret och dess standard lagrings konto måste samplaceras i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster.

> [!NOTE]  
> För andra metoder för att skapa kluster och förstå de egenskaper som används i den här snabb starten, se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

> [!NOTE]  
> Om du *omedelbart* fortsätter till nästa kurs för att lära dig hur man kör ETL-åtgärder med Hadoop på HDInsight vill du kanske låta klustret köra vidare. Detta beror på att du i självstudien måste skapa ett Hadoop-kluster igen. Men om du inte tänker göra nästa kurs direkt måste du ta bort klustret nu.

**Ta bort klustret och/eller Storage-kontot av standardtyp**

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

    ![HDInsight-borttagningskluster](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Ta bort HDInsight-kluster")

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Apache Hadoop kluster i HDInsight med hjälp av en Resource Manager-mall. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med interaktiv fråga på HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)