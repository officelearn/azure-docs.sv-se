---
title: Tjänsten kvoter och begränsningar för Azure Batch AI | Microsoft Docs
description: Lär dig mer om Azure Batch AI standardkvoter, gränser och begränsningar för och hur du begär kvot ökar
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1f9bd8503c2e8f2aa3c54d41b8585c6fbf81c92b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542093"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Batch AI-tjänsten kvoter och begränsningar

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Som med andra Azure-tjänster, det finns begränsningar för vissa resurser som är associerade med Batch AI-tjänsten. I Batch AI dessa begränsningar är standardkvoter som tillämpas på prenumerationsnivån för varje region där tjänsten är [tillgängliga](https://azure.microsoft.com/global-infrastructure/services/). Den här artikeln beskrivs de standardinställningarna och hur du kan begära en kvot ökar.

Tänk på dessa kvoter du design- och skala upp dina Batch AI-resurser. Exempelvis om klustret inte når målantalet noder som du angav, kanske sedan du har nått en gräns för Batch AI-kärnor för prenumerationen.

Om du planerar att köra produktionsarbetsbelastningar i Batch AI kan du behöva öka en eller flera av kvoter ovanför standardvärdet.

> [!NOTE]
> En kvot är en kreditgräns, inte en garanti för kapacitet. Kontakta Azure-supporten om du har storskaliga kapacitetsbehoven.
> 
> 

## <a name="resource-quotas"></a>Resurskvoter

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>Andra gränser

Följande är strikt gränser som inte kan överskridas når en gång.

| **Resurs** | **Övre gräns** |
| --- | --- |
| Maximal arbetsytor per resursgrupp | 800 |
| Maximal klusterstorlek | 100 noder |
| Den maximala GPU MPI bearbetar per nod | 1 – 4 |
| Den maximala GPU arbetare per nod | 1 – 4 |
| Maximal jobbet livslängd | 7 dagar<sup>1</sup> |
| Maximal parametern servrar per nod | 1 |

<sup>1</sup> högsta livstid som refererar till tid som ett jobb börjar köras och när den är klar. Slutförda jobb kvar på obestämd tid; data för jobb som inte slutförts inom den maximala livstiden är inte tillgänglig.

## <a name="view-batch-ai-quotas"></a>Visa kvoter för Batch AI

Visa din aktuella prenumeration-kvoter för Batch AI i den [Azure-portalen][portal].

1. På den vänstra rutan klickar du på **alla tjänster**. Sök sedan efter **Batch AI** och klicka för att öppna tjänsten.
2. Klicka på **användning + kvoter** på Batch AI-menyn.
3. Välj din prenumeration för att visa kvotgränserna.

## <a name="increase-a-batch-ai-cores-quota"></a>Öka en kvot för kärnor av Batch AI

Följ dessa steg för att begära en kvot öka för dina Batch AI-prenumerationen med den [Azure-portalen][portal]. 

1. På den vänstra rutan klickar du på **alla tjänster**. Sök sedan efter **Batch AI** och klicka för att öppna tjänsten.
2. Klicka på **ny supportbegäran** på Batch AI-menyn.
3. I **grunderna**:
   
    a. **Typ av problem** > **kvot**
   
    b. **Prenumeration** > Välj din prenumeration.
   
    c. **Typ av kvot** > **Batch AI**
   
    d. **Supportplan** > Välj ditt supportavtal.

    Klicka på **Nästa**.
4. I **problemet**:
   
    a. Välj en **allvarlighetsgrad** enligt din [inverkan på företaget][support_sev].
   
    b. I **kvotinformation**, ange plats, typ av kvot och resurstypen. Ange den nya gränsen som du vill begära. Klicka på **spara och fortsätt**.

    c. Valfritt – ladda upp alla relevanta filer med mer information om orsaken till ökning.
   
    Klicka på **Nästa**.
5. I **kontaktinformation**:
   
    a. Välj en **prioriterade kontaktmetod**.
   
    b. Verifiera och ange nödvändiga kontaktinformation.
   
    Klicka på **skapa** för att skicka in supportbegäran.

När du har skickat din supportbegäran, Azure-supporten kommer att kontakta dig. Du har slutfört begäran kan ta upp till 2 arbetsdagar.


## <a name="next-steps"></a>Nästa steg

När du bekantar dig med kvotgränserna, ta en titt i följande artiklar för att komma igång med Batch AI.

> [!div class="nextstepaction"]
> [Batch AI Snabbstartsguide](quickstart-tensorflow-training-cli.md)
> [Batch AI-recept](https://github.com/Azure/BatchAI/tree/master/recipes)
> [mer information om Batch AI-resurser](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: https://aka.ms/supportseverity