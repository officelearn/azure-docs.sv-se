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
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
ms.locfileid: "29528345"
---
När du distribuerar resurser till Azure kan ha enorm flexibilitet när du bestämmer vilka typer av resurser för att distribuera, var de finns och hur du ställer in. Den flexibiliteten öppnas fler alternativ än du vill tillåta i din organisation. När du planerar att distribuera resurser till Azure, kanske du undrar:

* Hur jag för att uppfylla juridiska krav för data suveränitet i vissa länder?
* Hur kan jag styra kostnader?
* Hur jag säkerställa att någon inte oavsiktligt ändrar kritiska system?
* Hur gör spåra resurskostnader och debiterar den korrekt?

Den här artikeln tar upp frågor. Mer specifikt kan du:

> [!div class="checklist"]
* Tilldela användare till roller och tilldela roller till ett scope så att användarna har behörighet att utföra åtgärder som förväntat, men inte fler åtgärder.
* Tillämpa principer som föreskriver konventioner för resurser i din prenumeration.
* Låsa resurser som är viktiga för ditt system.
* Tagga resurser så att du kan spåra dem med värden som passar din organisation.

Den här artikeln fokuserar på de uppgifter du utför för att implementera styrning. En bredare diskussion av begrepp finns [styrning i Azure](../articles/security/governance-in-azure.md). 
