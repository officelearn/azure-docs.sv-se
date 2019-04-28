---
title: Infrastruktur FQDN för Azure-brandväggen
description: Lär dig mer om infrastruktur fullständiga domännamn i Azure-brandvägg
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066335"
---
# <a name="infrastructure-fqdns"></a>Infrastruktur-FQDN

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. 

Följande tjänster ingår i den inbyggda samlingen:

- Compute åtkomst till lagring plattform bild lagringsplats (PIR)
- För hanterade diskar status lagring
- Azure-diagnostik och loggning (MDS)
- Azure Active Directory

## <a name="overriding"></a>Åsidosätta 

Du kan åsidosätta den här inbyggda infrastruktur-samlingen genom att skapa en neka alla regelsamling för program som har bearbetats senast. Den bearbetas alltid före regelsamlingen för infrastrukturen. Allt som inte ingår i regelsamlingen för infrastrukturen nekas som standard.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuera och konfigurera en brandvägg för Azure](tutorial-firewall-deploy-portal.md).