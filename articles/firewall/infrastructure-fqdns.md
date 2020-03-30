---
title: Infrastruktur FQDN för Azure-brandvägg
description: Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168970"
---
# <a name="infrastructure-fqdns"></a>Infrastruktur-FQDN

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. 

Följande tjänster ingår i den inbyggda regelsamlingen:

- Beräkna åtkomst till databasen för lagringsplattformsavbildning (PIR)
- Åtkomst till lagring av hanterade diskar
- Azure Diagnostik och loggning (MDS)

## <a name="overriding"></a>Åsidosätta 

Du kan åsidosätta den här inbyggda infrastrukturregelsamlingen genom att skapa en neka all programregelsamling som bearbetas senast. Den bearbetas alltid före regelsamlingen för infrastrukturen. Allt som inte ingår i regelsamlingen för infrastrukturen nekas som standard.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).