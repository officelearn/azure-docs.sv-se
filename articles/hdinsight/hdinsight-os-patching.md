---
title: Konfigurera operativ systemets uppdaterings schema för Linux-baserade HDInsight-kluster – Azure
description: Lär dig hur du konfigurerar operativ systemets uppdaterings schema för Linux-baserade HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 06111ec35a127cf17fdcc77ff717de7a4bc7299f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076864"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurera operativ systemets uppdaterings schema för Linux-baserade HDInsight-kluster 

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya Azure HDInsight-kluster inom tre månader efter att de har publicerats. Från och med januari 2019 installeras inte kluster som körs automatiskt. Kunder måste använda skript åtgärder eller andra mekanismer för att korrigera ett kluster som körs. Nya kluster kommer alltid att ha de senaste tillgängliga uppdateringarna, inklusive de senaste säkerhets korrigeringarna.

Ibland måste du starta om de virtuella datorerna (VM) i ett HDInsight-kluster för att installera viktiga säkerhets korrigeringar.

Genom att använda skript åtgärderna som beskrivs i den här artikeln kan du ändra schemat för OS-korrigeringen på följande sätt:

1. Installera alla uppdateringar eller installera endast kernel + säkerhets uppdateringar eller kernel-uppdateringar.
2. Gör en omedelbar omstart eller Schemalägg en omstart på den virtuella datorn.

> [!NOTE]  
> Skript åtgärderna som beskrivs i den här artikeln fungerar bara med Linux-baserade HDInsight-kluster som skapats efter den 1 augusti 2016. Korrigerings program gäller endast efter omstart av virtuella datorer.
> Skript åtgärder tillämpar inte automatiskt uppdateringar för alla framtida uppdaterings cykler. Kör skripten varje gången nya uppdateringar måste tillämpas för att installera uppdateringarna och starta sedan om den virtuella datorn.

## <a name="add-information-to-the-script"></a>Lägg till information i skriptet

Följande information krävs för att använda ett skript:

- Skript platsen installera – uppdateringar – schema-omstarter: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight använder denna URI för att hitta och köra skriptet på alla virtuella datorer i klustret. Det här skriptet innehåller alternativ för att installera uppdateringar och starta om den virtuella datorn.
  
- Skriptet för att starta om skript platsen: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight använder denna URI för att hitta och köra skriptet på alla virtuella datorer i klustret. Det här skriptet startar om den virtuella datorn.
  
- De typer av klusternoder som skriptet tillämpas på är huvudnoden, workernode och Zookeeper. Tillämpa skriptet på alla nodtyper i klustret. Om skriptet inte tillämpas på en nodtyp, kommer de virtuella datorerna för den nodtypen inte att uppdateras eller startas om.

- Skriptet installera uppdateringar – schema-omstarter accepterar två numeriska parametrar:

    | Parameter | Definition |
    | --- | --- |
    | Installera endast kernel-uppdateringar/installera alla uppdateringar/installera endast kernel + säkerhets uppdateringar|0, 1 eller 2. Värdet 0 installerar endast kernel-uppdateringar. Värdet 1 installerar alla uppdateringar och 2 installerar endast kernel + säkerhets uppdateringar. Om ingen parameter anges är standardvärdet 0. |
    | Ingen omstart/aktivera schema omstart/aktivera omedelbar omstart |0, 1 eller 2. Värdet 0 inaktiverar omstart. Värdet 1 möjliggör omstart av schemat och 2 aktiverar omedelbar omstart. Om ingen parameter anges är standardvärdet 0. Användaren måste ändra Indataparametern 1 till indataparameter 2. |
   
 - Skriptet för att starta om schema accepterar en numerisk parameter:

    | Parameter | Definition |
    | --- | --- |
    | Aktivera schema omstart/aktivera omedelbar omstart |1 eller 2. Värdet 1 möjliggör omstart av schemat (schemalagt i 12-24 timmar). Värdet 2 aktiverar omedelbar omstart (om 5 minuter). Om ingen parameter anges är standardvärdet 1. |  

> [!NOTE]
> Du måste markera ett skript som beständigt när du har tillämpat det på ett befintligt kluster. Annars kommer alla nya noder som skapats med skalnings åtgärder att använda standard uppdaterings schemat. Om du använder skriptet som en del av processen för att skapa kluster sparas det automatiskt.


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder skript åtgärder finns i följande avsnitt i [Anpassa Linux-baserade HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md):

* [Använd en skript åtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Tillämpa en skript åtgärd på ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
