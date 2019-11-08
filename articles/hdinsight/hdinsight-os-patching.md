---
title: Konfigurera operativ systemets uppdaterings schema för Azure HDInsight-kluster
description: Lär dig hur du konfigurerar operativ systemets uppdaterings schema för Linux-baserade HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748472"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurera operativ systemets uppdaterings schema för Linux-baserade HDInsight-kluster

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya Azure HDInsight-kluster inom tre månader efter att de har publicerats. Från och med januari 2019 installeras inte kluster som körs automatiskt. Kunder måste använda skript åtgärder eller andra mekanismer för att korrigera ett kluster som körs. Nya kluster kommer alltid att ha de senaste tillgängliga uppdateringarna, inklusive de senaste säkerhets korrigeringarna.

HDInsight ger stöd för att utföra vanliga uppgifter i klustret, till exempel att installera OS-korrigeringsfiler, säkerhets uppdateringar och starta om noder. Dessa uppgifter utförs med hjälp av följande två skript som kan köras som [skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)och som kon figurer ATS med parametrar:

- `schedule-reboots.sh`-gör en omedelbar omstart eller Schemalägg en omstart på klusternoderna.
- `install-updates-schedule-reboots.sh`-installera alla uppdateringar, endast kernel + säkerhets uppdateringar eller bara kernel-uppdateringar.

> [!NOTE]  
> Skript åtgärder tillämpar inte automatiskt uppdateringar för alla framtida uppdaterings cykler. Kör skripten varje gången nya uppdateringar måste tillämpas för att installera uppdateringarna och starta sedan om den virtuella datorn.

## <a name="restart-nodes"></a>Starta om noder
  
Skript [schema – omstarter](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)anger den typ av omstart som ska utföras på datorerna i klustret. När du skickar skript åtgärden ställer du in den så att den gäller för alla tre nodtyper: Head Node, Worker-nod och Zookeeper. Om skriptet inte tillämpas på en nodtyp, kommer de virtuella datorerna för den nodtypen inte att uppdateras eller startas om.

`schedule-reboots script` accepterar en numerisk parameter:

| Parameter | Godkända värden | Definition |
| --- | --- | --- |
| Typ av omstart att utföra | 1 eller 2 | Värdet 1 möjliggör omstart av schemat (schemalagt i 12-24 timmar). Värdet 2 aktiverar omedelbar omstart (om 5 minuter). Om ingen parameter anges är standardvärdet 1. |  

## <a name="install-updates-and-restart-nodes"></a>Installera uppdateringar och starta om noder

Skriptet [install-updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) innehåller alternativ för att installera olika typer av uppdateringar och starta om den virtuella datorn.

`install-updates-schedule-reboots` skriptet accepterar två numeriska parametrar, enligt beskrivningen i följande tabell:

| Parameter | Godkända värden | Definition |
| --- | --- | --- |
| Typ av uppdateringar som ska installeras | 0, 1 eller 2 | Värdet 0 installerar endast kernel-uppdateringar. Värdet 1 installerar alla uppdateringar och 2 installerar endast kernel + säkerhets uppdateringar. Om ingen parameter anges är standardvärdet 0. |
| Typ av omstart att utföra | 0, 1 eller 2 | Värdet 0 inaktiverar omstart. Värdet 1 möjliggör omstart av schemat och 2 aktiverar omedelbar omstart. Om ingen parameter anges är standardvärdet 0. Användaren måste ändra Indataparametern 1 till indataparameter 2. |

> [!NOTE]
> Du måste markera ett skript som beständigt när du har tillämpat det på ett befintligt kluster. Annars kommer alla nya noder som skapats med skalnings åtgärder att använda standard uppdaterings schemat. Om du använder skriptet som en del av processen för att skapa kluster sparas det automatiskt.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder skript åtgärder finns i följande avsnitt i [Anpassa Linux-baserade HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md):

* [Använd en skript åtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Tillämpa en skript åtgärd på ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
