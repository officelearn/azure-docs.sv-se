---
title: Vad är Oracle WebLogic Server på Azure?
description: Lär dig hur du kör Oracle WebLogic Server på Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851874"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Vad är Oracle WebLogic Server på Azure?

På den här sidan beskrivs lösningar för att köra WebLogic-Server (WLS) på Azure Virtual Machines.  Dessa lösningar har utvecklats gemensamt av Oracle och Microsoft.

Oracle WebLogic Server är världens första moln inbyggda, Enterprise Java Platform Application Server för utveckling och distribution av distribuerade företags program i flera nivåer. Med Azure WebLogic-Server kan du integrera molnbaserad data behandling.  Du får större valmöjligheter och flexibilitet för WebLogic-migrering, inklusive lyft och SKIFT för dina Java-program till Azure-molnet.   WLS på Azure ger stor påverkan på en liten ansträngning. Med erbjudandena kan du snabbt starta dina LOB-program (Line of Business).  Varje erbjudande tillhandahåller automatiskt virtuella nätverk, lagrings-och Linux-resurser.  Med noll ansträngning installeras WebLogic-servern.  WLS på Azure ställer in säkerhet med en nätverks säkerhets grupp, belastnings utjämning med Azure App Gateway, autentisering med Azure Active Directory och ansluter automatiskt till den befintliga databasen.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Du kan använda Azure Portal för att distribuera WebLogic Server på Azure":::

Det finns fyra erbjudanden som är tillgängliga för att möta olika scenarier: en nod utan en administratörs Server, en nod med en administratörs Server, ett kluster och ett dynamiskt kluster.  Testa erbjudandena, de är tillgängliga kostnads fritt.

_De här erbjudandena är en egen licens_. De förutsätter att du redan har rätt licenser med Oracle och att de är korrekt licensierade för att köra erbjudanden i Microsoft Azure.

_Om du vill arbeta nära dina migrerings scenarier med teknik teamet som utvecklar dessa erbjudanden väljer du knappen [kontakta mig](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ på [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Program chefer, arkitekter och tekniker kommer att komma ut till dig inom kort och börja samar beta!

## <a name="oracle-weblogic-server-single-node"></a>Enskild nod för Oracle WebLogic-Server

Det här erbjudandet tillhandahåller en enskild virtuell dator och installerar WLS på den. Den skapar inte en domän eller startar administrations servern. En nod är användbar för scenarier med mycket anpassad domän konfiguration.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic-Server med administratörs Server

Det här erbjudandet tillhandahåller en enskild virtuell dator och installerar WLS på den. Den skapar en domän och startar administrations servern, vilket gör att du kan hantera domänen.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-kluster

Det här erbjudandet skapar ett kluster med hög tillgänglighet för virtuella WLS-datorer. Administrations servern och alla hanterade servrar startas som standard, vilket gör att du kan hantera domänen.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamiskt kluster för Oracle WebLogic Server

Det här erbjudandet skapar ett hög tillgängligt och skalbart dynamiskt kluster av WLS Virtual Machines. Administrations servern och alla hanterade servrar startas som standard, vilket gör att du kan hantera domänen.

## <a name="next-steps"></a>Nästa steg

Utforska erbjudandena i Azure Marketplace.

> [!div class="nextstepaction"]
> [Enskild nod för Oracle WebLogic-Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic-Server med administratörs Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-kluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamiskt kluster för Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
