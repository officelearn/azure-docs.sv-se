---
title: Vad är lösningar för att köra Oracle WebLogic-Server på Azure Virtual Machines
description: Lär dig hur du kör Oracle WebLogic Server på Microsoft Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: rezar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: rezar
ms.openlocfilehash: e8f1b115f8a52b4352478f91dd5849c45bfebdc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274375"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Vilka lösningar finns för att köra Oracle WebLogic Server på Azure Virtual Machines?

Den här sidan beskriver lösningar för att köra Oracle WebLogic Server (WLS) på virtuella Azure-datorer. Dessa lösningar har utvecklats gemensamt av Oracle och Microsoft.

WLS är en ledande Java-Programserver som kör några av de viktigaste viktiga företags Java-programmen över hela världen. WLS utgör mellanvaru Stiftelsen för Oracle-programsviten. Oracle och Microsoft strävar efter att ge kunderna möjlighet att välja och flexibilitet för att köra arbets belastningar på Azure som en ledande moln plattform.

Azure WLS-lösningarna syftar till att göra det så enkelt som möjligt att lyfta och flytta dina Java EE-program till Azure Virtual Machines genom att automatisera de flesta vanliga åtgärder. Lösningarna etablerar automatiskt virtuella nätverks-, lagrings-, Java-och Linux-resurser. WebLogic-servern installeras med minimal ansträngning. Lösningarna kan konfigurera säkerhet med en nätverks säkerhets grupp, belastnings utjämning med Azure App gateway och autentisering med Azure Active Directory. Du kan också ansluta automatiskt till din befintliga databas, inklusive Azure PostgreSQL, Azure SQL och Oracle DB i Oracle-molnet eller Azure. I väg kartan för lösningarna ingår möjligheten att aktivera distribuerad loggning och distribuerad cachelagring via Oracle-samstämmighet. Microsoft och Oracle samarbetar för att möjliggöra liknande funktioner för WebLogic och Azure Kubernetes service (AKS).

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Du kan använda Azure Portal för att distribuera WebLogic-Server på Azure":::

Det finns fyra erbjudanden som är tillgängliga för att möta olika scenarier: en nod utan en administratörs Server, en nod med en administratörs Server, ett kluster och ett dynamiskt kluster. Erbjudandena är tillgängliga utan kostnad. Dessa erbjudanden beskrivs och länkas nedan.

_De här erbjudandena är en egen licens_. De förutsätter att du redan har rätt licenser med Oracle och att de är korrekt licensierade för att köra erbjudanden i Azure.

Det finns stöd för flera operativ system, Java och WLS-versioner via bas avbildningar (till exempel WebLogic Server 14 och JDK 11 på Oracle Linux 7,6). De här bas avbildningarna är också tillgängliga på Azure på egen hand. Bas avbildningarna är lämpliga för kunder som kräver komplexa, anpassade Azure-distributioner. Den aktuella uppsättningen bas avbildningar finns [här](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_Om du är intresse rad av att jobba nära dina migrerings scenarier med teknik teamet som utvecklar dessa erbjudanden väljer du knappen [kontakta mig](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ på [sidan för Marketplace erbjudande översikt](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Program chefer, arkitekter och tekniker kommer att kontakta dig inom kort och börja nära samarbete. Möjligheten att samar beta med ett migreringsjobb är kostnads fri medan erbjudandena är aktiva under utveckling.

## <a name="oracle-weblogic-server-single-node"></a>Enskild nod för Oracle WebLogic-Server

Det här erbjudandet tillhandahåller en enskild virtuell dator och installerar WLS på den. Den skapar inte en domän eller startar administrations servern. Det enda nod-erbjudandet är användbart för scenarier med mycket anpassad domän konfiguration.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic-Server med administratörs Server

Det här erbjudandet tillhandahåller en enskild virtuell dator och installerar WLS på den. Den skapar en domän och startar administrations servern. Du kan hantera domänen och komma igång med program distributioner direkt.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-kluster

Det här erbjudandet skapar ett kluster med hög tillgänglighet för virtuella WLS-datorer. Administrations servern och alla hanterade servrar startas som standard. Du kan hantera klustret och komma igång med program med hög tillgänglighet direkt.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamiskt kluster för Oracle WebLogic Server

Det här erbjudandet skapar ett hög tillgängligt och skalbart dynamiskt kluster av WLS Virtual Machines. Administrations servern och alla hanterade servrar startas som standard.

Lösningarna gör det möjligt att använda en rad olika produktions klara distributions arkitekturer med relativt enkelt. Du kan möta majoriteten av migreringsprocessen på det mest produktiva sättet genom att göra det möjligt att fokusera på affärs program utveckling.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Du kan använda Azure Portal för att distribuera WebLogic-Server på Azure":::

Utöver vad som automatiskt tillhandahålls av lösningarna har kunderna fullständig flexibilitet att anpassa sina distributioner ytterligare. Det är sannolikt ovanpå att distribuera program kunder att integrera ytterligare Azure-resurser med sina distributioner. Kunderna uppmanas att ge feedback om ytterligare förbättringar av lösningarna.

## <a name="next-steps"></a>Nästa steg

Utforska erbjudandena på Azure.

> [!div class="nextstepaction"]
> [Enskild nod för Oracle WebLogic-Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic-Server med administratörs Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-kluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamiskt kluster för Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
