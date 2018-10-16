---
title: Förstå Azure Digital Twins rollbaserad åtkomstkontroll | Microsoft Docs
description: Läs om autentisering i digitala Twins med rollbaserad åtkomstkontroll.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324328"
---
# <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure Digital Twins kan exakt åtkomstkontroll till specifika data, resurser och åtgärder i grafen spatial. Detta sker via detaljerade roll- och behörighetshantering som kallas _Role-Based Access Control_. Rollbaserad åtkomstkontroll består av _roller_, eller behörighetsnivån, och _rolltilldelningar_, eller associationen mellan en roll till en användare eller enhet.

Med hjälp av rollbaserad åtkomstkontroll, kan behörighet beviljas till en användare, en enhet, ett huvudnamn för tjänsten, en användardefinierad funktion, alla användare som tillhör en domän eller en klient. Graden av åtkomst kan dessutom också vara välavvägda.

Rollbaserad åtkomstkontroll är unikt eftersom behörigheter ärvs av spatial diagrammet.

## <a name="what-can-i-do-with-role-based-access-control"></a>Vad kan jag göra med rollbaserad åtkomstkontroll?

Utvecklare kan använda rollbaserad åtkomstkontroll till:

* Bevilja en användare möjlighet att hantera enheter för en hel byggnad eller endast för en viss plats eller våning.
* Bevilja en global administratörsåtkomst till alla spatial graph-noder för en hela diagrammet eller endast för en del av diagrammet.
* Ger en support specialist läsåtkomst till diagrammet, förutom åtkomstnycklar.
* Ge alla medlemmar i en domän läsbehörighet till alla objekt i diagrammet.

## <a name="role-based-access-control-best-practices"></a>Metodtips för rollbaserad åtkomstkontroll

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Roller

### <a name="role-definitions"></a>Rolldefinitioner

En **rolldefinition** är en uppsättning behörigheter och kallas ibland en **rollen**. Rolldefinitionen visar en lista över tillåtna åtgärder, inklusive *skapa*, *läsa*, *uppdatera*, och *ta bort*. Den anger också vilka objekttyper gäller dessa behörigheter.

Följande roller är tillgängliga i Azure Digital Twins:

* **Utrymme administratör**: skapa, läsa, uppdatera och ta bort behörigheten för det angivna utrymmet och alla noder under. Globala behörigheter.
* **Användaradministratör**: skapa, läsa, uppdatera och ta bort behörigheten för användare och användare-relaterade objekt. Läsbehörighet för blanksteg.
* **Enhetsadministratör**: skapa, läsa, uppdatera och ta bort behörigheten för enheter och enheter-relaterade objekt. Läsbehörighet för blanksteg.
* **Nyckeln administratör**: skapa, läsa, uppdatera och ta bort behörigheten för åtkomstnycklar. Läsbehörighet för blanksteg.
* **Token administratör**: läsa och uppdatera behörighet för åtkomstnycklar. Läsbehörighet för blanksteg.
* **Användaren**: läsbehörighet för blanksteg, sensorer och användare, inklusive de tillhörande relaterade objekt.
* **Stöd för Specialist**: läsbehörighet för allt utom åtkomstnycklar.
* **Enheten Installer**: läsa och uppdatera behörighet för enheter och sensorer, inklusive de tillhörande relaterade objekt. Läsbehörighet för blanksteg.
* **Gateway-enhet**: skapa behörigheten för sensorer. Läsbehörighet för enheter och sensorer, relaterade inklusive de tillhörande objekt.

>[!NOTE]
> *Fullständig definitioner för ovanstående kan hämtas genom att fråga system/roller API.*

### <a name="object-types"></a>Objekt av typen

Den `ObjectIdType` refererar till typ av identitet som som ges en roll. Förutom den `DeviceId` och `UserDefinedFunctionId` typer, vilka motsvarar en egenskap för ett Azure Active Directory (Azure AD)-objekt:
  
* Den `UserId` typen tilldelar en roll till en användare.
* Den `DeviceId` typen tilldelar en roll till en enhet.
* Den `DomainName` typen tilldelar en roll till ett domännamn. Varje användare med det angivna domännamnet har behörighet för rollen motsvarande.
* Den `TenantId` typen tilldelar en roll till en klient. Varje användare som hör till den angivna Azure AD-klient-ID har behörighet för rollen motsvarande.
* Den `ServicePrincipalId` typen tilldelar en roll till ett objekt-ID.
* Den `UserDefinedFunctionId` typen tilldelar en roll till en användardefinierade funktionen (UDF).

> [!div class="nextstepaction"]
> [Fråga eller objekt-ID för en användare](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Hämta objekt-ID för tjänstens huvudnamn](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Hämta objekt-ID för en Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Rolltilldelningar

Behörigheter beviljas till en mottagare genom att skapa en rolltilldelning och återkallas genom att ta bort en rolltilldelning. En rolltilldelning i Azure Digital Twins associerar ett objekt (användare, Azure AD-klient, osv.), rollen och ett blanksteg. Sedan behörigheter för alla objekt som hör till det området, inklusive hela spatial diagrammet under den.

Exempelvis kan en användare får en rolltilldelning med rollen `DeviceInstaller` för rotnoden i ett spatial diagram, som representerar en byggnad. Användaren kan sedan att läsa och uppdatera enheter för den noden, men alla andra underordnade utrymmen i byggnaden.

## <a name="next-steps"></a>Nästa steg

Läs om Azure Digital Twins säkerhet [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).
