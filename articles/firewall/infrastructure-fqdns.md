---
title: Infrastruktur FQDN för Azure-brandväggen
description: Lär dig mer om infrastruktur fullständiga domännamn i Azure-brandvägg
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 1369a82829b2c80768d746ba92daf2482c1fd7f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994147"
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