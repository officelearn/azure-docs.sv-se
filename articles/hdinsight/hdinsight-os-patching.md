---
title: Konfigurera korrigeringsschema för operativsystemet för Azure HDInsight-kluster
description: Lär dig hur du konfigurerar os-korrigeringsschema för Linux-baserade HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206868"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurera os-korrigeringsschemat för Linux-baserade HDInsight-kluster

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya Azure HDInsight-kluster som skapas inom tre månader efter att de publicerats. Från och med januari 2019 är kluster som körs inte automatiskt korrigerade. Kunder måste använda skriptåtgärder eller andra mekanismer för att korrigera ett kluster som körs. Nyskapade kluster kommer alltid att ha de senaste tillgängliga uppdateringarna, inklusive de senaste säkerhetskorrigeringarna.

HDInsight ger stöd för dig att utföra vanliga uppgifter i klustret, till exempel installera OS-korrigeringar, säkerhetsuppdateringar och starta om noder. Dessa uppgifter utförs med hjälp av följande två skript som kan köras som [skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)och konfigureras med parametrar:

- `schedule-reboots.sh`- Gör en omedelbar omstart eller schemalägg en omstart på klusternoderna.
- `install-updates-schedule-reboots.sh`- Installera alla uppdateringar, endast kernel + säkerhetsuppdateringar, eller endast kärnuppdateringar.

> [!NOTE]  
> Skriptåtgärder tillämpas inte automatiskt för alla framtida uppdateringscykler. Kör skripten varje gång nya uppdateringar måste tillämpas för att installera uppdateringarna och starta sedan om den virtuella datorn.

## <a name="preparation"></a>Förberedelse

Korrigering på en representativ icke-produktionsmiljö innan du distribuerar till produktion. Utveckla en plan för att korrekt testa ditt system innan din faktiska korrigering.

Från tid till annan, från en ssh-session med klustret, kan du få ett meddelande om att en uppgradering är tillgänglig. Meddelandet kan se ut ungefär som:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

Korrigering är valfri och efter eget gottfinnande.

## <a name="restart-nodes"></a>Starta om noder
  
Skriptet [schema-omstarter](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), anger vilken typ av omstart som ska utföras på datorerna i klustret. När du skickar skriptåtgärden anger du att den ska tillämpas på alla tre nodtyperna: huvudnod, arbetsnod och zookeeper. Om skriptet inte tillämpas på en nodtyp uppdateras eller startas inte de virtuella datorerna för den nodtypen.

Accepterar `schedule-reboots script` en numerisk parameter:

| Parameter | Godkända värden | Definition |
| --- | --- | --- |
| Typ av omstart som ska utföras | 1 eller 2 | Värdet 1 aktiverar omstart av schemat (schemalagt inom 12-24 timmar). Värdet 2 gör det möjligt att omedelbart starta om (på 5 minuter). Om ingen parameter anges är standardvärdet 1. |  

## <a name="install-updates-and-restart-nodes"></a>Installera uppdateringar och starta om noder

Skriptet [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) innehåller alternativ för att installera olika typer av uppdateringar och starta om den virtuella datorn.

Skriptet `install-updates-schedule-reboots` accepterar två numeriska parametrar enligt beskrivningen i följande tabell:

| Parameter | Godkända värden | Definition |
| --- | --- | --- |
| Typ av uppdateringar som ska installeras | 0, 1 eller 2 | Värdet 0 installerar endast kernel-uppdateringar. Värdet 1 installerar alla uppdateringar och 2 installerar endast kernel + säkerhetsuppdateringar. Om ingen parameter anges är standardvärdet 0. |
| Typ av omstart som ska utföras | 0, 1 eller 2 | Värdet 0 inaktiverar omstart. Värdet 1 aktiverar omstart av schemat och 2 möjliggör omedelbar omstart. Om ingen parameter anges är standardvärdet 0. Användaren måste ändra indataparameter 1 till indataparameter 2. |

> [!NOTE]
> Du måste markera ett skript som behärsat när du har tillämpat det på ett befintligt kluster. Annars använder alla nya noder som skapas genom skalningsåtgärder standardkorrigeringsschemat. Om du använder skriptet som en del av processen för att skapa kluster sparas det automatiskt.

## <a name="next-steps"></a>Nästa steg

Specifika steg för hur du använder skriptåtgärder finns i följande avsnitt i [Anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärd:](hdinsight-hadoop-customize-cluster-linux.md)

- [Använda en skriptåtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Använda en skriptåtgärd på ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
