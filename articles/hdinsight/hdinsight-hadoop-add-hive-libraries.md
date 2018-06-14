---
title: 'Lägga till Hive-bibliotek under HDInsight - kluster: Azure | Microsoft Docs'
description: Lär dig hur du lägger till Hive-bibliotek (jar-filer) till ett HDInsight-kluster när klustret skapas.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 71e2859085dc4a9f4fa327d88faff4fecf5108ef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400664"
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Lägg till anpassade Hive-bibliotek när du skapar ditt HDInsight-kluster

Lär dig mer om att läsa in HDInsight Hive-bibliotek förväg. Det här dokumentet innehåller information om hur du använder en skriptåtgärd för att läsa in bibliotek förväg när klustret skapas. Bibliotek som lagts till med hjälp av stegen i det här dokumentet är globalt tillgänglig i Hive - behöver inte använda [JAR-Lägg till](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) inte läsas in.

## <a name="how-it-works"></a>Hur det fungerar

När du skapar ett kluster kan använda du en skriptåtgärd för att ändra noder som har skapats. Skriptet i det här dokumentet accepterar en enda parameter som är platsen för bibliotek. Den här platsen måste vara i ett Azure Storage-konto och bibliotek måste lagras som jar-filer.

När klustret skapas skriptet räknar filerna, kopierar dem till den `/usr/lib/customhivelibs/` katalog på head- och arbetsroller noder läggs sedan till i den `hive.aux.jars.path` egenskap i den `core-site.xml` filen. På Linux-baserade kluster även uppdateras den `hive-env.sh` filen med platsen för filerna.

> [!NOTE]
> Med åtgärderna som skript i den här artikeln tillgängliggör biblioteken i följande scenarier:
>
> * **Linux-baserat HDInsight** – när du använder en Hive-klient **WebHCat**, och **HiveServer2**.
> * **Windows-baserade HDInsight** – när du använder Hive-klienten och **WebHCat**.

## <a name="the-script"></a>Skriptet

**Skriptets placering**

För **Linux-baserade kluster**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

För **Windows-baserade kluster**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

**Krav**

* Skripten måste tillämpas på både den **huvudnoder** och **arbetarnoder**.

* Burkar som du vill installera måste lagras i Azure Blob Storage i en **enskild behållare**.

* Storage-konto som innehåller bibliotek med jar-filer **måste** länkas till HDInsight-klustret när du skapar. Det måste antingen vara standardkontot för lagring eller ett konto som har lagts till via __valfri konfiguration__.

* WASB-sökvägen till behållaren måste anges som en parameter för skriptåtgärden. Om exempelvis burkar lagras i en behållare med namnet **libs** på ett lagringskonto med namnet **mystorage**, parametern skulle vara **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > Det här dokumentet förutsätts att du har redan skapat ett lagringskonto, blob-behållare, och överföra filer till den.
  >
  > Om du inte har skapat ett lagringskonto kan du göra det via den [Azure-portalen](https://portal.azure.com). Du kan sedan använda ett verktyg som [Azure Lagringsutforskaren](http://storageexplorer.com/) att skapa en behållare för kontot och överföra filer till den.

## <a name="create-a-cluster-using-the-script"></a>Skapa ett kluster med hjälp av skript

> [!NOTE]
> Följande steg skapar ett Linux-baserade HDInsight-kluster. Om du vill skapa ett Windows-baserade kluster, Välj **Windows** som klustret OS när du skapar klustret och Använd skript för Windows (PowerShell) i stället för bash-skript.
>
> Du kan också använda Azure PowerShell eller HDInsight .NET SDK för att skapa ett kluster med det här skriptet. Mer information om hur du använder metoderna finns [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

1. Börja etablera ett kluster med hjälp av stegen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md), men inte slutföra etablering.

2. På den **valfri konfiguration** väljer **skriptåtgärder**, och ange följande information:

   * **NAMNET**: Ange ett eget namn för skriptåtgärden.

   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD**: Markera det här alternativet.

   * **WORKER**: Markera det här alternativet.

   * **ZOOKEEPER**: lämnar fältet tomt.

   * **PARAMETRARNA**: Ange WASB-adressen för kontot för behållaren och lagring som innehåller burkar. Till exempel **wasb://libs@mystorage.blob.core.windows.net/**.

3. Längst ned i den **skriptåtgärder**, använda den **Välj** för att spara konfigurationen.

4. På den **valfri konfiguration** väljer **länkade Lagringskonton** och välj den **lägga till en nyckel för säkerhetslagring** länk. Välj lagringskontot som innehåller burkar. Använd sedan den **Välj** knappar för att spara inställningarna och återgå den **valfri konfiguration**.

5. Spara valfri konfiguration genom att använda den **Välj** knappen längst ned i den **valfri konfiguration** avsnitt.

6. Fortsätta etablering klustret enligt beskrivningen i [etablera HDInsight-kluster på Linux](hdinsight-hadoop-provision-linux-clusters.md).

När klustret har skapats är klar är du kan använda burkar lagts till via det här skriptet från Hive utan att använda den `ADD JAR` instruktionen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med Hive finns [använda Hive med HDInsight](hadoop/hdinsight-use-hive.md)
