---
title: Om Batch AI-resurser – Azure | Microsoft Docs
description: Översikt över arbetsytor, kluster, filservrar, experiment och jobb i Batch AI-tjänsten i Microsoft Azure.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 4454448809411c6390bc1f65c33b4abe76094ded
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407753"
---
# <a name="overview-of-resources-in-batch-ai"></a>Översikt över resurser i Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

När du startar med hjälp av Batch AI-tjänsten, bör du förstå de tillgängliga Batch AI-resurserna. Som med andra Azure-tjänster du skapar Batch AI-resurser i en eller flera Azure *resursgrupper*. Skapa en eller flera Batch AI *arbetsytor* i en resursgrupp. Varje arbetsyta som innehåller en blandning av Batch AI *kluster*, *filservrar*, och *experiment*. Ett Batch AI-experiment kapslar in en grupp med *jobb*.

Följande bild visar ett exempel på resursen hierarki för Batch AI. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

I följande avsnitt går in i detalj om Batch AI-resurser.

## <a name="workspace"></a>Arbetsyta

En arbetsyta i Batch AI är en samlingar för resten av Batch AI-resurser. Arbetsytor som bidrar till att separera arbete som hör till olika grupper eller projekt. Du kan till exempel skapa en utvecklings- och en test-arbetsyta.

## <a name="cluster"></a>Kluster

Ett Batch AI-kluster innehåller beräkningsresurserna för jobb som körs. Alla noder i ett kluster har samma storlek och OS-avbildning. Batch AI har du många alternativ för att skapa kluster som är anpassade efter olika behov. Normalt kan ställa du in ett annat kluster för varje kategori av processorkraft som krävs för att slutföra ett projekt. Skala Batch AI-kluster upp eller ner baserat på begäran och budget. Mer information finns i [fungerar med Batch AI-kluster](clusters.md).

## <a name="file-server"></a>Filserver

Du kan också skapa en filserver i Batch AI kan lagra data, tränar skript, och utdataloggar. En Batch AI-filserver är en hanterad NFS för en nod, som automatiskt kan monteras på klusternoder för att tillhandahålla en enkel och central lagringsplats för jobb. Endast en filserver behövs i en arbetsyta i de flesta fall och du kan separera data för utbildningsjobb i olika kataloger. Om NFS inte är lämpligt för dina arbetsbelastningar, Batch AI har stöd för andra alternativ för lagring, inklusive [Azure Storage](use-azure-storage.md) eller anpassade lösningar, till exempel ett Gluster eller Lustre filsystem.

## <a name="experiment"></a>Experiment

Ett experiment grupperar en uppsättning relaterade jobb som du fråga och hantera tillsammans. Varje arbetsyta kan ha flera experiment, där varje experiment försöker lösa ett specifikt problem.

## <a name="job"></a>Jobb

Ett jobb är en enskild uppgift eller ett skript som måste köras, till exempel för att träna en modell med djupinlärning. Varje jobb körs ett visst skript på ett kluster på arbetsytan. (Skriptet kan lagras på en Batch AI-filserver eller andra lagringslösning.) Varje Batch AI-jobb har en framework-typ som är kopplade till den: TensorFlow, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, anpassade MPI eller anpassat. För varje framework Batch AI-tjänsten ställer in infrastrukturen som krävs och hanterar processerna som jobbet. Varje experiment kan ha flera jobb som är liknande, förutom några ändringar i olika parametrar.

## <a name="next-steps"></a>Nästa steg

* Kör din första [Batch AI-utbildningsjobb](quickstart-tensorflow-training-cli.md).

* Kolla in exemplen på [utbildningsrecept](https://github.com/Azure/BatchAI/tree/master/recipes) för olika ramverk.