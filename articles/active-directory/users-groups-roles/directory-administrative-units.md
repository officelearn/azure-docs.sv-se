---
title: Administrativa enheter i Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för mer detaljerad delegering av behörigheter i Azure Active Directory
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
ms.openlocfilehash: 7aa356c806b6b96fa6a22f8642b005b2002f7c2a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447895"
---
# <a name="administrative-units-in-azure-active-directory"></a>Administrativa enheter i Azure Active Directory

I den här artikeln beskrivs administrativa enheter i Azure Active Directory (Azure AD). En administrativ enhet är en Azure AD-resurs som kan vara en behållare för andra Azure AD-resurser. En administrativ enhet kan bara innehålla användare och grupper.

Med administrativa enheter kan du bevilja administratörs behörigheter som är begränsade till en avdelning, region eller något annat segment i din organisation som du definierar. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller ange en princip på en detaljerad nivå. En användar konto administratör kan till exempel uppdatera profil information, återställa lösen ord och tilldela licenser enbart för användare i sin administrativa enhet.

Du kan till exempel delegera till regionala support specialister som [Administratörs](directory-assign-admin-roles.md#helpdesk-administrator) rollen för supportavdelningen begränsas till att bara hantera användare i den region som de stöder.

## <a name="deployment-scenario"></a>Distributionsscenario

Att begränsa administrations omfånget med hjälp av administrativa enheter kan vara användbart i organisationer som består av oberoende avdelningar av något slag. Tänk dig ett exempel på ett stort universitet som består av många autonoma skolor (skola, skola och så vidare) som var och en har ett team av IT-administratörer som kontrollerar åtkomst, hanterar användare och anger principer för deras skola. En central administratör kan:

- Skapa en roll med administratörs behörighet enbart för Azure AD-användare i den administrativa enheten för affärs skolan
- Skapa en administrativ enhet för affärs skolan
- Fyll i admin-enheten med endast affärs studenter och personal i skolan
- Lägg till IT-teamet för affärs skolan i rollen med deras omfång

## <a name="license-requirements"></a>Licenskrav

Användning av administrativa enheter kräver en Azure Active Directory Premium-licens för varje administrativ enhets administratör och Azure Active Directory Free licenser för administrativa enhets medlemmar. Mer information finns i [komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Hantera administrativa enheter

Du kan hantera administrativa enheter med hjälp av Azure Portal, PowerShell-cmdletar och skript, eller Microsoft Graph. Du hittar mer information i vår dokumentation:

- [Skapa, ta bort, fylla i och lägga till roller i administrativa enheter](roles-admin-units-manage.md): fullständiga instruktions procedurer
- [Arbeta med administratörs enheter](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): så här arbetar du med administrativa enheter med hjälp av PowerShell
- [Stöd för diagram över administrativa enheter](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): detaljerad dokumentation om Microsoft Graph för administrativa enheter.

### <a name="planning-your-administrative-units"></a>Planera dina administrativa enheter

Administrativa enheter kan användas för att logiskt gruppera Azure AD-resurser. Till exempel, för en organisation vars IT-avdelning är spridd globalt, kan det vara klokt att skapa administrativa enheter som definierar dessa geografiska gränser. I ett annat scenario där en organisation med flera nationella organisationer har olika "under organisationer", som är halv fabrikat i drift, kan varje under organisation representeras av en administrativ enhet.

De kriterier som de administrativa enheterna skapas i vägleds av de unika kraven för en organisation. Administrativa enheter är ett vanligt sätt att definiera strukturen mellan Microsoft 365 tjänster. Vi rekommenderar att du förbereder dina administrativa enheter med deras användning i Microsoft 365 tjänster i åtanke. Du kan få det maximala värdet av administrativa enheter när du kan koppla vanliga resurser mellan Microsoft 365 under en administrativ enhet.

Du kan vänta dig att skapa administrativa enheter i organisationen för att gå igenom följande steg:

1. Första införande: din organisation kommer att börja skapa administrativa enheter baserat på inledande kriterier och antalet administrativa enheter ökar när kriterierna är raffinerade.
1. Rensning: när villkoret är väl definierat kommer administrativa enheter som inte längre krävs att tas bort.
1. Stabilisering: din organisations struktur är väl definierad och antalet administrativa enheter kommer inte att ändras avsevärt över korta varaktigheter.

## <a name="currently-supported-scenarios"></a>Scenarier som stöds för närvarande

Globala administratörer eller privilegierade roll administratörer kan använda Azure AD-portalen för att skapa administrativa enheter, lägga till användare som medlemmar i administrativa enheter och sedan tilldela IT-personal till administrativa roller för enhets omfångs administratörer. De administrativa enhets omfångs administratörer kan sedan använda Microsoft 365 administrations Center för grundläggande hantering av användare i deras administrativa enheter.

Dessutom kan grupper läggas till som medlemmar i en administrativ enhet, och en administratör för enhets gruppen kan hantera dem med hjälp av PowerShell, Microsoft Graph och Azure AD-portalen.

I tabellen nedan beskrivs aktuella stöd för administrativa enhets scenarier.

### <a name="administrative-unit-management"></a>Hantering av administrativa enheter

Behörigheter |   MS Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Skapa och ta bort administrativa enheter   |    Stöds    |   Stöds   |    Stöds inte
Lägga till och ta bort administrativa enhets medlemmar individuellt    |   Stöds    |   Stöds   |    Stöds inte
Lägga till och ta bort administrativa enhets medlemmar med hjälp av CSV-filen   |    Stöds inte     |  Stöds   |    Ingen Support plan
Tilldela administratörer av administrativa enhets omfång  |     Stöds    |   Stöds    |   Stöds inte
Lägga till och ta bort AU-medlemmar dynamiskt baserat på attribut | Stöds inte | Stöds inte | Stöds inte

### <a name="user-management"></a>Användarhantering

Behörigheter |   MS Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365
----------- | ----------------------- | --------------- | -----------------
administrativ enhets hantering av användar egenskaper, lösen ord, licenser   |    Stöds     |  Stöds   |   Stöds
administrativ enhet – definition av blockerade och avblockerade användar inloggningar    |   Stöds   |    Stöds   |    Stöds
administrativ enhets begränsad hantering av användarens MFA-autentiseringsuppgifter   |    Stöds   |   Stöds   |   Stöds inte

### <a name="group-management"></a>Grupphantering

Behörigheter |   MS Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365
----------- | ----------------------- | --------------- | -----------------
administrativ enhets hantering av grupp egenskaper och medlemmar     |  Stöds   |    Stöds    |  Stöds inte
administrativ enhets hantering av grupp licensiering   |    Stöds  |    Stöds   |   Stöds inte


Administrativa enheter tillämpar endast omfång på hanterings behörigheter. De förhindrar inte att medlemmar eller administratörer använder sina [standard användar behörigheter](../fundamentals/users-default-permissions.md) för att bläddra bland andra användare, grupper eller resurser utanför den administrativa enheten. I Microsoft 365 administrations centret filtreras användare utanför en omfångs administratörs administrativa enheter, men du kan söka i andra användare i Azure AD-portalen, PowerShell och andra Microsoft-tjänster.

## <a name="next-steps"></a>Nästa steg

- [Hantera Australien](roles-admin-units-manage.md)
- [Hantera användare i Australien](roles-admin-units-add-manage-users.md)
- [Hantera grupper i Australien](roles-admin-units-add-manage-groups.md)
- [Tilldela begränsade roller till en AU](roles-admin-units-assign-roles.md)
