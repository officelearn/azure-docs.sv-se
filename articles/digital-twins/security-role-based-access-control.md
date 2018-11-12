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
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014796"
---
# <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure Digital Twins kan exakt åtkomstkontroll till specifika data, resurser och åtgärder i grafen spatial. Detta sker via detaljerade roll- och behörighetshantering som kallas rollbaserad åtkomstkontroll (RBAC). RBAC består av _roller_ och _rolltilldelningar_. Roller identifiera behörighetsnivån. Rolltilldelningar koppla en roll till en användare eller enhet.

Med RBAC kan kan behörighet beviljas till:

- En användare.
- En enhet.
- Ett huvudnamn för tjänsten.
- En användardefinierad funktion. 
- Alla användare som tillhör en domän. 
- En klient.
 
Graden av åtkomst kan även finjusteras.

RBAC är unikt eftersom behörigheter ärvs av spatial diagrammet.

## <a name="what-can-i-do-with-rbac"></a>Vad kan jag göra med RBAC?

Utvecklare kan använda RBAC för att:

* Bevilja en användare möjlighet att hantera enheter för en hel byggnad eller endast för ett specifikt rum eller våning.
* Bevilja en global administratörsåtkomst till alla spatial graph-noder för en hela diagrammet eller endast för en del av diagrammet.
* Ger en support specialist läsåtkomst till diagrammet, förutom åtkomstnycklar.
* Ge alla medlemmar i en domän läsbehörighet till alla objekt i diagrammet.

## <a name="rbac-best-practices"></a>Metodtips för RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Roller

### <a name="role-definitions"></a>Rolldefinitioner

En rolldefinition är en uppsättning behörigheter och kallas ibland för en roll. Rollen definitionslistor tillåtna åtgärder, bland annat skapa, läsa, uppdatera och ta bort. Den anger också vilka objekttyper gäller dessa behörigheter.

Följande roller är tillgängliga i Azure Digital Twins:

* **Utrymme administratör**: skapa, läsa, uppdatera och ta bort behörigheten för det angivna utrymmet och alla noder under. Globala behörigheter.
* **Användaradministratör**: skapa, läsa, uppdatera och ta bort behörigheten för användare och användare-relaterade objekt. Läsbehörighet för blanksteg.
* **Enhetsadministratör**: skapa, läsa, uppdatera och ta bort behörigheten för enheter och enheter-relaterade objekt. Läsbehörighet för blanksteg.
* **Nyckeln administratör**: skapa, läsa, uppdatera och ta bort behörigheten för åtkomstnycklar. Läsbehörighet för blanksteg.
* **Token administratör**: läsa och uppdatera behörighet för åtkomstnycklar. Läsbehörighet för blanksteg.
* **Användaren**: läsbehörighet för blanksteg, sensorer och användare, som innehåller de tillhörande relaterade objekt.
* **Stöd för Specialist**: läsbehörighet för allt utom åtkomstnycklar.
* **Enheten Installer**: läsa och uppdatera behörighet för enheter och sensorer, som innehåller de tillhörande relaterade objekt. Läsbehörighet för blanksteg.
* **Gateway-enhet**: skapa behörigheten för sensorer. Läsbehörighet för enheter och sensorer, relaterade som innehåller de tillhörande objekt.

>[!NOTE]
> Fråga systemet/roller API för att hämta fullständig definitionerna för rollerna som tidigare.

### <a name="object-types"></a>Objekt av typen

Den `ObjectIdType` refererar till typ av identitet som har gett en roll. Förutom den `DeviceId` och `UserDefinedFunctionId` typer, vilka motsvarar en egenskap för ett Azure Active Directory (Azure AD)-objekt:
  
* Den `UserId` typen tilldelar en roll till en användare.
* Den `DeviceId` typen tilldelar en roll till en enhet.
* Den `DomainName` typen tilldelar en roll till ett domännamn. Varje användare med det angivna domännamnet har rättigheter motsvarande roll.
* Den `TenantId` typen tilldelar en roll till en klient. Varje användare som tillhör den angivna Azure AD-klient-ID har behörighet för rollen motsvarande.
* Den `ServicePrincipalId` typen tilldelar en roll till ett objekt-ID.
* Den `UserDefinedFunctionId` typen tilldelar en roll till en användardefinierad funktion (UDF).

> [!div class="nextstepaction"]
> [Fråga eller objekt-ID för en användare](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Hämta objekt-ID för tjänstens huvudnamn](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Hämta objekt-ID för en Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Rolltilldelningar

Skapa en rolltilldelning för att tilldela behörigheter till en mottagare. Om du vill återkalla behörighet att ta bort rolltilldelningen. En rolltilldelning i Azure Digital Twins associerar ett objekt, till exempel en användare eller en Azure AD-klient med en roll eller ett blanksteg. Behörigheter för alla objekt som hör till det området. Området innehåller hela spatial diagrammet under den.

Exempelvis kan en användare får en rolltilldelning med rollen `DeviceInstaller` för rotnoden i ett spatial diagram, som representerar en byggnad. Användaren kan läsa och uppdatera enheter för den aktuella noden och alla andra underordnade utrymmen i byggnaden.

## <a name="next-steps"></a>Nästa steg

Läs om Azure Digital Twins säkerhet [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).
