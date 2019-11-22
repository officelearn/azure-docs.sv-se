---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e22ca39536e8e5d216d786c9d3c473f13431859f
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307227"
---
Rollbaserad åtkomst kontroll är en arvs driven säkerhets strategi för hantering av åtkomst, behörigheter och roller. Underordnade roller ärver behörigheter från överordnade roller. Behörigheter kan även tilldelas utan att ärvas från en överordnad roll. De kan också tilldelas för att anpassa en roll vid behov.

En utrymmes administratör kan till exempel behöva global åtkomst för att köra alla åtgärder för ett visst utrymme. Åtkomsten inkluderar alla noder under eller inom utrymmet. Installations programmet för enheten kan bara behöva *läsa* och *Uppdatera* behörigheter för enheter och sensorer.

I varje fall beviljas roller *exakt och inte mer än den åtkomst som krävs* för att utföra sina uppgifter enligt principen om minsta behörighet. Enligt den här principen beviljas en identitet *endast*:

* Mängden åtkomst som krävs för att slutföra jobbet.
* En roll lämplig och begränsad för att utföra sitt jobb.

>[!IMPORTANT]
> Följ alltid principen om minsta behörighet.

Två andra viktiga rollbaserade åtkomst kontroll metoder att följa:

> [!div class="checklist"]
> * Granska roll tilldelningarna regelbundet för att kontrol lera att varje roll har rätt behörigheter.
> * Rensa roller och tilldelningar när individer ändrar roller eller tilldelningar.