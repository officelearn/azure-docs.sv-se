---
title: Fel sökning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953168"
---
# <a name="personalizer-troubleshooting"></a>Fel sökning av personanpassa

Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.

## <a name="learning-loop"></a>Inlärnings slinga

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Inlärnings slingan verkar inte lära sig. Hur gör jag för att åtgärda detta?

Inlärnings slingan behöver några tusen belönings samtal innan ranknings anropen prioriteras effektivt. 

Om du är osäker på hur din inlärnings slinga för närvarande fungerar, kör du en [offline-utvärdering](concepts-offline-evaluation.md)och tillämpar den korrigerade inlärnings principen. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera modell uppdaterings frekvensen](how-to-settings.md#model-update-frequency)