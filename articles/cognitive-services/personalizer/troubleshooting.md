---
title: Felsökning – Personalizer
titleSuffix: Azure Cognitive Services
description: Felsökning av frågor om Personalizer finns i den här artikeln.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722259"
---
# <a name="personalizer-troubleshooting"></a>Personalizer felsökning

Den här artikeln innehåller svar på vanliga frågor om Personalizer som felsökning.

## <a name="learning-loop"></a>Learning loop

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Learning loopen verkar inte mer. Hur jag för att åtgärda detta?

Learning loopen måste några tusen utmärkelse anrop innan rangordnas anrop prioritera effektivt. 

Om du är osäker på hur learning loopen för närvarande fungerar, kör en [offline utvärdering](concepts-offline-evaluation.md), och tillämpa principen korrigerad learning. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera uppdateringsfrekvensen för modellen](how-to-settings.md#model-update-frequency)