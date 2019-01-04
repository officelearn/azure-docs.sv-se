---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/28/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: e81b8fb06240d566e46ca0b45a0910e014dee329
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53995788"
---
Rollbaserad åtkomstkontroll är en händelsedriven arv Säkerhetsstrategin för att hantera åtkomst, behörigheter och roller. Underordnade roller ärver behörigheter från överordnad roller. Behörigheter kan också tilldelas utan att ärvas från en överordnad-roll. De kan också tilldelade att anpassa en roll efter behov.

En administratör utrymme kanske till exempel behöver global åtkomst för att köra alla åtgärder för en angiven utrymme. Åtkomst innehåller alla noder under eller i området. Ett installationsprogram för enheten kanske bara behöver *läsa* och *uppdatera* behörigheter för enheter och sensorer.

I alla fall roller beviljas *exakt och inte mer än den åtkomst som krävs* att utföra sina uppgifter per princip för lägsta behörighet. En identitet beviljas enligt den här principen *endast*:

* Mängden åtkomst som krävs för att slutföra sin jobbet.
* En roll lämplig och begränsade till att utföra sitt jobb.

>[!IMPORTANT]
> Alltid följa principen för lägsta behörighet.

Två andra viktiga rollbaserad åtkomstkontroll praxis:

> [!div class="checklist"]
> * Granska regelbundet rolltilldelningar för att kontrollera att varje roll har rätt behörigheter.
> * Rensa roller och tilldelningar när enskilda användare ändrar roller eller tilldelningar.