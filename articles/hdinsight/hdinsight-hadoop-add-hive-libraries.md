---
title: Lägga till Hive-bibliotek under Skapa kluster i HDInsight - Azure
description: Lär dig hur du lägger till Hive-bibliotek (jar-filer) till ett HDInsight-kluster när klustret skapas.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: fe7cb2c3990a5aa161665519490ede90be04e1ee
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594629"
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Lägg till anpassade Hive-bibliotek när du skapar HDInsight-kluster

Lär dig hur du Förhandsladda Hive-bibliotek på HDInsight. Det här dokumentet innehåller information om hur du använder en skriptåtgärd för att läsa in bibliotek när klustret skapas före. Bibliotek som har lagts till med hjälp av stegen i det här dokumentet är tillgängliga globalt i Hive - behöver inte använda [JAR-Lägg till](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) att läsa in dem.

## <a name="how-it-works"></a>Hur det fungerar

När du skapar ett kluster kan använda du en skriptåtgärd för att ändra noder som har skapats. Skriptet i det här dokumentet accepterar en parameter, vilket är platsen för biblioteken. Den här platsen måste vara i ett Azure Storage-konto och bibliotek som ska lagras som jar-filerna.

När du skapar klustret skriptet räknar upp filerna, kopierar dem till den `/usr/lib/customhivelibs/` katalog på huvud- och worker-noder läggs sedan till den `hive.aux.jars.path` -egenskapen i den `core-site.xml` filen. På Linux-baserade kluster, även uppdateras den `hive-env.sh` filen med platsen för filerna.

> [!NOTE]
> Med åtgärderna som skript i den här artikeln gör bibliotek som är tillgängliga i följande scenarier:
>
> * **Linux-baserade HDInsight** – när du använder en Hive-klient **WebHCat**, och **HiveServer2**.
> * **Windows-baserade HDInsight** – när du använder Hive-klienten och **WebHCat**.

## <a name="the-script"></a>Skriptet

**Skriptplats**

För **Linux-baserade kluster**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

För **Windows-baserade kluster**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

**Krav**

* Skripten måste tillämpas på både den **huvudnoder** och **arbetsnoder**.

* JAR-filer som du vill installera måste lagras i Azure Blob Storage i ett **enskild behållare**.

* Lagringskontot som innehåller bibliotek med jar-filerna **måste** länkas till HDInsight-klustret när du skapar. Det måste antingen vara standardkontot för lagring eller ett konto har lagts till via __valfri konfiguration__.

* WASB-sökväg till behållaren måste anges som en parameter för att skriptåtgärd. Exempel: om de JAR-filer som lagras i en behållare med namnet **libs** i ett lagringskonto med namnet **mystorage**, parametern skulle vara **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > Det här dokumentet förutsätter att du har redan skapat ett lagringskonto, blob-behållare och överfört filerna till den.
  >
  > Om du inte har skapat ett lagringskonto kan du göra så via den [Azure-portalen](https://portal.azure.com). Du kan sedan använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/) att skapa en behållare i kontot och ladda upp filer till den.

## <a name="create-a-cluster-using-the-script"></a>Skapa ett kluster med hjälp av skript

> [!NOTE]
> Följande steg skapar ett Linux-baserade HDInsight-kluster. Om du vill skapa ett Windows-baserat kluster, Välj **Windows** som klustret OS när du skapar klustret och Använd Windows (PowerShell) skript i stället för bash-skript.
>
> Du kan också använda Azure PowerShell eller HDInsight .NET SDK för att skapa ett kluster med hjälp av det här skriptet. Läs mer om hur du använder dessa metoder, [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

1. Börja etablera ett kluster med hjälp av stegen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men inte Slutför etableringen.

2. På den **valfri konfiguration** väljer **skriptåtgärder**, och ange följande information:

   * **NAMN på**: Ange ett eget namn för skriptåtgärden.

   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD**: Markera det här alternativet.

   * **WORKER**: Markera det här alternativet.

   * **ZOOKEEPER**: lämna fältet tomt.

   * **PARAMETRAR**: Ange WASB-adressen till behållaren och storage-kontot som innehåller de JAR-filer. Till exempel **wasb://libs@mystorage.blob.core.windows.net/**.

3. Längst ned på den **skriptåtgärder**, använda den **Välj** för att spara konfigurationen.

4. På den **valfri konfiguration** väljer **länkade Storage-konton** och välj den **Lägg till lagringsnyckel** länk. Välj det lagringskonto som innehåller de JAR-filer. Använd sedan den **Välj** knappar för att spara inställningarna och returnera den **valfri konfiguration**.

5. Spara valfri konfiguration genom att använda den **Välj** knappen längst ned på den **valfri konfiguration** avsnittet.

6. Fortsätta att etablera klustret enligt beskrivningen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md).

När klustret har skapats har du ska kunna använda JAR-filer som har lagts till via det här skriptet från Hive utan att använda den `ADD JAR` instruktionen.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du arbetar med Hive [använda Hive med HDInsight](hadoop/hdinsight-use-hive.md)
