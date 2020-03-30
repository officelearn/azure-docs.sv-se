---
title: Apache Hive-bibliotek under klusterskapande - Azure HDInsight
description: Lär dig hur du lägger till Apache Hive-bibliotek (jar-filer) i ett HDInsight-kluster när klustret skapas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471031"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Lägga till anpassade Apache Hive-bibliotek när du skapar ditt HDInsight-kluster

Läs om hur du förinläsningar av [Apache Hive-bibliotek](https://hive.apache.org/) på HDInsight. Det här dokumentet innehåller information om hur du använder en skriptåtgärd för att förinläsning av bibliotek under klusterskapande. Bibliotek som läggs till med hjälp av stegen i det här dokumentet är globalt tillgängliga i Hive - det finns ingen anledning att använda [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) för att läsa in dem.

## <a name="how-it-works"></a>Hur det fungerar

När du skapar ett kluster kan du använda en skriptåtgärd för att ändra klusternoder när de skapas. Skriptet i det här dokumentet accepterar en enda parameter, som är bibliotekens plats. Den här platsen måste finnas i ett Azure Storage-konto och biblioteken måste lagras som jar-filer.

När klustret skapas räknar skriptet upp filerna, kopierar dem till katalogen `/usr/lib/customhivelibs/` på `hive.aux.jars.path` huvudet `core-site.xml` och arbetsnoder och lägger sedan till dem i egenskapen i filen. På Linux-baserade kluster uppdaterar den `hive-env.sh` också filen med platsen för filerna.

Om du använder skriptåtgärden i den här artikeln blir biblioteken tillgängliga när du använder en Hive-klient för **WebHCat**och **HiveServer2**.

## <a name="the-script"></a>Skriptet

**Skriptplats**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Krav

* Skripten måste tillämpas på både **noderna Huvud** och **Worker.**

* Burkarna som du vill installera måste lagras i Azure Blob Storage i en **enda behållare**.

* Lagringskontot som innehåller biblioteket med jar-filer **måste** vara länkat till HDInsight-klustret när det skapas. Det måste antingen vara standardlagringskontot eller ett konto som läggs till via __Lagringskontoinställningar__.

* WASB-sökvägen till behållaren måste anges som en parameter till skriptåtgärden. Om burkarna till exempel lagras i en behållare med namnet libs på ett `wasbs://libs@mystorage.blob.core.windows.net/` **lagringskonto** med namnet **mystorage,** skulle parametern vara .

  > [!NOTE]  
  > Det här dokumentet förutsätter att du redan har skapat ett lagringskonto, blob-behållare och överfört filerna till det.
  >
  > Om du inte har skapat ett lagringskonto kan du göra det via [Azure-portalen](https://portal.azure.com). Du kan sedan använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) för att skapa en behållare i kontot och överföra filer till det.

## <a name="create-a-cluster-using-the-script"></a>Skapa ett kluster med skriptet

1. Börja etablera ett kluster med hjälp av stegen i [Etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men slutför inte etableringen. Du kan också använda Azure PowerShell eller HDInsight .NET SDK för att skapa ett kluster med det här skriptet. Mer information om hur du använder dessa metoder finns i [Anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md). Välj åtgärden + Lägg till skript på fliken **Konfiguration + prissättning** för **Azure-portalen**.

1. Om **Storage**lagringskontot som innehåller biblioteket med jar-filer skiljer sig från det konto som används för klustret slutför du **Ytterligare lagringskonton**.

1. För **skriptåtgärder**anger du följande information:

    |Egenskap |Värde |
    |---|---|
    |Skripttyp|- Anpassad|
    |Namn|Bibliotek |
    |Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Nodtyper|Chef, Arbetare|
    |Parametrar|Ange WASB-adressen till behållar- och lagringskontot som innehåller burkarna. Till exempel `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > För Apache Spark 2.1, använd detta `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`bash script URI: .

1. Fortsätt att etablera klustret enligt beskrivningen i [Provision HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md).

När klusterskapandet är klart kan du använda burkarna som lagts till genom `ADD JAR` skriptet från Hive utan att behöva använda satsen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med Hive finns i [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
