---
title: Infrastruktur-FQDN för Azure-brandvägg
description: Lär dig mer om infrastruktur-FQDN i Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130207"
---
# <a name="infrastructure-fqdns"></a>Infrastruktur-FQDN

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. 

Följande tjänster ingår i den inbyggda regel samlingen:

- Beräkna åtkomst till lagrings plattformens avbildnings lagring (PIR)
- Managed disks status för lagrings åtkomst
- Azure-diagnostik och loggning (MDS)

## <a name="overriding"></a>Åsidosätta 

Du kan åsidosätta den här inbyggda infrastruktur regel samlingen genom att skapa en neka all program regel samling som bearbetas sist. Den bearbetas alltid före regelsamlingen för infrastrukturen. Allt som inte ingår i regelsamlingen för infrastrukturen nekas som standard.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).