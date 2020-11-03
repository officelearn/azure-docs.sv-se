---
title: Apache Spark strömmande program stoppas efter 24 dagar i Azure HDInsight
description: Ett Apache Spark streaming-program stoppas efter att det har körts i 24 dagar och det finns inga fel i loggfilerna.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288032"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: Apache Spark strömnings program stoppas efter att ha körts i 24 dagar i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett Apache Spark streaming-program stoppas efter att det har körts i 24 dagar och det finns inga fel i loggfilerna.

## <a name="cause"></a>Orsak

`livy.server.session.timeout`Värdet styr hur lång tid Apache livy ska vänta tills en session har slutförts. När sessionens längd når `session.timeout` värdet avslutas livy-sessionen och programmet automatiskt.

## <a name="resolution"></a>Lösning

För tids krävande jobb ökar du värdet för `livy.server.session.timeout` att använda Ambari-användargränssnittet. Du kan komma åt livy-konfigurationen från Ambari-gränssnittet med hjälp av URL: en `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Ersätt `<yourclustername>` med namnet på ditt HDInsight-kluster på det sätt som visas i portalen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]