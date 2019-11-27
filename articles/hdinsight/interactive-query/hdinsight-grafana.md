---
title: Använda Grafana i Azure HDInsight
description: Lär dig hur du kommer åt Grafana-instrumentpanelen med Apache Hadoop kluster i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: dae9c47f535d87214c9e1583562b4c0419cd44cf
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305435"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Åtkomst till Grafana i Azure HDInsight

[Grafana](https://grafana.com/) är ett populärt diagram och instrument panels verktyg med öppen källkod. Grafana är funktionen Rich; Det innebär inte bara att användare kan skapa anpassningsbara och shareable-instrumentpaneler, men den erbjuder även mallbaserade och skriptbaserade instrument paneler, LDAP-integration, flera data källor med mera.

För närvarande stöds Grafana i Azure HDInsight med kluster typerna HBase, Kafka och interaktiv fråga. Det finns inte stöd för kluster med Enterprise Security Pack aktiverat.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-apache-hadoop-cluster"></a>Skapa ett Apache Hadoop-kluster

I det här avsnittet skapar du ett interaktivt Query-kluster i HDInsight med hjälp av en Azure Resource Manager-mall. Du behöver inte ha någon erfarenhet av Resource Manager-mallar för att kunna följa den här artikeln. 

1. Klicka på knappen **Distribuera till Azure** om du vill logga in på Azure och öppna Resource Manager-mallen i Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Ange eller välj de värde som föreslås i följande skärmbild:

    > [!NOTE]  
    > De värden som du anger måste vara unika och bör följa riktlinjerna för namngivning. Mallen utför inte några verifieringskontroller. Om de värden som du anger redan används, eller inte följer riktlinjerna, får du ett felmeddelande när du har skickat mallen.       
    > 
    >
    
    ![HDInsight Linux komma igång med Resource Manager-mall på portalen](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuera Hadoop-kluster i HDInsigut med hjälp av Azure Portal och en mall för resurs grupp hanteraren")

    Ange eller välj följande värden:
    
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Prenumeration**     |  Välj din Azure-prenumeration. |
    |**Resursgrupp**     | Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en container med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. |
    |**Plats**     | Välj en Azure-plats där du vill skapa klustret.  Välj en plats närmare så får du bättre prestanda. |
    |**Klustertyp**     | Välj **Hadoop**. |
    |**Klusternamn**     | Ange ett namn för Apache Hadoop klustret. Eftersom alla kluster i HDInsight delar samma DNS-namnområde måste namnet vara unikt. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. De första och sista tecknen i namnet får inte vara bindestreck. |
    |**Inloggningsnamn och lösenord för klustret**     | Standard inloggnings namnet är **admin**. Lösen ordet måste bestå av minst 10 tecken och måste innehålla minst en siffra, en versal och en gemen bokstav, ett icke-alfanumeriskt tecken (förutom tecknen "" "\). Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |**SSH-användarnamn och lösenord**     | Standardanvändarnamnet är **sshuser**.  Du kan byta namn på SSH-användarnamn.  SSH-användarlösenordet har samma krav som lösenordet för klusterinloggning.|

    Vissa egenskaper har hårdkodats i mallen.  Du kan konfigurera dessa värden från mallen. Fler förklaringar av dessa egenskaper finns i [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Välj **Jag godkänner villkoren som anges ovan**, **Fäst på instrumentpanelen** och sedan **Köp**. Du ser en ny panel som heter **Skicka distribution** på portalens instrumentpanel. Det tar cirka 20 minuter att skapa ett kluster.

    ![Azure Malldistribution-förlopp](./media/hdinsight-grafana/deployment-progress-tile.png "Azure Malldistribution-förlopp")

4. När klustret skapas, ändras namnet i rubriktexten på panelen till det resursgruppsnamn som du har angett. Panelen visar även det HDInsight-kluster som skapas i resursgruppen.

    ![HDInsight Linux komma igång resurs grupp](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Resurs grupp för Azure HDInsight-kluster")

5. Panelen visas också den standardlagring som associeras med klustret. Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas Storage-konto av standardtyp. HDInsight-kluster och dess standard lagrings konto måste samplaceras i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster.
    

> [!NOTE]  
> För andra metoder för att skapa kluster och förstå de egenskaper som används i den här artikeln, se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Öppna Grafana-instrumentpanelen

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **HDInsight-kluster**och välj sedan det kluster namn som du skapade i det sista avsnittet.

3. Under **snabb länkar**klickar du på **kluster instrument panel**.

    ![HDInsight-kluster instrument panels Portal](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "HDInsight-klustrets instrument panel på portalen")

4. Klicka på panelen **Grafana** på instrument panelen. Du kan också bläddra till `/grafana/` sökvägen till kluster-URL: en. Till exempel `https://<clustername>.azurehdinsight.net/grafana/`.

5. Ange användarens autentiseringsuppgifter för Hadoop-klustret.

6. Grafana-instrumentpanelen visas och ser ut som i det här exemplet:

    ![HDInsight Grafana-webbinstrumentpanel](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana-instrumentpanel")

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. 

> [!NOTE]  
> Om du *omedelbart* fortsätter till nästa kurs för att lära dig hur man kör ETL-åtgärder med Hadoop på HDInsight vill du kanske låta klustret köra vidare. Anledningen till detta är att du i den här självstudiekursen måste skapa ett Hadoop-kluster igen. Men om du inte tänker göra nästa kurs direkt måste du ta bort klustret nu.

**Ta bort klustret och/eller Storage-kontot av standardtyp**

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

    ![Ikon för Azure Portal ta bort kluster](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Ta bort HDInsight-kluster")

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du skapar ett Linux-baserat HDInsight-kluster med hjälp av en Resource Manager-mall och hur du utför grundläggande Apache Hive frågor. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med interaktiv fråga på HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Om du är redo att börja arbeta med dina egna data och vill veta mer om hur data lagras i HDInsight eller om att hämta data till HDInsight, hittar du mer information i följande artiklar:

* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight](../hdinsight-upload-data.md).

Mer information om att analysera data med HDInsight finns i följande artiklar:

* Mer information om att använda Hive med HDInsight, inklusive hur du utför Hive-frågor från Visual Studio, finns i [Använda Apache Hive med HDInsight](../hdinsight-use-hive.md).
* Du kan läsa mer om Pig, ett språk som används för att omvandla data, i [Använda Apache Pig med HDInsight](../hdinsight-use-pig.md).
* Du kan läsa mer om MapReduce, ett sätt att skriva appar som bearbetar data i Hadoop, i [Använda MapReduce med HDInsight](../hdinsight-use-mapreduce.md).
* Du kan läsa mer om hur du använder HDInsight Tools för Visual Studio för att analysera data i HDInsight i [Komma igång med Visual Studio Hadoop-verktyg för HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Mer information om att skapa eller hantera HDInsight-kluster hittar du i följande artiklar:

* Mer information om att hantera ditt Linux-baserade HDInsight-kluster finns i [Hantera HDInsight-kluster med Ambari](../hdinsight-hadoop-manage-ambari.md).
* Mer information om de alternativ som du kan välja när du skapar ett HDInsight-kluster finns i [Skapa HDInsight i Linux med anpassade alternativ](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
