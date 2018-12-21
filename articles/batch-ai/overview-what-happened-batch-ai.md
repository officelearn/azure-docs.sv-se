---
title: Vad händer med Azure Batch AI? | Microsoft Docs
description: Läs om vad som händer med Azure Batch AI och beräkningsalternativen i Azure Machine Learning-tjänsten.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436883"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Vad händer med Azure Batch AI?

**Azure Batch AI-tjänsten tas ur bruk i mars.** De skalenliga tränings- och bedömningsfunktionerna i Batch AI är nu tillgängliga i [Azure Machine Learning Services](../machine-learning/service/overview-what-is-azure-ml.md), som blev allmänt tillgänglig den 4 december 2018.

Tillsammans med många andra funktioner för maskininlärning innehåller Azure Machine Learning-tjänsten ett molnbaserat hanterat beräkningsmål för att träna, distribuera och bedöms maskininlärningsmodeller. Den här beräkningsmålet kallas för [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Börja migrera och använda det idag](#migrate). Du kan interagera med Azure Machine Learning-tjänsten via dess [Python SDK: er](../machine-learning/service/quickstart-create-workspace-with-python.md), kommandoradsgränssnitt och [Azure Portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Supporttidslinje

| Date | Supportinformation för Batch AI-tjänsten |
| ---- |-----------------|
| 14 december&nbsp;&nbsp; 2018| Fortsätt att använda din befintliga Azure Batch AI-prenumeration som tidigare. Det går däremot inte längre att registrera **nya prenumerationer** och inga nya investeringar kommer att göras i den här tjänsten.|
| 31 mars&nbsp;&nbsp; 2019 | Efter detta datum fungerar inte längre befintliga Batch AI-prenumerationer. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Hur migrerar jag?

Att undvika avbrott i dina program och dra nytta av de senaste funktionerna ska du göra följande innan 31 mars 2019:

1. Skapa en Azure Machine Learning-tjänstarbetsyta och kom igång:
    + [Python-baserad snabbstart](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure Portal-baserad snabbstart](../machine-learning/service/quickstart-get-started.md)

1. Konfigurera en [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) för att träna modellen.

1. Uppdatera dina skript så att de använder Azure Machine Learning Compute.

## <a name="support"></a>Support

Support är tillgänglig för befintliga kunder som vill migrera till den mer omfattande [Azure Machine Learning-tjänsten](https://aka.ms/aml-docs).

Om Azure Machine Learning-tjänsten inte uppfyller dina behov där en funktion finns i Batch AI-tjänsten ska du skicka en Batch AI-supportbegäran till supportteamet så att de kan godkänna att din prenumeration får använda Batch AI tills tjänsten dras tillbaka.

## <a name="next-steps"></a>Nästa steg

+ Läs [Översikt över Azure Machine Learning-tjänsten](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurera ett beräkningsmål för modellträning](../machine-learning/service/how-to-set-up-training-targets.md) med Azure Machine Learning-tjänsten.

+ Granska [Azure-översikten](https://azure.microsoft.com/updates/) för att lära dig av andra Azure-tjänstuppdateringar.
