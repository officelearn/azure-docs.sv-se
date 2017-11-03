---
title: "Konfigurera OS-korrigering schema för Linux-baserat HDInsight - kluster i Azure | Microsoft Docs"
description: "Lär dig hur du konfigurerar OS-korrigering schema för Linux-baserade HDInsight-kluster."
services: hdinsight
documentationcenter: 
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: af3c5a19ae8e2e606e4b0506f9f6dddb41192e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="os-patching-for-hdinsight"></a>OS-korrigering för HDInsight 
Som en hanterad tjänst i Hadoop hand HDInsight tar om uppdatering av Operativsystemet för de underliggande virtuella datorer som används av HDInsight-kluster. Från och med 1 augusti 2016 har vi ändrat gästen OS uppdatering principen för Linux-baserade HDInsight-kluster (version 3.4 eller högre). Syftet med den nya principen är att avsevärt minska antalet omstarter på grund av korrigering. Den nya principen kommer att fortsätta korrigering virtuella maskiner (VMs) på Linux-kluster varje måndag eller torsdag som börjar vid 12: 00 UTC successiva överskrids över noderna i alla kluster. Alla angivna VM startas dock endast högst en gång var 30 dagar på grund av gäst OS-korrigering. Dessutom kan sker den första omstarten för ett nyskapat kluster inte tidigare än 30 dagar från datumet då klustret skapades. Korrigeringsfiler börjar gälla när de virtuella datorerna startas om.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Så här konfigurerar du OS-korrigering schema för Linux-baserade HDInsight-kluster
De virtuella datorerna i ett HDInsight-kluster måste startas om ibland så att viktiga säkerhetsuppdateringar kan installeras. Från och med 1 augusti 2016 kan nya Linux-baserat HDInsight-kluster (version 3.4 eller nyare) startas om med hjälp av följande schema:

1. En virtuell dator i klustret kan endast startas korrigeringsfiler som mest en gång i en 30-dagarsperiod.
2. Omstarten inträffar början från 12: 00 UTC.
3. Omstart processen sprids över virtuella datorer i klustret, så att klustret är fortfarande tillgängliga under omstart.
4. Den första omstarten för ett nyskapat kluster sker inte tidigare än 30 dagar efter datumet då klustret skapades.

Med skriptåtgärder som beskrivs i den här artikeln, kan du ändra OS korrigering schemat på följande sätt:
1. Aktivera eller inaktivera automatiska omstarter
2. Ange hur ofta startas om (dagar mellan olika omstarter)
3. Ange vilken dag i veckan när en omstart

> [!NOTE]
> Den här skriptåtgärden fungerar bara med Linux-baserat HDInsight-kluster som skapas efter den 1 augusti 2016. Korrigeringsfiler börjar gälla när virtuella datorer startas om. 
>

## <a name="how-to-use-the-script"></a>Hur du använder skriptet 

När det här kräver följande information:
1. Skriptets placering: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight använder den här URI: N för att hitta och kör skriptet på alla virtuella datorer i klustret.
  
2. Kluster-nodtyper som skriptet tillämpas: headnode, workernode, zookeeper. Det här skriptet måste tillämpas på varje nod i klustret. Om den inte används för typen, kommer de virtuella datorerna för att nodtypen fortsätta att använda det tidigare schemat för uppdatering.


3.  Parameter: Det här skriptet tar tre numeriska parametrar:

    | Parameter | Definition |
    | --- | --- |
    | Aktivera/inaktivera automatiska omstarter |0 eller 1. Värdet 0 inaktiverar automatiska omstarter när 1 aktiverar automatiska omstarter. |
    | frekvens |7 till 90 (sammanlagt). Antal dagar som ska vänta innan den startas om de virtuella datorerna för korrigeringsprogram som kräver en omstart. |
    | Dag i veckan |1-7 (sammanlagt). Värdet 1 anger att omstart ska ske på en måndag och 7 visar en Sunday.For exempelvis med parametrar 1 60 2 leder till att automatiskt startar om var 60: e dag (mest) tisdagen. |
    | Beständiga |När du använder en skriptåtgärd till ett befintligt kluster, kan du markera skriptet som bestående. Beständiga skript används när nya workernodes läggs till i klustret via skalning åtgärder. |

> [!NOTE]
> Du måste markera det här skriptet som beständiga när tillämpas på ett befintligt kluster. Annars kommer alla nya noder som skapats via skalning operations använder standard korrigering schema.
Om du använder skriptet som en del av klusterskapandeprocessen sparas det automatiskt.
>

## <a name="next-steps"></a>Nästa steg

Specifika anvisningar för att använda instruktionen skript finns i följande avsnitt i den [anpassa Linuz-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md):

* [Använd en skriptåtgärd när klustret skapas](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Tillämpa en skriptåtgärd till ett kluster som körs](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
