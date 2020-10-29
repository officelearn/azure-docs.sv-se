---
title: Administrativa enheter i Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för mer detaljerad delegering av behörigheter i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fb24d0cd2714969b5a888b1036f524c4c062d76
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027626"
---
# <a name="administrative-units-in-azure-active-directory"></a>Administrativa enheter i Azure Active Directory

I den här artikeln beskrivs administrativa enheter i Azure Active Directory (Azure AD). En administrativ enhet är en Azure AD-resurs som kan vara en behållare för andra Azure AD-resurser. En administrativ enhet kan bara innehålla användare och grupper.

Med hjälp av administrativa enheter kan du bevilja administratörs behörigheter som är begränsade till en avdelning, region eller något annat segment i din organisation som du definierar. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller ange en princip på en detaljerad nivå. En användar konto administratör kan till exempel uppdatera profil information, återställa lösen ord och tilldela licenser enbart för användare i sin administrativa enhet.

Du kan till exempel delegera till regionala support specialister rollen [supportavdelning administratör](permissions-reference.md#helpdesk-administrator) , som är begränsad till att bara hantera användare i den region som de stöder.

## <a name="deployment-scenario"></a>Distributionsscenario

Det kan vara användbart att begränsa administrativ omfattning genom att använda administrativa enheter i organisationer som består av oberoende avdelningar av något slag. Överväg exemplet på en stor University som består av många autonoma skolor (skola, skola och så vidare). Varje skola har en grupp IT-administratörer som kontrollerar åtkomst, hanterar användare och ställer in principer för sin skola. 

En central administratör kan:

- Skapa en roll med administratörs behörighet enbart för Azure AD-användare i den administrativa enheten för affärs skolan.
- Skapa en administrativ enhet för affärs skolan.
- Fyll i den administrativa enheten med endast affärs studenter och personal.
- Lägg till IT-teamet för affärs skolan i rollen, tillsammans med dess omfattning.

## <a name="license-requirements"></a>Licenskrav

Om du vill använda administrativa enheter behöver du en Azure Active Directory Premium-licens för varje administrativ enhets administratör och Azure Active Directory Free licenser för administrativa enhets medlemmar. Mer information finns i [komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Hantera administrativa enheter

Du kan hantera administrativa enheter med hjälp av Azure Portal, PowerShell-cmdletar och skript, eller Microsoft Graph. Mer information finns i:

- [Skapa, ta bort, fylla i och lägga till roller i administrativa enheter](admin-units-manage.md): innehåller fullständiga instruktions procedurer.
- [Arbeta med administrativa enheter](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): beskriver hur du arbetar med administrativa enheter med hjälp av PowerShell.
- [Stöd för diagram över administrativa enheter](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): innehåller detaljerad dokumentation om Microsoft Graph för administrativa enheter.

### <a name="plan-your-administrative-units"></a>Planera dina administrativa enheter

Du kan använda administrativa enheter för att logiskt gruppera Azure AD-resurser. Till exempel, för en organisation vars IT-avdelning är spridd globalt, kan det vara klokt att skapa administrativa enheter som definierar dessa geografiska gränser. I ett annat scenario, där en multinationell organisation har olika *under organisationer* som är halv autonom i deras verksamhet, kan en administrativ enhet representera varje under organisation.

De kriterier som de administrativa enheterna skapas i vägleds av de unika kraven för en organisation. Administrativa enheter är ett vanligt sätt att definiera strukturen mellan Microsoft 365 tjänster. Vi rekommenderar att du förbereder dina administrativa enheter med deras användning i Microsoft 365 tjänster i åtanke. Du kan få det maximala värdet av administrativa enheter när du kan koppla vanliga resurser mellan Microsoft 365 under en administrativ enhet.

Du kan vänta dig att skapa administrativa enheter i organisationen för att gå igenom följande steg:

1. **Första införande** : din organisation kommer att börja skapa administrativa enheter baserat på inledande kriterier och antalet administrativa enheter ökar när villkoren är raffinerade.
1. **Rensning** : när kriterierna är väl definierade tas administrativa enheter som inte längre behövs bort.
1. **Stabilisering** : din organisations struktur är väl definierad och antalet administrativa enheter kommer inte att ändras avsevärt på kort sikt.

## <a name="currently-supported-scenarios"></a>Scenarier som stöds för närvarande

Som global administratör eller administratör för privilegierade roller kan du använda Azure AD-portalen för att skapa administrativa enheter, lägga till användare som medlemmar i administrativa enheter och sedan tilldela IT-personalen till administrativa roller för enhets omfångs administratörer. Administrativa enhets omfångs administratörer kan sedan använda Microsoft 365 administrations Center för grundläggande hantering av användare i deras administrativa enheter.

Dessutom kan du lägga till grupper som medlemmar i en administrativ enhet. En administrativ enhets grupp administratör kan hantera dem med hjälp av PowerShell, Microsoft Graph och Azure AD-portalen.

I följande tabeller beskrivs det aktuella stödet för administrativa enhets scenarier:

### <a name="administrative-unit-management"></a>Hantering av administrativa enheter

| Behörigheter |   Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365 | 
| -- | -- | -- | -- |
| Skapa och ta bort administrativa enheter   |    Stöds    |   Stöds   |    Stöds inte | 
| Lägga till och ta bort administrativa enhets medlemmar individuellt    |   Stöds    |   Stöds   |    Stöds inte | 
| Lägga till och ta bort administrativa enhets medlemmar i bulk genom att använda CSV-filer   |    Stöds inte     |  Stöds   |    Ingen Support plan | 
| Tilldela administratörer av administrativa enhets omfång  |     Stöds    |   Stöds    |   Stöds inte | 
| Att lägga till och ta bort administrativa enhets medlemmar dynamiskt baserat på attribut | Stöds inte | Stöds inte | Stöds inte 

### <a name="user-management"></a>Användarhantering

| Behörigheter |   Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365 |
| -- | -- | -- | -- |
| Administrativ enhets hantering av användar egenskaper, lösen ord och licenser   |    Stöds     |  Stöds   |   Stöds |
| Administrativ enhet – definition av blockerade och avblockerade användar inloggningar    |   Stöds   |    Stöds   |    Stöds |
| Administrativ enhets begränsad hantering av autentiseringsuppgifter för användarens multifaktorautentisering   |    Stöds   |   Stöds   |   Stöds inte |

### <a name="group-management"></a>Grupphantering

| Behörigheter |   Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365 |
| -- | -- | -- | -- |
| Administrativ enhets hantering av grupp egenskaper och medlemmar     |  Stöds   |    Stöds    |  Stöds inte |
| Administrativ enhets hantering av grupp licensiering   |    Stöds  |    Stöds   |   Stöds inte |


Administrativa enheter tillämpar endast omfång på hanterings behörigheter. De förhindrar inte att medlemmar eller administratörer använder sina [standard användar behörigheter](../fundamentals/users-default-permissions.md) för att bläddra bland andra användare, grupper eller resurser utanför den administrativa enheten. I Microsoft 365 administrations centret filtreras användare utanför en omfångs administratörs administrativa enheter. Men du kan söka efter andra användare i Azure AD-portalen, PowerShell och andra Microsoft-tjänster.

## <a name="next-steps"></a>Nästa steg

- [Hantera administrativa enheter](admin-units-manage.md)
- [Hantera användare i administrativa enheter](admin-units-add-manage-users.md)
- [Hantera grupper i administrativa enheter](admin-units-add-manage-groups.md)
- [Tilldela begränsade roller till en administrativ enhet](admin-units-assign-roles.md)
