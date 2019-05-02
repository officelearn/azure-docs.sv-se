---
title: Ordna dina resurser med hanteringsgrupper i Azure – Azure Governance
description: Läs om hanteringsgrupperna, hur behörigheterna fungerar och hur du använder dem.
author: rthorn17
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.date: 04/22/2019
ms.author: rithorn
ms.topic: overview
ms.openlocfilehash: ceb606f2243ef723866e485c6580a6323c1c92ec
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722480"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Ordna resurser med hanteringsgrupper i Azure

Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsvillkor för hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen. Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.

Du kan till exempel tillämpa principer för en hanteringsgrupp som begränsar regionerna som är tillgängliga för att skapa virtuella datorer (VM). Den här principen tillämpas för alla hanteringsgrupper, prenumerationer och resurser under hanteringsgruppen genom att endast tillåta att virtuella datorer skapas i den regionen.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarki för hanteringsgrupper och prenumerationer

Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering. Följande diagram visar ett exempel på att skapa en hierarki för styrning med hjälp av hanteringsgrupper.

![Exempel på ett hierarkiträd för hanteringsgrupp](./media/tree.png)

Skapa en hierarki så att du kan tillämpa en princip, till exempel begränsa VM-platser till regionen USA, västra i gruppen ”Produktion”. Den här principen ärvs av EA-prenumerationerna under den hanteringsgruppen och gäller även för alla virtuella datorer under dessa prenumerationer. Den här säkerhetsprincipen kan inte ändras av resursen eller prenumerationsägaren, vilket leder till bättre styrning.

Ett annat scenario där du kan använda hanteringsgrupper är för att ge användaråtkomst till flera prenumerationer. Genom att flytta flera prenumerationer under hanteringsgruppen kan du skapa en tilldelning av [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) till den. Detta gör även att alla prenumerationer ärver åtkomsten.
Genom att tilldela till hanteringsgruppen kan du ge användarna åtkomst till allt de behöver istället för att skapa skript för RBAC för flera prenumerationer.

### <a name="important-facts-about-management-groups"></a>Viktiga fakta om hanteringsgrupper

- 10 000 hanteringsgrupper kan användas i en enskild katalog.
- Ett träd för hanteringsgruppen har stöd för upp till sex nivåer.
  - Den här gränsen omfattar inte rotnivån eller prenumerationsnivån.
