---
title: Fel sökning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Fel söknings frågor om Personanpassare hittar du i den här artikeln.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 9f4c4129217923f7fb32996f7447ed09a034f888
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955213"
---
# <a name="personalizer-troubleshooting"></a>Fel sökning av personanpassa

Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.

## <a name="learning-loop"></a>Inlärnings slinga

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Inlärnings slingan verkar inte lära sig. Hur åtgärdar jag detta?

Inlärnings slingan behöver några tusen belönings samtal innan ranknings anropen prioriteras effektivt. 

Om du är osäker på hur din inlärnings slinga för närvarande fungerar, kör du en [offline-utvärdering](concepts-offline-evaluation.md)och tillämpar den korrigerade inlärnings principen. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera modell uppdaterings frekvensen](how-to-settings.md#model-update-frequency)