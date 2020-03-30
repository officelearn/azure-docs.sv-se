---
title: Analys på Azure HDInsight Hadoop med Hive - Team Data Science Process
description: Exempel på teamdatascience-processen som går igenom användningen av Hive på Azure HDInsight Hadoop för att göra prediktiva analyser.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864170"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight Hadoop datavetenskap genomgångar med Hive på Azure 

Dessa genomgångar använder Hive med ett HDInsight Hadoop-kluster för att göra prediktiva analyser. De följer stegen som beskrivs i teamdatavetenskapsprocessen. En översikt över teamdatavetenskapsprocessen finns i [Data Science Process](overview.md). En introduktion till Azure HDInsight finns i [Introduktion till Azure HDInsight, Hadoop-teknikstacken och Hadoop-kluster](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Ytterligare genomgångar av datavetenskap som kör Team Data Science Process grupperas efter den **plattform** som de använder. Se [Genomgångar som kör Team Data Science Process](walkthroughs.md) för en specificering av dessa exempel.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Förutsäg taxi tips med Hive med HDInsight Hadoop

[Genomgången Använd HDInsight Hadoop-kluster](hive-walkthrough.md) använder data från New York-taxibilar för att förutsäga: 

- Om ett tips betalas 
- Fördelningen av dricksbelopp

Scenariot implementeras med Hive med ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/). Du lär dig att lagra, utforska och funktionsingenjörsdata från en allmänt tillgänglig NYC-taxiresa och biljettprisdatauppsättning. Du använder också Azure Machine Learning för att skapa och distribuera modellerna.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Förutsäg annonsklick med Hive med HDInsight Hadoop

[Med Azure HDInsight Hadoop-kluster på en dataset-genomgång](hive-criteo-walkthrough.md) med 1 TB används en allmänt tillgänglig [Criteo-klickdatauppsättning](https://labs.criteo.com/downloads/download-terabyte-click-logs/) för att förutsäga om ett tips betalas och förväntade belopp. Scenariot implementeras med Hive med ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, funktionstekniker och nedprovsdata. Den använder Azure Machine Learning för att skapa, träna och få en binär klassificeringsmodell som förutsäger om en användare klickar på en annons. Genomgången avslutas med att visa hur du publicerar en av dessa modeller som en webbtjänst.


## <a name="next-steps"></a>Nästa steg

En diskussion om de viktigaste komponenterna som ingår i teamdatavetenskapsprocessen finns i [översikt över teamdatavetenskapsprocess](overview.md).

En diskussion om livscykeln för teamdatavetenskapsprocessen som du kan använda för att strukturera dina datavetenskapsprojekt finns i [livscykeln för Team Data Science Process](lifecycle.md). Livscykeln beskriver de steg, från början till, som projekten vanligtvis följer när de körs. 

