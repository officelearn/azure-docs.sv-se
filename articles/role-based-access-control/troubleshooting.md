---
title: Felsöka RBAC i Azure | Microsoft Docs
description: Felsök problem med Azure rollbaserad åtkomstkontroll (RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d1a0e46fe348bbc60a4d02a4727a9bb27cb26742
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223304"
---
# <a name="troubleshoot-rbac-in-azure"></a>Felsöka RBAC i Azure

Den här artikeln innehåller vanliga frågor och svar om rollbaserad åtkomstkontroll (RBAC), så att du vet vad som händer när du använder roller i Azure-portalen och kan felsöka problem med åtkomst till.

## <a name="web-app-features-that-require-write-access"></a>Web app-funktioner som kräver skrivbehörighet

Om du ger en användare skrivskyddad åtkomst till en enkel webbapp inaktiveras vissa funktioner som inte förväntat. Följande hanteringsfunktioner kräver **skriva** åtkomst till en webbapp (deltagare eller ägare) och inte är tillgängliga i alla skrivskyddade scenarier.

* Kommandon (till exempel start, Stopp osv.)
* Ändringar av inställningar som allmän konfiguration, inställningar, inställningar för säkerhetskopiering och inställningar för programövervakning
* Komma åt autentiseringsuppgifterna för publicering och andra hemligheter som appinställningar och anslutningssträngar
* Direktuppspelningsloggar
* Diagnostikloggar konfiguration
* Konsol (Kommandotolken)
* Aktiva och de senaste distributioner (för kontinuerlig lokal git-distribution)
* Uppskattade utgifter
* Webbtest
* Virtuellt nätverk (endast visas för en läsare om ett virtuellt nätverk har tidigare konfigurerats av en användare med skrivbehörighet).

Om du inte åtkomst till någon av dessa paneler, måste du be din administratör om deltagaråtkomst till webbappen.

## <a name="web-app-resources-that-require-write-access"></a>Web app-resurser som kräver skrivbehörighet

Webbappar är komplicerat av förekomsten av ett par olika resurser som samspel. Här är en typisk resursgrupp med några webbplatser:

![Resursgrupp för Web app](./media/troubleshooting/website-resource-model.png)

Resultatet blir att, om du ger någon åtkomst till bara webbappen, mycket av funktionen på webbplats-bladet i Azure-portalen är inaktiverad.

Dessa objekt kräver **skriva** åtkomst till den **App Service-plan** som motsvarar din webbplats:  

* Visa webbappen är prisnivå (kostnadsfri eller Standard)  
* Skalningskonfiguration (antal instanser, VM-storlek, inställningarna för automatisk skalning)  
* Kvoter (lagring, bandbredd och processor)  

Dessa objekt kräver **skriva** åtkomst till hela **resursgrupp** som innehåller din webbplats:  

* SSL-certifikat och bindningar (SSL-certifikat kan delas mellan platser i samma resursgrupp och geografiska plats)  
* Varningsregler  
* Inställningarna för automatisk skalning  
* Application Insights-komponenter  
* Webbtest  

## <a name="virtual-machine-features-that-require-write-access"></a>Funktioner för virtuella datorer som kräver skrivbehörighet

Liknar webbappar, vissa funktioner på bladet för virtuella datorn kräver skrivbehörighet till den virtuella datorn eller till andra resurser i resursgruppen.

Virtuella datorer är relaterade till domän namn, virtuella nätverk, lagringskonton och Varningsregler.

Dessa objekt kräver **skriva** åtkomst till den **VM**:

* Slutpunkter  
* IP-adresser  
* Diskar  
* Tillägg  

Dessa kräver **skriva** åtkomst till både den **VM**, och **resursgrupp** (tillsammans med domännamn) som den tillhör:  

* Tillgänglighetsuppsättning  
* Belastningsutjämnade uppsättningen  
* Varningsregler  

Fråga din administratör om du inte åtkomst till någon av dessa paneler för deltagaråtkomst till resursgruppen.

## <a name="azure-functions-and-write-access"></a>Azure Functions- och skrivåtkomst

Vissa funktioner i [Azure Functions](../azure-functions/functions-overview.md) kräver skrivbehörighet. Till exempel om en användare har tilldelats rollen Läsare, kommer de inte att kunna visa funktioner i en funktionsapp. Portalen visar **(ingen åtkomst)**.

![Ingen åtkomst för funktionsappar](./media/troubleshooting/functionapps-noaccess.png)

Användaren kan klicka på **plattformsfunktioner** fliken och klicka sedan på **alla inställningar** att visa vissa inställningar som är relaterade till en funktionsapp (liknar en webbapp), men de kan inte ändra någon av dessa inställningar.

## <a name="rbac-changes-are-not-being-detected"></a>RBAC ändringar identifieras inte

Azure Resource Manager cachelagrar ibland konfigurationer och att förbättra prestanda. När du skapar eller tar bort rolltilldelningar kan det ta upp till 30 minuter innan ändringarna ska börja gälla. Om du använder den Azure-portalen, Azure PowerShell eller Azure CLI kan du framtvinga en uppdatering av ändringarna för tilldelningen av rollen genom att logga och logga in. Om du gör ändringarna för tilldelningen av rollen med REST API-anrop, kan du tvinga en uppdatering genom att uppdatera ditt åtkomst-token.

## <a name="next-steps"></a>Nästa steg
* [Hantera åtkomst med hjälp av RBAC och Azure-portalen](role-assignments-portal.md)
* [Visa aktivitetsloggar för RBAC ändringar](change-history-report.md)

