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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122896"
---
## <a name="change-the-model-update-frequency"></a>Ändra modell uppdaterings frekvensen

I Azure Portal i gruppen personanpassa på sidan **konfiguration** ändrar du **modell uppdaterings frekvensen** till 10 sekunder. Den här korta varaktigheten kommer att träna tjänsten snabbt, så att du kan se hur de viktigaste åtgärderna ändras för varje iteration.

![Ändra modell uppdaterings frekvens](../media/settings/configure-model-update-frequency-settings.png)

När en säkerhetsslinga först instansieras finns det ingen modell eftersom det inte har skett några belönings-API-anrop att träna från. Ranknings anrop returnerar lika många sannolikheter för varje objekt. Ditt program borde fortfarande alltid rangordna innehåll med hjälp av utdata från RewardActionId.