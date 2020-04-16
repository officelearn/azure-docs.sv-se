---
title: Hantering av administrativa enheter (förhandsversion) – Azure AD | Microsoft-dokument
description: Använda administrativa enheter för mer detaljerad delegering av behörigheter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406477"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Hantering av administrativa enheter i Azure Active Directory (förhandsversion)

I den här artikeln beskrivs administrativa enheter i Azure Active Directory (Azure AD). En administrativ enhet är en Azure AD-resurs som kan vara en behållare för andra Azure AD-resurser. I den här förhandsversionen kan en administrativ enhet bara innehålla användare och grupper.

Med administrativa enheter kan du bevilja administratörsbehörighet som är begränsad till en avdelning, region eller annat segment i organisationen som du definierar. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller för att ange principen på detaljerad nivå. En administratör för användarkonton kan till exempel uppdatera profilinformation, återställa lösenord och tilldela licenser för användare endast i deras administrativa enhet.

 Delegera till regionala supportspecialister rollen [Helpdesk Administrator](directory-assign-admin-roles.md#helpdesk-administrator) som är begränsad till att hantera bara användarna i den region de stöder.

## <a name="deployment-scenario"></a>Distributionsscenario

Att begränsa administrativt scope med hjälp av administrativa enheter kan vara användbart i organisationer som består av oberoende divisioner av något slag. Tänk på exemplet med ett stort universitet som består av många autonoma skolor (School of Business, School of Engineering och så vidare) som var och en har ett team av IT-administratörer som styr åtkomst, hantera användare och ange principer för sin skola. En central administratör kan:

- Skapa en roll med administrativa behörigheter för endast Azure AD-användare i business school administrativa enheten
- Skapa en administrativ enhet för Handelshögskolan
- Fyll i administratörsenheten med endast affärsskoleelever och personal
- Lägg till Business school IT-teamet i rollen med deras omfattning

## <a name="license-requirements"></a>Licenskrav

Med hjälp av administrativa enheter krävs en Azure Active Directory Premium-licens för varje administratör för administrativa enheter och Azure Active Directory Free-licenser för administrativa enhetsmedlemmar. Mer information finns i [Komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Hantera administrativa enheter

I den här förhandsversionen kan du hantera administrativa enheter med Azure-portalen, PowerShell-cmdlets och skript eller Microsoft Graph. Du kan läsa vår dokumentation för mer information:

- [Skapa, ta bort, fylla i och lägga till roller i administrativa enheter](roles-admin-units-manage.md): Slutför instruktioner
- [Arbeta med adminenheter:](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)Så här arbetar du med administrativa enheter med PowerShell
- [Stöd för administrationsenhetsdiagram](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): Detaljerad dokumentation på Microsoft Graph för administrativa enheter.

### <a name="planning-your-administrative-units"></a>Planera dina administrativa enheter

Administrativa enheter kan användas för att logiskt gruppera Azure AD-resurser. För en organisation vars IT-avdelning är utspridd globalt kan det till exempel vara meningsfullt att skapa administrativa enheter som definierar dessa geografiska gränser. I ett annat scenario där en multinationell organisation har olika "underorganisationer", som är halvautonoma i operationer, kan varje underorganisation representeras av en administrativ enhet.

De kriterier som administrativa enheter skapas på styrs av en organisations unika krav. Administrativa enheter är ett vanligt sätt att definiera struktur över M365-tjänster. Vi rekommenderar att du förbereder dina administrativa enheter med deras användning i m365-tjänster i åtanke. Du kan få maximalt värde av administrativa enheter när du kan associera gemensamma resurser över M365 under en administrativ enhet.

Du kan förvänta dig att skapandet av administrativa enheter i organisationen ska gå igenom följande steg:

1. Inledande antagande: Din organisation börjar skapa administrativa enheter baserat på inledande kriterier och antalet administrativa enheter ökar när kriterierna förfinas.
1. Beskärning: När kriterierna är väl definierade tas administrativa enheter som inte längre behövs bort.
1. Stabilisering: Organisationsstrukturen är väl definierad och antalet administrativa enheter kommer inte att ändras avsevärt under korta varaktigheter.

## <a name="currently-supported-scenarios"></a>Scenarier som stöds för närvarande

Globala administratörer eller privilegierade rolladministratörer kan använda Azure AD-portalen för att skapa administrativa enheter, lägga till användare som medlemmar i administrativa enheter och sedan tilldela IT-personal till administratörsroller som omfattas av administrativa enheter. Administratörerna för den administrativa enheten kan sedan använda Office 365-portalen för grundläggande hantering av användare i sina administrativa enheter.

Dessutom kan grupper läggas till som medlemmar i den administrativa enheten och en administratörsenhetsomfattig gruppadministratör kan hantera dem med PowerShell, Microsoft Graph och Azure AD-portalen.

I tabellen nedan beskrivs aktuellt stöd för scenarier för administrativa enheter.

### <a name="administrative-unit-management"></a>Förvaltning av administrativa enheter

Behörigheter |   MS-graf/PowerShell   | Azure AD-portal | Administrationscenter för Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Skapa och ta bort administrativa enheter   |    Stöds    |   Stöds   |    Stöds inte
Lägga till och ta bort medlemmar på administrativa enheter individuellt    |   Stöds    |   Stöds   |    Stöds inte
Masstillägg och borttagning av medlemmar i administrativa enheter med CSV-fil   |    Stöds inte     |  Stöds   |    Ingen plan för att stödja
Tilldela administratörer med administrativa enhetsomfattade  |     Stöds    |   Stöds    |   Stöds inte
Lägga till och ta bort AU-medlemmar dynamiskt baserat på attribut | Stöds inte | Stöds inte | Stöds inte

### <a name="user-management"></a>Användarhantering

Behörigheter |   MS-graf/PowerShell   | Azure AD-portal | Administrationscenter för Microsoft 365
----------- | ----------------------- | --------------- | -----------------
administrativ enhetsscoped hantering av användaregenskaper, lösenord, licenser   |    Stöds     |  Stöds   |   Stöds
administrativ enhetsbestring och blockering av användarloggningar    |   Stöds   |    Stöds   |    Stöds
administrativ enhetsbehörig hantering av användare MFA-autentiseringsuppgifter   |    Stöds   |   Stöds   |   Stöds inte

### <a name="group-management"></a>Grupphantering

Behörigheter |   MS-graf/PowerShell   | Azure AD-portal | Administrationscenter för Microsoft 365
----------- | ----------------------- | --------------- | -----------------
administrativ enhetsbesiktning av gruppfastigheter och medlemmar     |  Stöds   |    Stöds    |  Stöds inte
administrativ enhetsbesiktning av koncernlicensiering   |    Stöds  |    Stöds   |   Stöds inte

> [!NOTE]
>
> Administratörer med ett administrativt enhetsomfång kan inte hantera regler för dynamiskt gruppmedlemskap.

Administrativa enheter tillämpar endast omfattning på hanteringsbehörigheter. De hindrar inte medlemmar eller administratörer från att använda sina [standardanvändarbehörigheter](../fundamentals/users-default-permissions.md) för att bläddra bland andra användare, grupper eller resurser utanför den administrativa enheten. I Office 365-portalen filtreras användare utanför en begränsad administratörs administrativa enheter ut, men du kan bläddra bland andra användare i Azure AD-portalen, PowerShell och andra Microsoft-tjänster.

## <a name="next-steps"></a>Nästa steg

- [Hantera AUs](roles-admin-units-manage.md)
- [Hantera användare i AUs](roles-admin-units-add-manage-users.md)
- [Hantera grupper i AUs](roles-admin-units-add-manage-groups.md)
- [Tilldela begränsade roller till en AU](roles-admin-units-assign-roles.md)