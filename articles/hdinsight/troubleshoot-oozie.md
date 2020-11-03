---
title: Felsöka Apache Oozie i Azure HDInsight
description: Felsök vissa Apache Oozie-fel i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: edbe5274de8576fccb29e1e69d260a6531d4ab05
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287403"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Felsöka Apache Oozie i Azure HDInsight

Med Apache Oozie-ANVÄNDARGRÄNSSNITTET kan du Visa Oozie-loggar. Oozie-ANVÄNDARGRÄNSSNITTET innehåller också länkar till JobTracker-loggarna för de MapReduce-uppgifter som startades av arbets flödet. Mönstret för fel sökning bör vara:

1. Visa jobbet i Oozie-webbgränssnittet.

2. Om det uppstår ett fel eller fel för en speciell åtgärd väljer du åtgärden för att se om **fel meddelande** fältet innehåller mer information om felet.

3. Om det är tillgängligt använder du URL: en från åtgärden för att visa mer information, till exempel JobTracker-loggar, för åtgärden.

Följande är de fel som du kan komma att komma åt och hur du kan lösa dem.

## <a name="ja009-cant-initialize-cluster"></a>JA009: kan inte initiera kluster

### <a name="issue"></a>Problem

Jobbets status ändras till **inaktive** rad. Information för jobbet visar `RunHiveScript` status som **START_MANUAL**. När du väljer åtgärden visas följande fel meddelande:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Orsak

De Azure Blob Storage-adresser som används i **job.xml** -filen innehåller inte lagrings containern eller lagrings konto namnet. Blob Storage-formatet måste vara `wasbs://containername@storageaccountname.blob.core.windows.net` .

### <a name="resolution"></a>Lösning

Ändra de Blob Storage-adresser som används av jobbet.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie tillåts inte personifiera &lt; användare&gt;

### <a name="issue"></a>Problem

Jobbets status ändras till **inaktive** rad. Information för jobbet visar `RunHiveScript` status som **START_MANUAL**. Om du väljer åtgärden visas följande fel meddelande:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Orsak

De aktuella behörighets inställningarna tillåter inte att Oozie personifierar det angivna användar kontot.

### <a name="resolution"></a>Lösning

Oozie kan personifiera användare i **`users`** gruppen. Använd `groups USERNAME` för att se de grupper som användar kontot är medlem i. Om användaren inte är medlem i **`users`** gruppen använder du följande kommando för att lägga till användaren i gruppen:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Det kan ta flera minuter innan HDInsight känner av att användaren har lagts till i gruppen.

---

## <a name="launcher-error-sqoop"></a>Start fel (Sqoop)

### <a name="issue"></a>Problem

Jobbets status ändras till **avlivat**. Information för jobbet visar `RunSqoopExport` status som **fel**. Om du väljer åtgärden visas följande fel meddelande:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Orsak

Sqoop kan inte läsa in den databas driv rutin som krävs för att komma åt databasen.

### <a name="resolution"></a>Lösning

När du använder Sqoop från ett Oozie-jobb måste du inkludera databas driv rutinen med de andra resurserna, till exempel den workflow.xml, som används av jobbet. Referera också till arkivet som innehåller databas driv rutinen från `<sqoop>...</sqoop>` avsnittet i workflow.xml.

För till exempel att jobb exemplet [använder Hadoop Oozie-arbetsflöden](hdinsight-use-oozie-linux-mac.md)använder du följande steg:

1. Kopiera `mssql-jdbc-7.0.0.jre8.jar` filen till katalogen **/tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Ändra `workflow.xml` för att lägga till följande XML på en ny rad ovan `</sqoop>` :

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]