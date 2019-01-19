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
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: e204beea5bdf72c2ec5ebcf661d3c983a2e0e6b4
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411245"
---
# <a name="troubleshoot-rbac-in-azure"></a>Felsöka RBAC i Azure

Den här artikeln innehåller vanliga frågor och svar om rollbaserad åtkomstkontroll (RBAC), så att du vet vad som händer när du använder roller i Azure-portalen och kan felsöka problem med åtkomst till.

## <a name="problems-with-rbac-role-assignments"></a>Problem med RBAC-rolltilldelningar

- Om det inte går att lägga till en rolltilldelning eftersom den **Lägg till rolltilldelning** alternativet är inaktiverat eller eftersom du får ett behörighetsfel kan du kontrollera att du använder en roll som har den `Microsoft.Authorization/roleAssignments/*` behörighet för definitionsområdet du försöker tilldela rollen. Om du inte har den här behörigheten kan du fråga prenumerationsadministratören.
- Om du får ett behörighetsfel när du försöker skapa en resurs kan du kontrollera att du använder en roll som har behörighet att skapa resurser i det valda omfånget. Du kan behöva medverka. Om du inte har behörighet, kontrollera med systemadministratören.
- Om du får ett behörighetsfel när du försöker skapa eller uppdatera ett supportärende kan du kontrollera att du använder en roll som har den `Microsoft.Support/*` behörighet, till exempel [stöder begär deltagare](built-in-roles.md#support-request-contributor).
- Om du får ett felmeddelande om att antalet rolltilldelningar har överskridits när du försöker tilldela en roll kan du försöka att minska antalet rolltilldelningar genom att tilldela roller till grupper i stället. Azure har stöd för upp till **2000** rolltilldelningar per prenumeration.

## <a name="problems-with-custom-roles"></a>Problem med anpassade roller

- Om det inte går att uppdatera en befintlig anpassad roll kan du kontrollera om du har den `Microsoft.Authorization/roleDefinition/write` behörighet.
- Om det inte går att uppdatera en befintlig anpassad roll kan du kontrollera om en eller flera tilldelningsbara omfång har tagits bort i klienten. Den `AssignableScopes` egenskap för en anpassad roll kontroller [som kan skapa, ta bort, uppdatera eller visa den anpassade rollen](custom-roles.md#who-can-create-delete-update-or-view-a-custom-role).
- Om du får ett felmeddelande som den högsta tillåtna antalet rolldefinitioner har överskridits när du försöker skapa en ny roll, ta bort eventuella anpassade roller som inte används. Du kan även försöka att konsolidera eller återanvända alla befintliga anpassade roller. Azure har stöd för upp till **2000** anpassade roller i en klient.
- Om det inte går att ta bort en anpassad roll kan du kontrollera om en eller flera rolltilldelningar fortfarande använder den anpassade rollen.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Återställa RBAC när prenumerationer flyttas mellan klienter

- Om du behöver anvisningar att överföra en prenumeration till en annan klient kan du läsa [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md).
- När du överför en prenumeration till en annan klient alla rolltilldelningar tas bort permanent från käll-klient och migreras inte till Målklienten. Du måste återskapa din rolltilldelningar i Målklienten.
- Om du är en Global Administration och du har förlorat åtkomsten till en prenumeration, använder du den **åtkomsthantering för Azure-resurser** Växla tillfälligt [utöka din behörighet](elevate-access-global-admin.md) att återfå åtkomst till den prenumeration.

## <a name="rbac-changes-are-not-being-detected"></a>RBAC ändringar identifieras inte

Azure Resource Manager cachelagrar ibland konfigurationer och att förbättra prestanda. När du skapar eller tar bort rolltilldelningar kan det ta upp till 30 minuter innan ändringarna ska börja gälla. Om du använder den Azure-portalen, Azure PowerShell eller Azure CLI kan du framtvinga en uppdatering av ändringarna för tilldelningen av rollen genom att logga och logga in. Om du gör ändringarna för tilldelningen av rollen med REST API-anrop, kan du tvinga en uppdatering genom att uppdatera ditt åtkomst-token.

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

## <a name="next-steps"></a>Nästa steg
* [Hantera åtkomst med hjälp av RBAC och Azure-portalen](role-assignments-portal.md)
* [Visa aktivitetsloggar för RBAC ändringar](change-history-report.md)

