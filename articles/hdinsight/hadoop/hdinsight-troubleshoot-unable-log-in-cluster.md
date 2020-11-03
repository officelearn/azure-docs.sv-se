---
title: Det går inte att logga in på Azure HDInsight-kluster
description: Felsök varför det inte går att logga in på Apache Hadoop kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289009"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenario: det går inte att logga in på Azure HDInsight-kluster

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att logga in på Azure HDInsight-kluster.

## <a name="cause"></a>Orsak

Anledningen kan variera. Tänk på att när du loggar in på kluster-eller app-instrumentpaneler använder du din "kluster inloggning" eller HTTP-autentiseringsuppgifter. När du fjärransluter använder du dina autentiseringsuppgifter för Secure Shell (SSH) eller fjärrskrivbordet.

## <a name="resolution"></a>Lösning

Prova ett eller flera av följande steg för att lösa vanliga problem.

* Försök öppna instrumentpanelen för klustret i en ny webbläsarflik i sekretessläget.

* Om du inte kan återkalla dina SSH-autentiseringsuppgifter kan du [återställa autentiseringsuppgifterna i Ambari-användargränssnittet](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]