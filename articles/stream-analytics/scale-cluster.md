---
title: Ändra storlek på ett Azure Stream Analytics kluster
description: Lär dig hur du skalar upp och ned storleken på ett Azure Stream Analytics-kluster.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 0763e56de6c72a36d39b17d153db6fc4d7dd821a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "90948409"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Ändra storlek på ett Azure Stream Analytics kluster

Kapaciteten för ett Stream Analytics kluster mäts i enhet för strömning (SU). Flera jobb kan köras parallellt i samma kluster så länge summan av SUs som har tilldelats till alla jobb som körs inte överskrider klustrets kapacitet.

Klustrets kapacitet kan skalas upp och ned för att matcha storleken på dina strömnings arbets belastningar. Skalning av ett kluster tar tid och frekvent skalning är inte avsett. Vi rekommenderar att du planerar och etablerar ett kluster med ett korrekt antal SUs som du planerar att använda.

## <a name="change-the-scale-of-your-cluster"></a>Ändra klustrets skala

1. Leta upp och välj ditt Stream Analytics-kluster i Azure Portal.

1. I avsnittet **Översikt** väljer du **skala**. Du kan se hur många SUs som är tilldelade till klustret. Använd väljaren för att öka eller minska SUs efter behov.

   ![skala kluster](./media/scale-cluster/scale-cluster.png)

Skalnings åtgärden påverkar inte några jobb som körs för tillfället.

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du skalar upp och ned dina Stream Analytics-kluster. Sedan kan du lära dig hur du hanterar privata slut punkter och automatisk skalning av dina jobb:

* [Hantera privata slut punkter i ett Azure Stream Analytics kluster](private-endpoints.md)
* [Hantera jobb i ett Azure Stream Analytics kluster](manage-jobs-cluster.md)
