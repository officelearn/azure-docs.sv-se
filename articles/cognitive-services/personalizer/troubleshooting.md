---
title: Fel sökning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Fel söknings frågor om Personanpassare hittar du i den här artikeln.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663688"
---
# <a name="personalizer-troubleshooting"></a>Fel sökning av personanpassa

Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.

## <a name="learning-loop"></a>Inlärnings slinga

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Inlärnings slingan verkar inte lära sig. Hur åtgärdar jag detta?

Inlärnings slingan behöver några tusen belönings samtal innan ranknings anropen prioriteras effektivt. 

Om du är osäker på hur din inlärnings slinga för närvarande fungerar, kör du en [offline-utvärdering](concepts-offline-evaluation.md)och tillämpar den korrigerade inlärnings principen. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera modell uppdaterings frekvensen](how-to-settings.md#model-update-frequency)