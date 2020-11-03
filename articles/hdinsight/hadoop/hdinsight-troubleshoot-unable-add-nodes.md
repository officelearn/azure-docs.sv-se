---
title: Det går inte att lägga till noder i Azure HDInsight-kluster
description: Felsök varför det inte går att lägga till noder i Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289051"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: det går inte att lägga till noder i Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att lägga till noder i Azure HDInsight-klustret.

## <a name="cause"></a>Orsak

Anledningen kan variera.

## <a name="resolution"></a>Lösning

Använd funktionen [kluster storlek](../hdinsight-scaling-best-practices.md) och beräkna antalet ytterligare kärnor som krävs för klustret. Detta baseras på det totala antalet kärnor i de nya arbetsnoderna. Prova sedan ett eller flera av följande steg:

* Kontrol lera om det finns några tillgängliga kärnor på klustrets plats.

* Ta en titt på hur många tillgängliga kärnor som finns på andra platser. Överväg att återskapa ditt kluster på en annan plats med tillräckligt många tillgängliga kärnor.

* Om du vill öka kärnkvoten för en specifik plats skapar du ett supportärende för en HDInsight-kärnkvotökning.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]