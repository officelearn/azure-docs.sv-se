---
title: Konfigurera OS-korrigering schemat för Linux-baserade HDInsight-kluster – Azure
description: Lär dig hur du konfigurerar OS-korrigering schema för Linux-baserade HDInsight-kluster.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 5b8ed75863087e077d483c792ac4134a0c3e1eb0
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203639"
---
# <a name="os-patching-for-hdinsight"></a>OS-korrigering för HDInsight 

> [!IMPORTANT]
> Ubuntu-avbildningar blir tillgängliga för nya HDInsight-klustret skapas inom 3 månader att publiceras. Från och med januari 2019 körs kluster är **inte** korrigeras automatiskt. Kunder måste använda skriptåtgärder eller andra mekanismer för att korrigera ett aktivt kluster. Nyligen skapade kluster har alltid de senaste tillgängliga uppdateringar, inklusive de senaste säkerhetsuppdateringar.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Så här konfigurerar du den OS-korrigering schema för Linux-baserade HDInsight-kluster
De virtuella datorerna i ett HDInsight-kluster måste startas om ibland så att viktiga säkerhetsuppdateringar kan installeras. 

Med skriptåtgärd som beskrivs i den här artikeln kan ändra du den OS-korrigering schemat på följande sätt:
1. Installera fullständiga uppdateringar av Operativsystemet eller installera enbart säkerhetsuppdateringar
2. Starta om den virtuella datorn

> [!NOTE]  
> Den här skriptåtgärden fungerar endast med Linux-baserade HDInsight-kluster som skapats efter den 1 augusti 2016. Korrigeringar träder endast när virtuella datorer startas om. Det här skriptet gäller inte automatiskt uppdateringar för alla kommande uppdatering cykler. Kör skriptet varje gång nya uppdateringar som ska tillämpas för att installera uppdateringar och starta om den virtuella datorn.

## <a name="how-to-use-the-script"></a>Hur du använder skriptet 

När du använder det här skriptet kräver följande information:
1. Skriptets placering: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  HDInsight använder den här URI: N för att hitta och köra skriptet på alla virtuella datorer i klustret.
  
2. Kluster-nodtyper som skriptet tillämpas på: huvudnoden, workernode, zookeeper. Det här skriptet måste tillämpas på varje nod i klustret. Om det inte används för en nodtyp, kommer de virtuella datorerna för den nodtypen inte uppdateras.


3.  Parameter: Det här skriptet accepterar en parameter med numeriska:

    | Parameter | Definition |
    | --- | --- |
    | Installera hela OS uppdateringar/installera enbart säkerhetsuppdateringar |0 eller 1. Värdet 0 installerar säkerhetsuppdateringar endast medan 1 installerar fullständiga uppdateringar av Operativsystemet. Om ingen parameter anges standardvärdet är 0. |

> [!NOTE]  
> Du måste markera det här skriptet som sparas när du använder i ett befintligt kluster. Annars kommer alla nya noder som skapats genom skalningsåtgärder använder standard uppdateringsschema.  Om du använder skriptet som en del av klustret skapas, bevaras det automatiskt.

## <a name="next-steps"></a>Nästa steg

För vissa anvisningar om hur du använder skriptåtgärd, finns i följande avsnitt i den [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md):

* [Använda en skriptåtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Använda en skriptåtgärd till ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
