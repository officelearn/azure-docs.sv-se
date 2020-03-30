---
title: Kontot som används stöder inte http-fel i Azure HDInsight
description: I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165558"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Kontot som används stöder inte http-fel i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Följande felmeddelande tas emot:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Orsak

Det finns flera orsaker till att felmeddelandet tas emot:

* Lagringskontot har [aktiverat säker överföring](../../storage/common/storage-require-secure-transfer.md) och det felaktiga [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) används.

* Ett kluster skapades med ett lagringskonto som hade en säker överföring *inaktiverad*. Därefter aktiverades säker överföring på lagringskontot.

## <a name="resolution"></a>Lösning

Om säker överföring är aktiverad för Azure Storage eller Data `wasbs://` Lake `abfss://`Storage Gen2, skulle URI vara respektive .  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

För nya kluster använder du ett lagringskonto som redan har önskad säker överföringsinställning. Ändra inte inställningen för säker överföring för ett lagringskonto som används av ett befintligt kluster.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
