---
title: Felsöka RBAC för Azure-resurser | Microsoft Docs
description: Felsök problem med rollbaserad åtkomstkontroll (RBAC för Azure-resurser).
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
ms.date: 04/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: c6f947ad6f2f8dba2df17132243eb6d918539c14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344435"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Felsöka RBAC för Azure-resurser

Den här artikeln innehåller vanliga frågor och svar om rollbaserad åtkomstkontroll (RBAC) för Azure-resurser, så att du vet vad som händer när du använder roller i Azure-portalen och kan felsöka problem med åtkomst till.

## <a name="problems-with-rbac-role-assignments"></a>Problem med RBAC-rolltilldelningar

- Om det inte går att lägga till en rolltilldelning i Azure portal på **åtkomstkontroll (IAM)** eftersom den **Lägg till** > **Lägg till rolltilldelning** alternativet är inaktiverat eller eftersom du får felet behörigheter ”klienten med objekt-id har inte behörighet att utföra åtgärden” kan du kontrollera att du har loggat in med en användare som har tilldelats en roll som har den `Microsoft.Authorization/roleAssignments/write` behörighet som [ägare](built-in-roles.md#owner) eller [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) i det omfånget som du vill tilldela rollen.
- Om du får felmeddelandet ”inga fler rolltilldelningar kan skapas (kod: RoleAssignmentLimitExceeded)” när du försöker tilldela en roll kan du försöka att minska antalet rolltilldelningar genom att tilldela roller till grupper i stället. Azure har stöd för upp till **2000** rolltilldelningar per prenumeration.

## <a name="problems-with-custom-roles"></a>Problem med anpassade roller

- Om du behöver anvisningar att skapa en anpassad roll kan se de anpassade rollen självstudier med [Azure PowerShell](tutorial-custom-role-powershell.md) eller [Azure CLI](tutorial-custom-role-cli.md).
- Om det inte går att uppdatera en befintlig anpassad roll kan du kontrollera att du har loggat in med en användare som har tilldelats en roll som har den `Microsoft.Authorization/roleDefinition/write` behörighet som [ägare](built-in-roles.md#owner) eller [administratör för användaråtkomst](built-in-roles.md#user-access-administrator).
- Om du inte lyckas ta bort en anpassad roll och får felmeddelandet om att det finns befintliga rolltilldelningar som refererar till rollen (kod: RoleDefinitionHasAssignments), så finns det rolltilldelningar som fortfarande använder den anpassade rollen. Ta bort dessa rolltilldelningar och försök att ta bort den anpassade rollen igen.
- Om du får felmeddelandet ”Det högsta tillåtna antalet rolldefinitioner har överskridits. Inga fler Rolldefinitioner kan skapas (kod: RoleDefinitionLimitExceeded) ”när du försöker skapa en ny anpassad roll kan du ta bort eventuella anpassade roller som inte används. Azure har stöd för upp till **2000** anpassade roller i en klient.
- Om du får ett felmeddelande liknande ”klienten har behörighet att utföra åtgärden” Microsoft.Authorization/roleDefinitions/write ”på omfånget” /subscriptions/ {subscriptionid}}, men det inte gick att hitta den länkade prenumerationen ”när du försöker uppdatera en anpassad roll Om en eller flera [tilldelningsbara omfång](role-definitions.md#assignablescopes) har tagits bort i klienten. Om omfånget har tagits bort ska du skapa en supportbegäran eftersom det inte finns någon självbetjäningslösning tillgänglig just nu.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Återställa RBAC när prenumerationer flyttas mellan klienter

- Om du behöver anvisningar att överföra en prenumeration till en annan Azure AD-klient, finns i [överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md).
- Om du överför en prenumeration till en annan Azure AD-klient tas alla rolltilldelningar bort permanent från den Azure AD-klient som är källan och migreras inte till den Azure AD-klient som är målet. Du måste återskapa rolltilldelningarna i målklienten. Du måste manuellt återskapa hanterade identiteter för Azure-resurser. Mer information finns i [vanliga frågor och kända problem med hanterade identiteter](../active-directory/managed-identities-azure-resources/known-issues.md).
- Om du är en Azure AD Global administratör och du inte har åtkomst till en prenumeration när den har flyttats mellan klienter kan använda den **åtkomsthantering för Azure-resurser** Växla tillfälligt [utöka din behörighet](elevate-access-global-admin.md) att få åtkomst till prenumerationen.

## <a name="issues-with-service-admins-or-co-admins"></a>Problem med tjänstadministratörer eller medadministratörer

- Om du har problem med tjänstadministratör eller medadministratörer kan se [Lägg till eller ändra Azure-prenumerationsadministratörer](../billing/billing-add-change-azure-subscription-administrator.md) och [administratörsroller för klassiska prenumeration, Azure RBAC-roller och Azure AD administratörsroller](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Åtkomst nekad eller behörighet fel

- Om du får behörighetsfelet ”Klienten med objekt-ID har inte behörighet att utföra åtgärden över område (kod: AuthorizationFailed)” när du försöker skapa en resurs kan du kontrollera att du är inloggad med en användare med en roll som har skrivbehörighet till resursen i det valda omfånget. Om du vill hantera virtuella datorer i en resursgrupp ska du till exempel ha rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) på den resursgruppen (eller ett överordnat område). En lista med behörigheter för alla inbyggda roller finns i [Inbyggda roller för Azure-resurser](built-in-roles.md).
- Om du får felet behörigheter ”du har inte behörighet att skapa en supportbegäran” när du försöker skapa eller uppdatera ett supportärende, kontrollera att du har loggat in med en användare som har tilldelats en roll som har den `Microsoft.Support/supportTickets/write` behörighet, till exempel [Stöder begäran deltagare](built-in-roles.md#support-request-contributor).

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
* Belastningsutjämnad uppsättning  
* Varningsregler  

Fråga din administratör om du inte åtkomst till någon av dessa paneler för deltagaråtkomst till resursgruppen.

## <a name="azure-functions-and-write-access"></a>Azure Functions- och skrivåtkomst

Vissa funktioner i [Azure Functions](../azure-functions/functions-overview.md) kräver skrivbehörighet. Till exempel om en användare har tilldelats rollen Läsare, kommer de inte att kunna visa funktioner i en funktionsapp. Portalen visar **(ingen åtkomst)**.

![Ingen åtkomst för funktionsappar](./media/troubleshooting/functionapps-noaccess.png)

Användaren kan klicka på **plattformsfunktioner** fliken och klicka sedan på **alla inställningar** att visa vissa inställningar som är relaterade till en funktionsapp (liknar en webbapp), men de kan inte ändra någon av dessa inställningar.

## <a name="next-steps"></a>Nästa steg
* [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure-portalen](role-assignments-portal.md)
* [Visa aktivitetsloggar för RBAC ändringar till Azure-resurser](change-history-report.md)

