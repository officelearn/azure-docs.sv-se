---
title: Konfigurera den OS-korrigering schemat för Linux-baserade HDInsight-kluster – Azure
description: Lär dig hur du konfigurerar OS-korrigering schema för Linux-baserade HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657057"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurera den OS-korrigering schema för Linux-baserade HDInsight-kluster 

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya Azure HDInsight-klustret skapas inom tre månader att publiceras. Från och med januari 2019 inte körs kluster korrigeras automatiskt. Kunder måste använda skriptåtgärder eller andra mekanismer för att korrigera ett aktivt kluster. Nyligen skapade kluster har alltid de senaste tillgängliga uppdateringar, inklusive de senaste säkerhetsuppdateringar.

Ibland måste du starta om virtuella datorer (VM) i ett HDInsight-kluster för att installera viktiga säkerhetsuppdateringar.

Med hjälp av skriptåtgärder som beskrivs i den här artikeln kan ändra du den OS-korrigering schemat på följande sätt:

1. Installera alla uppdateringar eller installera bara kernel + säkerhetsuppdateringar eller kernel-uppdateringar.
2. Göra en omedelbar omstart eller schemalägga en omstart på den virtuella datorn.

> [!NOTE]  
> Skriptåtgärder som beskrivs i den här artikeln fungerar endast med Linux-baserade HDInsight-kluster som skapats efter den 1 augusti 2016. Korrigeringar är effektiva först efter att starta om virtuella datorer.
> Skriptåtgärder installera inte automatiskt uppdateringar för alla kommande uppdatering cykler. Kör skripten varje gång som nya uppdateringar måste användas för att installera uppdateringar och starta sedan om den virtuella datorn.

## <a name="add-information-to-the-script"></a>Lägg till information i skriptet

Med hjälp av ett skript kräver följande information:

- Install-uppdateringar-schema-omstarter skriptplats: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight använder den här URI: N för att hitta och köra skriptet på alla virtuella datorer i klustret. Det här skriptet ger alternativ för att installera uppdateringar och starta om den virtuella datorn.
  
- Schema-omstarter skriptplats: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight använder den här URI: N för att hitta och köra skriptet på alla virtuella datorer i klustret. Det här skriptet startar om den virtuella datorn.
  
- Kluster-nodtyper som skriptet tillämpas är huvudnoden, workernode och zookeeper. Använd skriptet på varje nod i klustret. Om skriptet inte tillämpas på en nodtyp, inte de virtuella datorerna för den nodtypen uppdateras eller startas om.

- Skriptet install-uppdateringar-schema-omstarter godkänner två numeriska parametrar:

    | Parameter | Definition |
    | --- | --- |
    | Installera kernel-uppdateringar / installera alla uppdateringar/installera kernel + security endast|0, 1 eller 2. Värdet 0 installerar kernel-uppdateringar. Värdet 1 installerar alla uppdateringar, och 2 installerar endast kernel + säkerhetsuppdateringar. Om ingen parameter anges är standardvärdet är 0. |
    | Ingen omstart/aktivera schema omstart/aktivera omedelbara omstart |0, 1 eller 2. Värdet 0 inaktiverar omstart. Värdet 1 aktiverar schema omstart och 2 kan omedelbar omstart. Om ingen parameter anges är standardvärdet är 0. Användaren måste ändra Indataparametern 1 för att ange parametern 2. |
   
 - Skriptet schema omstarter accepterar en parameter med numeriska:

    | Parameter | Definition |
    | --- | --- |
    | Aktivera schema omstart/aktivera omedelbara omstart |1 eller 2. Värdet 1 aktiverar schema omstart (schemalagd i 12 – 24 timmar). Värdet 2 aktiverar omedelbar omstart (i 5 minuter). Om ingen parameter anges är standardvärdet 1. |  

> [!NOTE]
> Du måste markera ett skript som sparas när du har installerat i ett befintligt kluster. Annars kommer alla nya noder som skapats genom skalningsåtgärder använder standard uppdateringsschema. Om du använder skriptet som en del av klustret skapas, har det sparat automatiskt.


## <a name="next-steps"></a>Nästa steg

För vissa anvisningar om hur du använder skriptåtgärder, finns i följande avsnitt i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md):

* [Använda en skriptåtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Använda en skriptåtgärd till ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
