---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2c16e82ccf259a4cc5ae8fcf35b2dd6b5d50ee2d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740604"
---
När du distribuerar resurser till Azure får du en enorm flexibilitet för att bestämma vilka typer av resurser som ska distribueras, var de finns och hur du konfigurerar dem. Den flexibiliteten kan dock möjliggöra fler alternativ än vad du vill tillåta i din organisation. När du planerar att distribuera resurser till Azure undrar du kanske:

* Hur gör jag för att uppfylla juridiska krav för datasuveränitet i vissa länder?
* Hur begränsar jag kostnaderna?
* Hur ser jag till att ingen oavsiktligt ändrar ett kritiskt system?
* Hur gör jag för att spåra resurskostnader och fakturera dem på rätt sätt?

Den här artikeln tar upp dessa frågor. Mer specifikt kan du:

> [!div class="checklist"]
* Tilldela användare till roller och tilldela rollerna till ett omfång så att användarna har behörighet att utföra förväntade åtgärder men inte fler åtgärder.
* Tillämpa principer som föreskriver konventioner för resurser i din prenumeration.
* Låsa resurser som är viktiga för systemet.
* Tagga resurser så att du kan spåra dem med värden som är lämpliga för din organisation.

Den här artikeln handlar om de uppgifter som du utför för att implementera styrning. En bredare beskrivning av begreppen finns i [Styrning i Azure](../articles/security/governance-in-azure.md). 
