---
title: Referens för övervakning av uppgifter | Microsoft-dokument
titleSuffix: Azure Machine Learning
description: Lär dig mer om data och resurser som samlas in för Azure Machine Learning och som är tillgängliga i Azure Monitor. Azure Monitor samlar in och ytor data om din Azure Machine Learning-arbetsyta och låter dig visa mått, ställa in aviseringar och analysera loggade data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927552"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure machine learning övervakning datareferens

Lär dig mer om data och resurser som samlas in av Azure Monitor från din Azure Machine Learning-arbetsyta. Mer information om hur du samlar in och analyserar övervakningsdata finns i [Övervaka Azure Machine Learning.](monitor-azure-machine-learning.md)

## <a name="resource-logs"></a>Resursloggar

I följande tabell visas egenskaperna för Azure Machine Learning-resursloggar när de samlas in i Azure Monitor Logs eller Azure Storage.

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents tabell

| Egenskap | Beskrivning |
|:--- |:---|
| TimeGenerated | Tid när loggposten genererades |
| OperationName | Namnet på den åtgärd som är associerad med logghändelsen |
| Kategori | Namnet på logghändelsen, AmlComputeClusterNodeEvent |
| JobId | ID för det inskickade jobbet |
| ExperimentId (På) | ID för experimentet |
| ExperimentName (ExperimentName) | Experimentets namn |
| CustomerSubscriptionId | SubscriptionId där Experiment och Jobb som skickats |
| WorkspaceName (Arbetsytenamn) | Namn på arbetsytan för maskininlärning |
| ClusterName | Namnet på klustret |
| EtableringStat | Tillståndet för jobbinlämningen |
| ResourceGroupName | Resursgruppens namn |
| Jobbnamn | Jobbets namn |
| ClusterId (kluster) | ID för klustret |
| Eventtype | Typ av jobbhändelse, t.ex. |
| ExecutionState | Jobbets tillstånd (körningen), t.ex. |
| ErrorDetails | Information om jobbfel |
| CreationApiVersion | Api-version som används för att skapa jobbet |
| ClusterResourceGroupName | Resursgruppsnamnet för klustret |
| TFWorkerCount | Antal TF-arbetare |
| TFParameterServerCount | Antal TF-parameterserver |
| Verktygstyp | Typ av verktyg som används |
| RunInContainer (På Andra) | Flagga som beskriver om jobbet ska köras i en behållare |
| JobErrorMessage | detaljerat meddelande om jobbfel |
| NodeId | ID för den nod som skapats där jobbet körs |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents tabell

| Egenskap | Beskrivning |
|:--- |:--- |
| TimeGenerated | Tid när loggposten genererades |
| OperationName | Namnet på den åtgärd som är associerad med logghändelsen |
| Kategori | Namnet på logghändelsen, AmlComputeClusterNodeEvent |
| EtableringStat | Etablera tillståndet för klustret |
| ClusterName | Klustrets namn |
| Klustertyp | Typ av kluster |
| SkapadAv | Användare som skapade klustret |
| CoreCount (olikartade) | Antal kärnor i klustret |
| VmSize (VmSize) | Klustrets vm-storlek |
| VmPriority | Prioritet för de noder som skapats i ett kluster Dedikerad/LowPriority |
| ScalingType | Typ av manuell klusterskalning/automatisk |
| InitialNodeCount | Inledande nodantal för klustret |
| MinimumNodeCount | Minsta antal noder i klustret |
| Högsta Belopp | Maximalt antal noder i klustret |
| NodeDeallocationOption | Så här ska noden hanteras |
| Utgivare | Utgivare av klustertypen |
| Erbjudande | Erbjudande som klustret skapas med |
| Sku | Sku för noden/den virtuella datorn som skapats i klustret |
| Version | Version av avbildningen som används när nod/virtuell dator skapas |
| SubnetId | Undernät i klustret |
| AllocationState (fördelningsstat) | Tillstånd för klusterallokering |
| AktuelltNodeCount | Antal aktuella noder i klustret |
| MålNodeCount | Antal målnoder för klustret samtidigt som upp/ned-upp/ned-upp- och nedskalning |
| Eventtype | Typ av händelse när klustret skapas. |
| NodeIdleTimeSecondsBeforeScaleDown | Inaktiv tid i sekunder innan klustret skalas ned |
| FöregripenNodeCount | Föregripen nodantal för klustret |
| IsResizeGrow (IsResizeGrow) | Flagga som anger att klustret skalas upp |
| VmFamilyName | Namn på den virtuella datorn för noderna som kan skapas i klustret |
| Lämnanrr | Lämna nodantal för klustret |
| OanvändarbartNodeCount | Oanvändbart nodantal för klustret |
| IdleNodeCount | Antal inaktiv nod i klustret |
| KörNodeCount | Köra nodantal för klustret |
| FörberedaNodeCount | Förbereda nodantal för klustret |
| Kvoten är fördelad | Allokerad kvot till klustret |
| Kvotutnyttjad | Utnyttjad kvot för klustret |
| AllocationStateTransitionTime | Övergångstid från ett tillstånd till ett annat |
| ClusterErrorKoder | Felkod som tas emot när kluster skapas eller skalning |
| CreationApiVersion | Api-version som används när klustret skapas |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents tabell

