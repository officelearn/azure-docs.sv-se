---
title: Hantera fysiskt minneskapacitet för Azure-stacken | Microsoft Docs
description: Övervaka och hantera tillgängligt lagringsutrymme för Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/13/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: b922790d51c7028c37bb5863d43e99e19790488c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Hantera fysiskt minneskapacitet för Azure-stacken

*Gäller för: Azure Stack integrerat system*

Du kan lägga till ytterligare minne för att öka den totala mängden tillgängliga minneskapaciteten för Azure-stacken. I Azure-stacken fysiska servern också kallas en *skala enhet nod*. Alla scale unit noder som är medlemmar i en enda skalningsenhet måste ha samma mängd minne.

> [!note]  
> Innan du fortsätter maskinvarudokumentationen tillverkare för att se om en minne uppgraderar tillverkaren har stöd för en fysisk en uppgradering av minnet. Ditt supportavtal för OEM-maskinvara leverantör kan kräva att leverantören utför fysisk server rack placering och firmware-uppdatering för enheten.

Följande flöde diagram visar den allmänna processen att lägga till minne i varje scale unit nod.

![Lägga till minne i varje scale unit nod](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Lägg till minne på en befintlig nod
Följande steg ger en översikt över processen att lägga till minne. 

> [!Warning]  
Följ inte stegen utan att referera till din OEM-tillverkarens dokumentation.

> [!Warning]  
Hela skalningsenheten måste stängas ner som löpande minnesuppgradering inte stöds.

1. Stoppa Azure stacken med hjälp av stegen som beskrivs i den [Start och stopp Azure Stack](azure-stack-start-and-stop.md) artikel.
2. Uppgradera minnet på varje fysisk dator med hjälp av dokumentationen för maskinvaran.
3. Starta Azure-stacken med hjälp av stegen i den [Start och stopp Azure Stack](azure-stack-start-and-stop.md) artikel.

## <a name="next-steps"></a>Nästa steg

 - Information om hur du hanterar storage-konton i Azure stackutrymme för att hitta, återställa och frigöra lagringskapacitet baserat på affärsbehoven finns [hantera storage-konton i Azure-stacken](azure-stack-manage-storage-accounts.md).
 - Information om operatorn Azure Stack molnet övervakar och hanterar lagringskapaciteten för deras Azure Stack-distribution finns [hantera lagringskapacitet för Azure-stacken](azure-stack-manage-storage-shares.md). 