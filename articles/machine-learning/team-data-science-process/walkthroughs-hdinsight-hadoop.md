---
title: "HDInsight Hadoop datavetenskap genomgång med Hive i Azure | Microsoft Docs"
description: "Exempel på Team av vetenskapliga data som beskriver genom att använda Hive på Azure HDInsight Hadoop att göra förutsägelseanalyser."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 5dbfd043f1809f407baf6aa4ca5c5272af425012
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight Hadoop datavetenskap genomgång med Hive på Azure 

Dessa genomgång använda Hive med HDInsight Hadoop-kluster för att göra förutsägelseanalyser. De följer stegen som beskrivs i Team av vetenskapliga data. En översikt över Team av vetenskapliga data, se [datavetenskap Process](overview.md). En introduktion till Azure HDInsight, se [introduktion till Azure HDInsight, Hadoop-teknikstacken och Hadoop-kluster](../../hdinsight/hdinsight-hadoop-introduction.md).

Ytterligare datavetenskap genomgångar som kör Team datavetenskap Process grupperas efter den **plattform** som de använder. Se [genomgång köra Team av vetenskapliga data](walkthroughs.md) för en specificering av de här exemplen.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Förutsäga taxi tips med Hive med HDInsight Hadoop

Den [Använd HDInsight Hadoop-kluster](hive-walkthrough.md) genomgången använder data från New York taxibilar för att förutsäga: 

- Om ett tips är betald 
- Distribution av tips belopp

Scenariot implementeras med hjälp av Hive med en [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/). Du lär dig hur du lagrar, utforska och funktionen tekniker data från en offentligt tillgängliga NYC Taxitransport resa och avgiften dataset. Du kan också använda Azure Machine Learning för att skapa och distribuera modeller.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Förutsäga annons klick med Hive med HDInsight Hadoop

Den [Använd Azure HDInsight Hadoop-kluster på en datamängd för 1 TB](hive-criteo-walkthrough.md) genomgången använder offentligt tillgängliga [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) klickar du på dataset för att förutsäga om ett tips är betald och intervallet för belopp som förväntat. Scenariot implementeras med hjälp av Hive med en [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska funktion tekniker och ned exempeldata. Azure Machine Learning används för att skapa, träna och betygsätta en binär klassificering modell förutsäga om en användare klickar på en annons. Avslutar den här genomgången visar hur du publicerar en av dessa modeller som en webbtjänst.


## <a name="next-steps"></a>Nästa steg

En beskrivning av de viktiga komponenter som utgör teamet datavetenskap Process finns [Team datavetenskap processöversikt](overview.md).

En beskrivning av livscykeln Team av vetenskapliga data som du kan använda för att strukturera datavetenskap projekt finns [Team datavetenskap Process livscykel](lifecycle.md). Livscykeln beskrivs stegen från början till slut att projekt vanligtvis följer när de utförs. 