| Egenskap | Beskrivning |
|:--- |:--- |
| TimeGenerated | Tid när loggposten genererades |
| OperationName | Namnet på den åtgärd som är associerad med logghändelsen |
| Kategori | Namnet på logghändelsen, AmlComputeClusterNodeEvent |
| ClusterName | Klustrets namn |
| NodeId | ID för den klusternod som skapats |
| VmSize (VmSize) | Den virtuella datorns storlek på noden |
| VmFamilyName | Vm-familjen som noden tillhör |
| VmPriority | Prioritet för noden som skapats Dedikerad/LowPriority |
| Utgivare | Utgivare av vm-avbildningen, t.ex. |
| Erbjudande | Erbjudande som är associerat med skapandet av virtuella datorer |
| Sku | Sku för noden/den virtuella datorn som skapats |
| Version | Version av avbildningen som används när nod/virtuell dator skapas |
| KlustercreationTime | Tid när klustret skapades |
| Ändra storlek PåStartTime | Tid när klustret skalas upp/ned startade |
| Ändra storlek PåTime | Tid när klustret skalas upp/ned slutade |
| NodeAllocationTime | Tid när noden allokerades |
| NodeBootTime | Tid när noden startades upp |
| StartTaskStartTime | Tid när aktiviteten tilldelades en nod och startades |
| StartTaskEndTime | Tid när aktiviteten som tilldelats en nod avslutades |
| TotaltE2ETimeInSeconds | Total tidsnod var aktiv |

### <a name="metrics"></a>Mått

I följande tabeller visas de plattformsmått som samlats in för Azure Machine Learning Alla mått lagras i namnområdet **Azure Machine Learning Workspace**.

**Modell**

| Mått | Enhet | Beskrivning |
| ----- | ----- | ----- |
| Det gick inte att distribuera modeller | Antal | Antalet modelldistributioner som misslyckades. |
| Modellut distribuera igång | Antal | Antalet modelldistributioner har startats. |
| Modellut distribuera lyckades | Antal | Antalet modelldistributioner som lyckades. |
| Modellregistret misslyckades | Antal | Antalet modellregistreringar som misslyckades. |
| Modellregistret lyckades | Antal | Antalet modellregistreringar som lyckades. |

**Kvot**

Kvotinformation är endast för Azure Machine Learning-beräkning.

| Mått | Enhet | Beskrivning |
| ----- | ----- | ----- |
| Aktiva kärnor | Antal | Antalet aktiva beräkningskärtor. |
| Aktiva noder | Antal | Antalet aktiva noder. |
| Inaktiva kärnor | Antal | Antalet inaktiva beräkningskärnor. |
| Inaktiva noder | Antal | Antalet inaktiva beräkningsnoder. |
| Lämna kärnor | Antal | Antalet lämnar kärnor. |
| Lämna noder | Antal | Antalet utelämnaer.ender. |
| Föregripna kärnor | Antal | Antalet föregripna kärnor. |
| Föregripna noder | Antal | Antalet föregripna noder. |
| Procentsats för kvotutnyttjande | Procent | Den procentandel av kvoten som används. |
| Totalt antal kärnor | Antal | Den totala kärnor. |
| Totalt antal noder | Antal | Den totala noderna. |
| Oersättliga kärnor | Antal | Antalet oanvändbara kärnor. |
| Oersättliga noder | Antal | Antalet oanvändbara noder. |

Följande är dimensioner som kan användas för att filtrera kvotmått:

| Dimension | Mått som är tillgängliga med | Beskrivning |
| ---- | ---- | ---- |
| Klusternamn | Alla kvotmått | Namnet på beräkningsinstansen. |
| Vm-familjenamn | Procentsats för kvotutnyttjande | Namnet på den virtuella datorn-familjen som används av klustret. |
| Vm-prioritet | Procentsats för kvotutnyttjande | Prioriteten för den virtuella datorn.

**Köra**

Information om träningskörningar.

| Mått | Enhet | Beskrivning |
| ----- | ----- | ----- |
| Slutförda körningar | Antal | Antalet slutförda körningar. |
| Misslyckade körningar | Antal | Antalet misslyckade körningar. |
| Startade körningar | Antal | Antalet startade körningar. |

Följande är dimensioner som kan användas för att filtrera körningsmått:

| Dimension | Beskrivning |
| ---- | ---- |
| ComputeType (Beräkningstyp) | Den beräkningstyp som körningen använde. |
| PipelineStepType | Den typ av [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) som används i körningen. |
| PublishedPipelineId | ID:et för den publicerade pipelinen som används i körningen. |
| RunType (olikartade) | Typ av körning. |

De giltiga värdena för RunType-dimensionen är:

| Värde | Beskrivning |
| ----- | ----- |
| Experiment | Icke-pipeline körs. |
| PipelineRun | En pipeline-körning, som är överordnad en StepRun. |
| StepRun (stegrun) | En körning för ett pipeline-steg. |
| ÅteranvändaStepRun | En körning för ett pipeline-steg som återanvänder en tidigare körning. |

## <a name="see-also"></a>Se även

- Se [Övervaka Azure Machine Learning](monitor-azure-machine-learning.md) för en beskrivning av övervakning av Azure Machine Learning.
- Mer information om hur du övervakar Azure-resurser finns i [Övervaka Azure-resurser med Azure-resurser.](/azure/azure-monitor/insights/monitor-azure-resource)
