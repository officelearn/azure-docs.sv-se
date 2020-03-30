---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044957"
---
Rollbaserad åtkomstkontroll är en arvsdriven säkerhetsstrategi för hantering av åtkomst, behörigheter och roller. Underordnade roller ärver behörigheter från överordnade roller. Behörigheter kan också tilldelas utan att ärvas från en överordnad roll. De kan också tilldelas för att anpassa en roll efter behov.

En utrymmesadministratör kan till exempel behöva global åtkomst för att köra alla åtgärder för ett angivet utrymme. Åtkomst innehåller alla noder under eller inom utrymmet. En enhetsinstallationsanordning kan bara behöva *läs-* och *uppdateringsbehörigheter* för enheter och sensorer.

I varje enskilt fall beviljas roller *exakt och inte mer än den åtkomst som krävs* för att uppfylla sina uppgifter enligt principen om lägsta behörighet. Enligt denna princip beviljas en identitet *endast:*

* Mängden åtkomst som behövs för att slutföra sitt jobb.
* En roll som är lämplig och begränsad till att utföra sitt arbete.

>[!IMPORTANT]
> Följ alltid principen om lägsta privilegium.

Två andra viktiga rollbaserade åtkomstkontrollmetoder att följa:

> [!div class="checklist"]
> * Granska regelbundet rolltilldelningar för att kontrollera att varje roll har rätt behörighet.
> * Rensa roller och tilldelningar när individer byter roller eller uppgifter.