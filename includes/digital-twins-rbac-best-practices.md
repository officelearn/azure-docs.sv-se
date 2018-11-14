---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 11/13/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 42aa275e692e4e2e9b7ca38825c828c1f56247fb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628217"
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