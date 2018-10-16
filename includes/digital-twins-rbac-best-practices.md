---
title: ta med fil
description: ta med fil
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324173"
---
Rollbaserad åtkomstkontroll är en händelsedriven arv Säkerhetsstrategin för att hantera åtkomst, behörigheter och roller. Underordnade roller ärver behörigheter från överordnad roller. Behörigheter kan också tilldelas utan att ärvas från en överordnad-roll. De kan också tilldelas för att anpassa en roll efter behov.

Till exempel en **utrymme administratör** behöva global åtkomst för att köra alla åtgärder för en angiven utrymme (inklusive alla noder under eller i det), medan en **enheten Installer** kanske bara behöver *läsa* och *uppdatera* behörigheter för enheter och sensorer.

I samtliga fall roller ska beviljas **exakt och inte mer än den åtkomst som krävs** att utföra sina uppgifter per den **principen för minsta privilegium**, som ger en identitet *endast*:

* Mängden åtkomst som krävs för att slutföra sin jobbet.
* En roll lämplig och begränsade till att utföra sitt jobb.

>[!IMPORTANT]
> **Alltid följa principen för lägsta behörighet**.

Två andra viktiga Role-Based Access Control praxis:

> [!div class="checklist"]
> * Granska regelbundet rolltilldelningar för att kontrollera att varje roll har rätt behörigheter.
> * Roller och tilldelningar bör vara rensade som enskilda användare ändra roller eller tilldelningar.