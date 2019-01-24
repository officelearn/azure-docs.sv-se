---
title: Konfigurera OS-korrigering schemat för Linux-baserade HDInsight-kluster – Azure
description: Lär dig hur du konfigurerar OS-korrigering schema för Linux-baserade HDInsight-kluster.
services: hdinsight
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 0f8d6290e779872d73b0cf7a9d0d820d2ea5e007
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848163"
---
# <a name="os-patching-for-hdinsight"></a>OS-korrigering för HDInsight 

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya HDInsight-klustret skapas inom 3 månader att publiceras. Från och med januari 2019 körs kluster är **inte** korrigeras automatiskt. Kunder måste använda skriptåtgärder eller andra mekanismer för att korrigera ett aktivt kluster.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Så här konfigurerar du den OS-korrigering schema för Linux-baserade HDInsight-kluster
De virtuella datorerna i ett HDInsight-kluster måste startas om ibland så att viktiga säkerhetsuppdateringar kan installeras. Från och med 1 augusti 2016 kommer nya Linux-baserade HDInsight-kluster (version 3.4 och senare,) startas om med hjälp av följande schema:

1. En virtuell dator i klustret kan bara starta om för korrigeringar högst en gång inom en period på 30 dagar.
2. Omstarten sker från 12: 00 UTC.
3. Starta om processen ut för virtuella datorer i klustret, så att klustret är fortfarande tillgängligt under omstart.
4. Den första omstarten för ett nyskapat kluster sker inte tidigare än 30 dagar efter skapandedatum för klustret.

Med skriptåtgärd som beskrivs i den här artikeln kan ändra du den OS-korrigering schemat på följande sätt:
1. Aktivera eller inaktivera automatiska omstarter
2. Ange hur ofta startas om (dagar mellan olika omstarter)
3. Ange vilken dag i veckan en omstart

> [!NOTE]  
> Den här skriptåtgärden fungerar endast med Linux-baserade HDInsight-kluster som skapats efter den 1 augusti 2016. Korrigeringar träder endast när virtuella datorer startas om. 

## <a name="how-to-use-the-script"></a>Hur du använder skriptet 

När du använder det här skriptet kräver följande information:
1. Skriptets placering: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight använder den här URI: N för att hitta och köra skriptet på alla virtuella datorer i klustret.
  
2. Kluster-nodtyper som skriptet tillämpas på: huvudnoden, workernode, zookeeper. Det här skriptet måste tillämpas på varje nod i klustret. Om det inte används för en nodtyp, fortsätter de virtuella datorerna för den nodtypen att använda föregående uppdateringsschemat.


3.  Parameter: Det här skriptet tar tre numeriska parametrar:

    | Parameter | Definition |
    | --- | --- |
    | Aktivera/inaktivera automatiska omstarter |0 eller 1. Värdet 0 inaktiverar automatiska omstarter när 1 aktiverar automatiska omstarter. |
    | Frekvens |7 till 90 (inklusivt). Antal dagar som ska gå innan omstartar de virtuella datorerna för korrigeringar som kräver en omstart. |
    | Dag i veckan |1-7 (inklusivt). Värdet 1 visar omstarten sker på en måndag och 7 visar ett exempel på Sunday.For finns med parametrar av 1 60 2 resulterar i automatiskt startar om var 60: e dag (högst) tisdagen. |
    | Persistence |När du använder en skriptåtgärd i ett befintligt kluster, kan du markera skriptet som sparas. Bestående skript att tillämpas när nya workernodes läggs till i klustret via skalningsåtgärder. |

> [!NOTE]  
> Du måste markera det här skriptet som sparas när du använder i ett befintligt kluster. Annars kommer alla nya noder som skapats genom skalningsåtgärder använder standard uppdateringsschema.  Om du använder skriptet som en del av klustret skapas, bevaras det automatiskt.

## <a name="next-steps"></a>Nästa steg

För vissa anvisningar om hur du använder skriptåtgärd, finns i följande avsnitt i den [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md):

* [Använda en skriptåtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Använda en skriptåtgärd till ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
