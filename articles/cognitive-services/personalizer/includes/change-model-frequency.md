---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122896"
---
## <a name="change-the-model-update-frequency"></a>Ändra modellens uppdateringsfrekvens

I Azure-portalen, i Personalizer-resursen på sidan **Konfiguration,** ändrar du **uppdateringsfrekvensen för modell** till 10 sekunder. Denna korta varaktighet kommer att träna tjänsten snabbt, så att du kan se hur den översta åtgärden ändras för varje iteration.

![Ändra uppdateringsfrekvens för modell](../media/settings/configure-model-update-frequency-settings.png)

När en Personalizer-loop först instansieras finns det ingen modell eftersom det inte har förekommit några belönings-API-anrop att träna från. Rank-anrop returnerar lika många sannolikheter för varje objekt. Ditt program bör fortfarande alltid rangordna innehåll med hjälp av utdata från RewardActionId.