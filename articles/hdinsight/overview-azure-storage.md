---
title: Översikt över Azure Storage i HDInsight
description: Översikt över Azure Storage i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 43d948a2a98407bacc212ddc6e065c67a105f332
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873386"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Översikt över Azure Storage i HDInsight

Azure Storage är en robust lagringslösning för allmänt syfte som integreras sömlöst med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Genom ett HDFS-gränssnitt kan den fullständiga uppsättningen komponenter i HDInsight fungera direkt på strukturerade eller ostrukturerade data som lagras som blobbar.

Vi rekommenderar att du använder separata lagringsbehållare för standardklusterlagring och dina affärsdata. Separationen är att isolera HDInsight-loggarna och temporära filer från dina egna affärsdata. Vi rekommenderar också att du tar bort standardblobbehållaren, som innehåller program- och systemloggar, efter varje användning för att minska lagringskostnaden. Se till att hämta loggarna innan du tar bort containern.

Om du väljer att skydda ditt **lagringskonto med begränsningarna brandväggar och virtuella nätverk** i valda **nätverk**måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...**. Undantaget är så att HDInsight kan komma åt ditt lagringskonto.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram ger en abstrakt vy över HDInsight-arkitekturen i Azure Storage:

!["HDInsight-lagringsarkitektur"](./media/overview-azure-storage/storage-architecture.png "HDInsight-lagringsarkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Via HDInsight kan du även komma åt data i Azure Storage. Syntaxen ser ut så här:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Tänk på följande principer när du använder ett Azure Storage-konto med HDInsight-kluster:

* **Containrar på de lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när det skapas har du full tillgång till blobarna i dessa containrar.

* **Offentliga behållare eller offentliga blobbar i lagringskonton som inte är anslutna till ett kluster:** Du har skrivskyddad behörighet till blobbar i behållarna.
  
  > [!NOTE]  
  > Med offentliga behållare kan du hämta en lista över alla blobbar som är tillgängliga i behållaren och hämta behållarmetadata. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i [Hantera anonym läsåtkomst till behållare och blobbar](../storage/blobs/storage-manage-access-to-resources.md).

* **Privata behållare i lagringskonton som inte är anslutna till ett kluster:** Du kan inte komma åt blobbar i behållarna om du inte definierar lagringskontot när du skickar WebHCat-jobben.

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Som standard använder HDInsight de lagringskonton som definierats i filen core-site.xml. Du kan ändra den här inställningen med apache [Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive. Och MapReduce, Apache Hadoop streaming och Apache Pig, har en beskrivning av lagringskonton och metadata. (Den här aspekten gäller för närvarande för Gris med lagringskonton men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metabutiker](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. Blob-behållare lagrar data som nyckel-/värdepar och har ingen kataloghierarki. Men nyckelnamnet kan innehålla ett snedstrecktecken ( / ) för att få det att se ut som om en fil lagras i en katalogstruktur. En blob-tangent kan till `input/log1.txt`exempel vara . Det `input` finns ingen faktisk katalog, men på grund av snedstreckstecknet i nyckelnamnet ser nyckeln ut som en sökväg.

## <a name="benefits-of-azure-storage"></a>Fördelar med Azure Storage

Beräkningskluster och lagringsresurser som inte är samlokaliseras har implicita prestandakostnader. Dessa kostnader minskas med hur beräkningskluster skapas nära lagringskontoresurserna i Azure-regionen. I den här regionen kan beräkningsnoderna effektivt komma åt data via höghastighetsnätverket i Azure Storage.

När du lagrar data i Azure Storage i stället för HDFS får du flera fördelar:

* **Återanvändning och delning av data:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage, däremot, kan nås via antingen HDFS API:er eller Blob storage REST API:er. På grund av det här arrangemanget kan en större uppsättning program (inklusive andra HDInsight-kluster) och verktyg användas för att producera och använda data.

* **Data arkivering:** När data lagras i Azure Storage kan HDInsight-kluster som används för beräkning tas bort på ett säkert sätt utan att förlora användardata.

* **Kostnad för datalagring:** Att lagra data i DFS på lång sikt är dyrare än att lagra data i Azure Storage. Eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure Storage. Eftersom data inte behöver laddas om för varje generering av beräkningskluster sparar du också kostnader för datainläsning.

* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem bestäms skalan av antalet noder som du skapar för klustret. Att ändra skalan kan vara mer komplicerat än de elastiska skalningsfunktionerna som du får automatiskt i Azure Storage.

* **Geo-replikering:** Din Azure Storage kan geo-replikeras. Även om geo-replikering ger dig geografisk återställning och dataredundans, påverkar en redundans till den georepnadda platsen allvarligt din prestanda och det kan medföra extra kostnader. Så välj geo-replikering försiktigt och endast om värdet av data motiverar den extra kostnaden.

Vissa MapReduce-jobb och paket kan skapa mellanliggande resultat som du inte vill lagra i Azure Storage. I så fall kan du välja att lagra data i den lokala HDFS. HDInsight använder DFS för flera av dessa mellanliggande resultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon `ls`(till exempel , `copyFromLocal`och `mkdir`) fungerar som förväntat i Azure Storage. Endast de kommandon som är specifika för den inbyggda HDFS-implementeringen `fschk` (som kallas DFS), till exempel och `dfsadmin`, visar olika beteenden i Azure Storage.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
* [Översikt över Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)