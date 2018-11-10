---
title: Använda Grafana på Azure HDInsight
description: Lär dig hur du kommer åt Grafana i Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 973913e81157d2158074e50a61be0d73e5606ec3
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006149"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Grafana åtkomst i Azure HDInsight


Grafana är populära, open source-diagram och instrumentpaneler builder. Grafana är funktion omfattande; inte bara tillåter att användare skapar anpassningsbara och delbart instrumentpaneler, den erbjuder även mallbaserade/skriptade instrumentpaneler, LDAP-integrering, flera datakällor och mer.

Grafana finns för närvarande endast stöd av en klustertypen för interaktiva frågor i Azure HDInsight.


Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-hadoop-cluster"></a>Skapa ett Hadoop-kluster

I det här avsnittet skapar du ett interaktivt frågekluster i HDInsight med en Azure Resource Manager-mall. Du behöver inte ha någon erfarenhet av Resource Manager-mallar för att kunna följa den här artikeln. 

1. Klicka på knappen **Distribuera till Azure** om du vill logga in på Azure och öppna Resource Manager-mallen i Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange eller välj de värde som föreslås i följande skärmbild:

    > [!NOTE]
    > De värden som du anger måste vara unika och bör följa riktlinjerna för namngivning. Mallen utför inte några verifieringskontroller. Om de värden som du anger redan används, eller inte följer riktlinjerna, får du ett felmeddelande när du har skickat mallen.       
    > 
    >
    
    ![HDInsight Linux komma igång med Resource Manager-mall i portalen](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuera Hadoop-kluster i HDInsigut med hjälp av Azure portal och en resursgrupphanterarmall")

    Ange eller välj följande värden:
    
    |Egenskap   |Beskrivning  |
    |---------|---------|
    |**Prenumeration**     |  Välj din Azure-prenumeration. |
    |**Resursgrupp**     | Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en container med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. |
    |**Plats**     | Välj en Azure-plats där du vill skapa klustret.  Välj en plats närmare så får du bättre prestanda. |
    |**Klustertyp**     | Välj **Hadoop**. |
    |**Klusternamn**     | Ange ett namn för Hadoop-klustret. Eftersom alla kluster i HDInsight delar samma DNS-namnområde måste namnet vara unikt. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. De första och sista tecknen i namnet får inte vara bindestreck. |
    |**Inloggningsnamn och lösenord för klustret**     | Standardinloggningsnamnet är **admin**. Lösenordet måste bestå av minst 10 tecken och måste innehålla åtminstone en siffra, en versal, en gemen, ett alfanumeriskt tecken (förutom tecknen ' " ` \). Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |**SSH-användarnamn och lösenord**     | Standardanvändarnamnet är **sshuser**.  Du kan byta namn på SSH-användarnamn.  SSH-användarlösenordet har samma krav som lösenordet för klusterinloggning.|
       
    Vissa egenskaper har hårdkodats i mallen.  Du kan konfigurera dessa värden från mallen. Fler förklaringar av dessa egenskaper finns i [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Välj **Jag godkänner villkoren som anges ovan**, **Fäst på instrumentpanelen** och sedan **Köp**. Du ser en ny panel som heter **Skicka distribution** på portalens instrumentpanel. Det tar cirka 20 minuter att skapa ett kluster.

    ![Malldistributionsförlopp](./media/hdinsight-grafana/deployment-progress-tile.png "Distributionsförlopp för Azure-mall")

4. När klustret skapas, ändras namnet i rubriktexten på panelen till det resursgruppsnamn som du har angett. Panelen visar även det HDInsight-kluster som skapas i resursgruppen. 
   
    ![HDInsight Linux komma igång resursgrupp](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Azure HDInsight klusterresursgrupp")
    
5. Panelen visas också den standardlagring som associeras med klustret. Varje kluster är beroende av ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake-konto](../hdinsight-hadoop-use-data-lake-store.md). Det kallas Storage-konto av standardtyp. HDInsight-kluster och Storage-kontot av standardtyp måste finnas i samma Azure-region. Lagringskontot tas inte bort om du tar bort kluster.
    

> [!NOTE]
> Mer information om andra metoder för att skapa kluster och förstå de egenskaper som tillämpas i de här självstudierna finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).       
> 
>

## <a name="access-the-grafana-dashboard"></a>Få åtkomst till Grafana-instrumentpanel

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **HDInsight-kluster**, och välj sedan klustrets namn som du skapade i det sista avsnittet.
3. Under **snabblänkar**, klickar du på **klusterinstrumentpanel**.

    ![HDInsight-kluster instrumentpanelen portal](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "HDInsight-klusterinstrumentpanel på portalen")

4. Från instrumentpanelen klickar du på den **Grafana** panelen.
5. Ange användarautentiseringsuppgifter för Hadoop-kluster.
6. Grafana-instrumentpanel ser ut som:

    ![HDInsight Grafana-instrumentpanel](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana-instrumentpanel")

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. 

> [!NOTE]
> Om du *omedelbart* fortsätter till nästa kurs för att lära dig hur man kör ETL-åtgärder med Hadoop på HDInsight vill du kanske låta klustret köra vidare. Anledningen till detta är att du i den här självstudiekursen måste skapa ett Hadoop-kluster igen. Men om du inte tänker göra nästa kurs direkt måste du ta bort klustret nu.
> 
> 

**Ta bort klustret och/eller Storage-kontot av standardtyp**

1. Gå tillbaka till webbläsarfliken där du har Azure-portalen. Du bör vara på översiktssidan för klustret. Om du endast vill ta bort klustret men behålla standardlagringskontot kan du klicka på **Ta bort**.

    ![HDInsight-borttagningskluster](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Ta bort HDInsight-kluster")

2. Om du vill ta bort klustret och standardlagringskontot öppnar du resursgruppssidan genom att välja resursgruppens namn (markerat i föregående skärmbild).

3. Ta bort resursgruppen som innehåller klustret och standardlagringskontot genom att välja **Ta bort resursgrupp**. Tänk på att lagringskontot tas bort om du tar bort resursgruppen. Välj att bara ta bort klustret om du vill behålla Storage-kontot.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du fått veta hur du skapar ett Linux-baserat HDInsight-kluster med en Resource Manager-mall och hur du utför grundläggande Hive-frågor. I nästa artikel får du lära dig hur du utför en extraktions-, transformations- eller inläsningsåtgärd (ETL) med Hadoop på HDInsight.

> [!div class="nextstepaction"]
>[Extrahera, transformera och läsa in data med Apache Hive på HDInsight ](../hdinsight-analyze-flight-delay-data-linux.md)

Om du är redo att börja arbeta med dina egna data och vill veta mer om hur data lagras i HDInsight eller om att hämta data till HDInsight, hittar du mer information i följande artiklar:

* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight](../hdinsight-upload-data.md).

Mer information om att analysera data med HDInsight finns i följande artiklar:

* Mer information om att använda Hive med HDInsight, inklusive hur du utför Hive-frågor från Visual Studio, finns i [Använda Hive med HDInsight](../hdinsight-use-hive.md).
* Du kan läsa mer om Pig, ett språk som används för att omvandla data, i [Använda Pig med HDInsight](../hdinsight-use-pig.md).
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


