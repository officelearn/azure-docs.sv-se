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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: dc572353c2e27ddfbae2398f1aece56586955e26
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
<!---Loc Comment: Please, check the comment in coversation section---> 
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Hantera fysiskt minneskapacitet för Azure-stacken

*Gäller för: Azure Stack integrerat system*

Du kan lägga till ytterligare minne för att öka den totala mängden tillgängliga minneskapaciteten för Azure-stacken. I Azure-stacken fysiska servern också kallas en *skala enhet nod*. Alla scale unit noder som är medlemmar i en enda skalningsenhet måste ha samma mängd minne.

> [!note]  
> Innan du fortsätter dokumentationen du maskinvarutillverkaren och se om en tillverkaren stöder en uppgradering av fysiskt minne. Ditt supportavtal för OEM-maskinvara leverantör kan kräva att leverantören utför fysisk server rack placering och firmware-uppdatering för enheten.

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
