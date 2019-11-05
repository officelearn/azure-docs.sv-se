---
title: Apache Hive bibliotek när klustret skapas – Azure HDInsight
description: Lär dig hur du lägger till Apache Hive bibliotek (jar-filer) till ett HDInsight-kluster när klustret skapas.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 51a93aaec4abdb2dd9d8fad042c079a48d4ea7a3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494837"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Lägg till anpassade Apache Hive-bibliotek när du skapar ett HDInsight-kluster

Lär dig hur du förinstallerar [Apache Hive](https://hive.apache.org/) bibliotek i HDInsight. Det här dokumentet innehåller information om hur du använder en skript åtgärd för att läsa in bibliotek när klustret skapas. Bibliotek som lagts till med hjälp av stegen i det här dokumentet är globalt tillgängliga i Hive – du behöver inte använda [Lägg till jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) för att läsa in dem.

## <a name="how-it-works"></a>Så här fungerar det

När du skapar ett kluster kan du använda en skript åtgärd för att ändra klusternoderna när de skapas. Skriptet i det här dokumentet accepterar en enda parameter, som är platsen för biblioteken. Den här platsen måste vara i ett Azure Storage konto och biblioteken måste lagras som jar-filer.

När klustret skapas räknar skriptet upp filerna, kopierar dem till `/usr/lib/customhivelibs/`-katalogen på Head-och Worker-noderna och lägger sedan till dem i `hive.aux.jars.path`-egenskapen i `core-site.xml`s filen. I Linux-baserade kluster uppdaterar den också `hive-env.sh`-filen med platsen för filerna.

> [!NOTE]  
> Genom att använda skript åtgärderna i den här artikeln blir biblioteken tillgängliga i följande scenarier:
>
> * **Linux-baserat HDInsight** – när du använder en Hive-klient, **WebHCat**och **HiveServer2**.
> * **Windows-baserat HDInsight** – när du använder Hive-klienten och **WebHCat**.

## <a name="the-script"></a>Skriptet

**Skript plats**

För **Linux-baserade kluster**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

För **Windows-baserade kluster**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Krav**

* Skripten måste användas både för **huvudnoderna** och **arbetsnoderna**.

* Jar v7 som du vill installera måste lagras i Azure Blob Storage i en **enda behållare**.

* Lagrings kontot som innehåller biblioteket för JAR-filer **måste** länkas till HDInsight-klustret när det skapas. Det måste antingen vara standard lagrings kontot eller ett konto som läggs till via __valfri konfiguration__.

* WASB-sökvägen till behållaren måste anges som en parameter för skript åtgärden. Om t. ex. jar v7 lagras i en behållare med namnet **libs** på ett lagrings konto med namnet **unstorage**, skulle parametern vara **wasb://libs\@mystorage.blob.Core.Windows.net/** .

  > [!NOTE]  
  > Det här dokumentet förutsätter att du redan har skapat ett lagrings konto, en BLOB-behållare och överfört filerna till det.
  >
  > Om du inte har skapat ett lagrings konto kan du göra det via [Azure Portal](https://portal.azure.com). Du kan sedan använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) för att skapa en behållare i kontot och ladda upp filer till den.

## <a name="create-a-cluster-using-the-script"></a>Skapa ett kluster med hjälp av skriptet

> [!NOTE]  
> Följande steg skapar ett Linux-baserat HDInsight-kluster. Om du vill skapa ett Windows-baserat kluster väljer du **Windows** som kluster-OS när du skapar klustret och använder Windows-skriptet (PowerShell) i stället för bash-skriptet.
>
> Du kan också använda Azure PowerShell eller HDInsight .NET SDK för att skapa ett kluster med det här skriptet. Mer information om hur du använder dessa metoder finns i [Anpassa HDInsight-kluster med skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

1. Starta etableringen av ett kluster med hjälp av stegen i [Etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men Slutför inte etablering.

2. I det **valfria konfigurations** avsnittet väljer du **skript åtgärder**och anger följande information:

   * **Namn**: Ange ett eget namn för skript åtgärden.

   * **Skript-URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **Head**: Markera det här alternativet.

   * **Arbetare**: Markera det här alternativet.

   * **ZOOKEEPER**: lämna tomt.

   * **Parametrar**: Ange WASB-adressen till den behållare och det lagrings konto som innehåller jar v7. Till exempel **wasb://libs\@mystorage.blob.Core.Windows.net/** .

3. Klicka på knappen **Välj** i slutet av **skript åtgärderna**för att spara konfigurationen.

4. I avsnittet **valfri konfiguration** väljer du **länkade lagrings konton** och väljer länken **Lägg till en lagrings nyckel** . Välj det lagrings konto som innehåller jar v7. Använd sedan knapparna **Välj** för att spara inställningar och returnera den **valfria konfigurationen**.

5. Om du vill spara den valfria konfigurationen använder du knappen **Välj** längst ned i det **valfria konfigurations** avsnittet.

6. Fortsätt att etablera klustret enligt beskrivningen i [Etablera HDInsight-kluster i Linux](hdinsight-hadoop-provision-linux-clusters.md).

När klustret har skapats kan du använda jar v7 som lagts till via det här skriptet från Hive utan att behöva använda instruktionen `ADD JAR`.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med Hive finns i [använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