- Varje hanteringsgrupp och prenumeration har endast stöd för ett överordnat element.
- Varje hanteringsgrupp kan ha många underordnade element.
- Alla prenumerationer och hanteringsgrupper ingår i en hierarki i varje katalog. Läs [Viktiga fakta om rothanteringsgruppen](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>En rothanteringsgrupp för varje katalog

Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas för rothanteringsgruppen.
Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med hjälp av rothanteringsgruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. [Den globala administratören för Azure Active Directory måste först utöka sin behörighet](../../role-based-access-control/elevate-access-global-admin.md) till rollen Administratör för användaråtkomst för rotgruppen. När behörigheten har utökats kan administratören hantera hierarkin och tilldela önskad RBAC-roll till andra kataloganvändare eller grupper. Som administratör kan du utse ditt eget konto till ägare av rothanteringsgruppen.

### <a name="important-facts-about-the-root-management-group"></a>Viktiga fakta om rothanteringsgruppen

- Som standard är rothanteringsgruppens visningsnamn **Klientorganisationens rotgrupp**. ID:t är samma som ID:t för Azure Active Directory.
- Om du vill ändra visningsnamnet måste ditt konto ha tilldelats rollen Ägare eller Deltagare i rothanteringsgruppen. Anvisningar om hur du ändrar namnet finns i [Change the name of a management group](manage.md#change-the-name-of-a-management-group) (Ändra namnet på en hanteringsgrupp).
- Rothanteringsgruppen kan inte flyttas eller tas bort, till skillnad från andra hanteringsgrupper.  
- Alla prenumerationer och hanteringsgrupper är underordnade rothanteringsgruppen i katalogen.
  - Alla resurser i katalogen är underordnade rothanteringsgruppen för global hantering.
  - Nya prenumerationer tilldelas som standard till rothanteringsgruppen när de skapas.
- Alla Azure-kunder kan se rothanteringsgruppen, men alla kunder får inte hantera rothanteringsgruppen.
  - Alla som har åtkomst till en prenumeration kan se kontexten för den aktuella prenumerationens placering i hierarkin.  
  - Det är inte någon som får åtkomst till rothanteringsgruppen som standard. Globala administratörer för Azure Active Directory är de enda användarna som kan ge sig själva åtkomst.  Efter att ha fått åtkomst kan de globala administratörerna tilldela önskade RBAC-roller till andra användare.  

> [!IMPORTANT]
> Alla tilldelningar av användaråtkomst eller principtilldelning för rothanteringsgruppen **gäller för alla resurser inom katalogen**.
> Alla kunder bör därför utvärdera behovet av objekt som definierats för det här området.
> Användaråtkomst och principtilldelningar bör endast vara ”måsten” i den här omfattningen.  

## <a name="initial-setup-of-management-groups"></a>Initial konfiguration av hanteringsgrupper

När användarna börjar använda hanteringsgrupper måste de genomföra en initial konfigurationsprocess. Det första steget är att rothanteringsgruppen skapas i katalogen. När den här gruppen har skapats blir alla befintliga prenumerationer i katalogen underordnade rothanteringsgruppen. Den här processen är till för att kontrollera att det endast finns en hanteringsgrupphierarki i en katalog. Hierarkin i katalogen gör det möjligt för administrativa kunder att använda global åtkomst och principer som andra kunder i katalogen inte kan kringgå. Allt som tilldelas på roten gäller för hela hierarkin, vilket omfattar alla hanteringsgrupper, prenumerationer, resursgrupper och resurser i Azure Active Directory-klientorganisationen.

## <a name="trouble-seeing-all-subscriptions"></a>Problem med att visa alla prenumerationer

Vissa kataloger som började använda hanteringsgrupper tidigt under förhandsgranskningen, innan den 25 juni 2018, märkte av ett problem där alla prenumerationer inte fanns i hierarkin. Processen för att lägga till prenumerationer i hierarkin implementerades efter det att en roll- eller principtilldelning utfördes på katalogens rothanteringsgrupp. 

### <a name="how-to-resolve-the-issue"></a>Så här löser du problemet

Det finns två alternativ som du kan använda för att lösa problemet.

1. Ta bort alla roll- och principtilldelningar från rothanteringsgruppen
   1. När du tar bort princip- och rolltilldelningar från rothanteringsgruppen återfyller tjänsten alla prenumerationer till hierarkin nästa dagcykel.  Den här principen är till för att kontrollera att det inte har getts någon oavsiktlig åtkomst eller principtilldelning till alla prenumerationer i klientorganisationen.
   1. Det bästa sättet att genomföra processen utan att påverka tjänsterna är att tilldela roll- eller principtilldelningar på en nivå lägre än rothanteringsgruppen. Sedan kan du ta bort alla tilldelningarna från rotomfånget.
1. Direktanropa API:t och påbörja återfyllningsprocessen
   1. Alla kunder i katalogen kan anropa API:erna *TenantBackfillStatusRequest* eller *StartTenantBackfillRequest*. När API:n StartTenantBackfillRequest anropas påbörjas den ursprungliga konfigurationsprocessen och alla prenumerationer flyttas till hierarkin. Processen gör även att alla nya prenumerationer blir underordnade rothanteringsgruppen. Den här processen kan utföras utan att ändra några tilldelningar på rotnivån. Genom att anropa API:et godkänner du att alla principer eller åtkomsttilldelningar på roten kan tillämpas på alla prenumerationer.

Om du har frågor om återfyllningsprocessen kan du kontakta: managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Åtkomst till hanteringsgrupp

Azure-hanteringsgrupper har stöd för [Azures rollbaserade åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) för alla resursåtkomster och rolldefinitioner.
Dessa behörigheter ärvs av underordnade resurser som finns i hierarkin. Alla inbyggda RBAC-roller kan tilldelas till en hanteringsgrupp som ärver nedåt i hierarkin av resurserna.
RBAC-rollen VM-deltagare kan till exempel tilldelas till en hanteringsgrupp. Rollen har ingen åtgärd för hanteringsgruppen, men ärver av alla virtuella datorer under hanteringsgruppen.

Följande diagram visar listan över roller och åtgärder som stöds för hanteringsgrupper.

| RBAC-rollnamn             | Skapa | Byt namn | Flytta** | Ta bort | Tilldela åtkomst | Tilldela princip | Läsa  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Ägare                       | X      | X      | X      | X      | X             | X             | X     |
|Deltagare                 | X      | X      | X      | X      |               |               | X     |
|MG-deltagare*             | X      | X      | X      | X      |               |               | X     |
|Läsare                      |        |        |        |        |               |               | X     |
|MG-läsare*                  |        |        |        |        |               |               | X     |
|Deltagare för resursprincip |        |        |        |        |               | X             |       |
|Administratör för användaråtkomst   |        |        |        |        | X             |               |       |

*: MG-deltagare och MG-läsare tillåter endast användare att utföra dessa åtgärder inom hanteringsgruppsomfånget.  
**: Rolltilldelningar på rothanteringsgruppen krävs inte för att flytta en prenumeration eller hanteringsgrupp till och från den.  Läs [Hantera dina resurser med hanteringsgrupper](manage.md) för mer information om att flytta objekt inom hierarkin.

### <a name="custom-rbac-role-definition-and-assignment"></a>Definition och tilldelning av anpassad RBAC-roll

Anpassade RBAC-roller stöds inte för närvarande för hanteringsgrupper. Gå till [feedbackforumet för hanteringsgrupper](https://aka.ms/mgfeedback) om du vill se statusen för det här objektet.

## <a name="audit-management-groups-using-activity-logs"></a>Granska hanteringsgrupper med hjälp av aktivitetsloggar

Hanteringsgrupper kan användas i [Azure-aktivitetsloggar](../../azure-monitor/platform/activity-logs-overview.md). Du kan söka efter alla händelser i en hanteringsgrupp från samma centrala plats som andra Azure-resurser.  Du kan till exempel se alla ändringar för rolltilldelningar eller principtilldelningar som gjorts i en viss hanteringsgrupp.

![Aktivitetsloggar med hanteringsgrupper](media/al-mg.png)

När du vill fråga hanteringsgrupper utanför Microsoft Azure-portalen är målområdet för hanteringsgrupper: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](manage.md)
- [Granska hanteringsgrupper i Azure PowerShell-resursmodulen](/powershell/module/az.resources#resources)
- [Granska hanteringsgrupper i REST API](/rest/api/resources/managementgroups)
- [Granska hanteringsgrupper i Azure CLI](/cli/azure/account/management-group)