---
title: Jupyter 404-fel - "Blockering av API för kors ursprung" - Azure HDInsight
description: Jupyter server 404 "Hittades inte" fel på grund av "Blockering Cross Origin API" i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894412"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenario: Jupyter server 404 "Hittades inte" fel på grund av "Blockering Cross Origin API" i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du öppnar Jupyter-tjänsten på HDInsight visas en felruta med texten "Hittades inte". Om du kontrollerar Jupyter loggar, kommer du att se ungefär så här:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Du kan också se en IP-adress i fältet "Origin" i Jupyter-loggen.

## <a name="cause"></a>Orsak

Det här felet kan orsakas av ett par saker:

- Om du har konfigurerat NSG-regler (Network Security Group) för att begränsa åtkomsten till klustret. Genom att begränsa åtkomsten med NSG-regler kan du fortfarande direkt komma åt Apache Ambari och andra tjänster som använder IP-adressen i stället för klusternamnet. Men när du öppnar Jupyter, kan du se en 404 "Hittades inte" fel.

- Om du har gett din HDInsight-gateway ett `xxx.azurehdinsight.net`annat anpassat DNS-namn än standard .

## <a name="resolution"></a>Lösning

1. Ändra jupyter.py filer på dessa två ställen:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Hitta raden som `NotebookApp.allow_origin='\"https://{2}.{3}\"'` säger: Och `NotebookApp.allow_origin='\"*\"'`ändra den till: .

1. Starta om Jupyter-tjänsten från Ambari.

1. Skriva `ps aux | grep jupyter` i kommandotolken bör visa att det gör det möjligt för alla WEBBADRESSer att ansluta till den.

Detta är en mindre säker än den inställning vi redan hade på plats. Men det antas tillgång till klustret är begränsad och att en utifrån får ansluta till klustret som vi har NSG på plats.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
