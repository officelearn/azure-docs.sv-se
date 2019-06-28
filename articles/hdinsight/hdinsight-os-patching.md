---
title: Konfigurera OS-korrigering schemat för Linux-baserade HDInsight-kluster – Azure
description: Lär dig hur du konfigurerar OS-korrigering schema för Linux-baserade HDInsight-kluster.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 3fad8869a31688e9e2413abb350eccf1f871f7dd
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330704"
---
# <a name="os-patching-for-hdinsight"></a>OS-korrigering för HDInsight 

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya HDInsight-klustret skapas inom tre månader att publiceras. Från och med januari 2019 körs kluster är **inte** korrigeras automatiskt. Kunder måste använda skriptåtgärder eller andra mekanismer för att korrigera ett aktivt kluster. Nyligen skapade kluster har alltid de senaste tillgängliga uppdateringar, inklusive de senaste säkerhetsuppdateringar.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Så här konfigurerar du den OS-korrigering schema för Linux-baserade HDInsight-kluster
De virtuella datorerna i ett HDInsight-kluster måste startas om ibland så att viktiga säkerhetsuppdateringar kan installeras. 

Med skriptåtgärder som beskrivs i den här artikeln kan ändra du den OS-korrigering schemat på följande sätt:
1. Installera alla uppdateringar eller installera kernel + security endast eller installera kernel-uppdateringar.
2. Startas om direkt eller Schemalägg en omstart på den virtuella datorn.

> [!NOTE]  
> Dessa skriptåtgärder fungerar endast med Linux-baserade HDInsight-kluster som skapats efter den 1 augusti 2016. Korrigeringar träder endast när virtuella datorer startas om. Dessa skript gäller inte automatiskt uppdateringar för alla kommande uppdatering cykler. Köra skript varje gång nya uppdateringar som ska tillämpas för att installera uppdateringar och starta om den virtuella datorn.

## <a name="how-to-use-the-script"></a>Hur du använder skriptet 

Med det här skriptet kräver följande information:
1. Install-uppdateringar-schema-omstarter skriptplats: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight använder den här URI: N för att hitta och köra skriptet på alla virtuella datorer i klustret. Det här skriptet ger alternativ för att installera uppdateringar och starta om den virtuella datorn.
  
2. Schema-omstarter skriptplats: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight använder den här URI: N för att hitta och köra skriptet på alla virtuella datorer i klustret. Det här skriptet startar om den virtuella datorn.
  
3. Kluster-nodtyper som skriptet tillämpas på: huvudnoden, workernode, zookeeper. Det här skriptet måste tillämpas på varje nod i klustret. Om det inte används för en nodtyp, ska sedan de virtuella datorerna för den nodtypen inte uppdateras eller startas om.

4. Parameter: Skriptet install-uppdateringar-schema-omstarter godkänner två numeriska parametrar:

    | Parameter | Definition |
    | --- | --- |
    | Installera kernel-uppdateringar / installera alla uppdateringar/installera kernel + security endast |0 eller 1 eller 2. Värdet 0 installerar kernel-uppdateringar, samtidigt som 1 installerar alla uppdateringar, och 2 installerar kernel + security uppdaterar endast. Om ingen parameter anges är standardvärdet är 0. |
    | Ingen omstart/aktivera schema omstart/aktivera omedelbara omstart |0 eller 1 eller 2. Värdet 0 inaktiverar omstart, medan 1 aktiverar schema omstart och 2 kan startas om direkt. Om ingen parameter anges är standardvärdet är 0. Användaren måste ange parametern 1 för att ange parametern 2. |
   
 5. Parameter: Skriptet schema omstarter accepterar en parameter med numeriska:

    | Parameter | Definition |
    | --- | --- |
    | Aktivera schema omstart/aktivera omedelbara omstart |1 eller 2. Värdet 1 aktiverar schema omstart (omstart schemaläggs i nästa 12 – 24 timmar) medan 2 möjliggör omedelbar omstart (i 5 minuter). Om ingen parameter anges är standardvärdet 1. |  

> [!NOTE] 
> Du måste markera skriptet som sparas när du använder i ett befintligt kluster. Annars kommer alla nya noder som skapats genom skalningsåtgärder använder standard uppdateringsschema.  Om du använder skriptet som en del av klustret skapas, bevaras det automatiskt.


## <a name="next-steps"></a>Nästa steg

För vissa anvisningar om hur du använder skriptåtgärd, finns i följande avsnitt i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md):

* [Använda en skriptåtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Använda en skriptåtgärd till ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
