---
title: Hantera kapacitet för fysiskt minne för Azure Stack | Microsoft Docs
description: Övervaka och hantera tillgängligt lagringsutrymme för Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 4a9c7221fbb549494e3ed112e2ab4e66c4b7dadd
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770643"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Hantera kapacitet för fysiskt minne för Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Du kan lägga till ytterligare minne för att öka den totala mängden tillgängliga minneskapaciteten för Azure Stack. I Azure Stack fysiska servern också kallas en *skala enhet noden*. Alla scale unit noder som är medlemmar i en enda skalningsenhet måste ha samma mängd minne.

> [!note]  
> Innan du fortsätter dokumentationen du maskinvarutillverkaren och se om en tillverkaren stöder en uppgradering av fysiskt minne. Ditt supportkontrakt för OEM-tillverkare maskinvara leverantören kan kräva att leverantören utför fysisk server rack placering och inbyggd programvara uppdaterats.

Följande flöde diagram visar den allmänna processen att lägga till minne till varje scale unit-nod.

![Lägga till minne till varje nod för skala-enhet](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Lägga till minne till en befintlig nod
I följande steg finns en översikt över processen att lägga till minne. 

> [!Warning]  
Följ inte stegen utan att referera till din OEM-tillverkarens dokumentation.

> [!Warning]  
Hela skalningsenheten måste vara avstängda som en löpande minnesuppgradering inte stöds.

1. Stoppa Azure Stack med hjälp av stegen som beskrivs i den [Start och stopp Azure Stack](azure-stack-start-and-stop.md) artikeln.
2. Uppgradera minnet på varje fysisk dator med hjälp av din tillverkarens dokumentation.
3. Starta Azure Stack med hjälp av stegen i den [Start och stopp Azure Stack](azure-stack-start-and-stop.md) artikeln.

## <a name="next-steps"></a>Nästa steg

 - Läs hur du hanterar storage-konton i Azure Stack för att hitta, återställa och frigöra lagringskapacitet baserat på affärsbehoven i [hantera lagringskonton i Azure Stack](azure-stack-manage-storage-accounts.md).
 - Läs Azure Stack-molnet operatör som övervakar och hanterar lagringskapaciteten för deras Azure Stack-distribution i [hantera lagringskapacitet för Azure Stack](azure-stack-manage-storage-shares.md). 
