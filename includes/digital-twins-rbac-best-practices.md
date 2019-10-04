---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1c8237ce4e8b758395567e939c1ed4bda1f297ff
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827650"
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