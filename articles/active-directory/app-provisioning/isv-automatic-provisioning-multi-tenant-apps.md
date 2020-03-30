---
title: Aktivera automatisk användaretablering för program med flera innehavare - Azure AD
description: En guide för oberoende programvaruleverantörer för att aktivera automatisk etablering
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522401"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Aktivera automatisk användaretablering för ditt program med flera innehavare

Automatisk användaretablering är processen att automatisera skapandet, underhållet och borttagningen av användaridentiteter i målsystem som dina program som en tjänst.

## <a name="why-enable-automatic-user-provisioning"></a>Varför aktivera automatisk etablering av användare?

Program som kräver att en användarpost finns i programmet innan en användares första inloggning kräver etablering av användare. Det finns fördelar för dig som tjänsteleverantör och fördelar för dina kunder.

### <a name="benefits-to-you-as-the-service-provider"></a>Fördelar för dig som tjänsteleverantör

* Öka säkerheten för ditt program med hjälp av Microsofts identitetsplattform.

* Minska faktiska och upplevda kundansträngningar att anta din ansökan.

* Minska dina kostnader för att integrera med flera identitetsleverantörer (IdPs) för automatisk användaretablering genom att använda SCIM-baserad etablering (System for Cross-Domain Identity Management).

* Minska supportkostnaderna genom att tillhandahålla omfattande loggar som hjälper kunderna att felsöka problem med användaretablering.

* Öka synligheten för ditt program i [Azure AD-appgalleriet](https://azuremarketplace.microsoft.com/marketplace/apps).

* Få en prioriterad lista på sidan App-självstudier.

### <a name="benefits-to-your-customers"></a>Fördelar för dina kunder

* Öka säkerheten genom att automatiskt ta bort åtkomsten till ditt program för användare som byter roller eller lämnar organisationen till ditt program.

* Förenkla användarhanteringen för ditt program genom att undvika mänskliga fel och repetitivt arbete i samband med manuell etablering.

* Minska kostnaderna för att vara värd och underhålla specialutvecklade etableringslösningar.

## <a name="choose-a-provisioning-method"></a>Välj en etableringsmetod

Azure AD innehåller flera integrationssökvägar för att aktivera automatisk användaretablering för ditt program.

* [Azure AD-etableringstjänsten](../app-provisioning/user-provisioning.md) hanterar etablering och avetablering av användare från Azure AD till ditt program (utgående etablering) och från ditt program till Azure AD (inkommande etablering). Tjänsten ansluter till SCIM-användarhanterings-API-slutpunkter (System for Cross-Domain Identity Management) som tillhandahålls av ditt program.

* När du använder [Microsoft Graph](https://docs.microsoft.com/graph/)hanterar ditt program inkommande och utgående etablering av användare och grupper från Azure AD till ditt program genom att fråga Microsoft Graph API.

* Användaretablering av säkerhetspåslagsspråket Just in Time (SAML JIT) kan aktiveras om ditt program använder SAML för federation. Den använder anspråksinformation som skickas i SAML-token för att etablera användare.

Information om vilket integrationsalternativ som ska användas för ditt program finns i jämförelsetabellen på hög nivå och sedan mer detaljerad information om varje alternativ.

| Funktioner som aktiveras eller förbättras genom automatisk etablering| Azure AD-etableringstjänst (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Användar- och grupphantering i Azure AD| √| √| Endast användare |
| Hantera användare och grupper synkroniserade från lokala Active Directory| √*| √*| Endast användare* |
| Få tillgång till data utöver användare och grupper under etablering av åtkomst till O365-data (Teams, SharePoint, E-post, Kalender, Dokument osv.)| X+| √| X |
| Skapa, läsa och uppdatera användare baserat på affärsregler| √| √| √ |
| Ta bort användare baserat på affärsregler| √| √| X |
| Hantera automatisk användaretablering för alla program från Azure-portalen| √| X| √ |
| Stöd för flera identitetsleverantörer| √| X| √ |
| Stöd gästkonton (B2B)| √| √| √ |
| Stödkonton utanför företaget (B2C)| X| √| √ |

<sup>*</sup>– Azure AD Connect-konfiguration krävs för att synkronisera användare från AD till Azure AD.  
<sup>+</sup >– Att använda SCIM för etablering hindrar dig inte från att integrera din applikation med MIcrosoft Graph för andra ändamål.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD-etableringstjänst (SCIM)

Azure AD-etableringstjänsterna använder [SCIM](https://aka.ms/SCIMOverview), en branschstandard för etablering som stöds av många identitetsleverantörer (IdPs) samt program (t.ex. Vi rekommenderar att du använder Azure AD-etableringstjänsten om du vill stödja IdPs utöver Azure AD, eftersom alla SCIM-kompatibla IdP kan ansluta till din SCIM-slutpunkt. Skapa en enkel /User slutpunkt, kan du aktivera etablering utan att behöva behålla din egen synkroniseringsmotor. 

Mer information om hur Azure AD-etableringstjänstens användare SCIM finns i: 

* [Läs mer om SCIM-standarden](https://aka.ms/SCIMOverview)

* [Använda SCIM (System for Cross-Domain Identity Management) för att automatiskt etablera användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Förstå Azure AD SCIM-implementeringen](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph för etablering

När du använder Microsoft Graph för etablering har du tillgång till alla omfattande användardata som är tillgängliga i Graph. Förutom information om användare och grupper kan du också hämta ytterligare information som användarens roller, hanterare och direktrapporter, ägda och registrerade enheter och hundratals andra datadelar som är tillgängliga i [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Mer än 15 miljoner organisationer och 90 % av 500 företag använder Azure AD när de prenumererar på Microsofts molntjänster som Office 365, Microsoft Azure, Enterprise Mobility Suite eller Microsoft 365. Du kan använda Microsoft Graph för att integrera din app med administrativa arbetsflöden, till exempel introduktions- (och uppsägning) medarbetarna, profilunderhåll med mera. 

Läs mer om hur du använder Microsoft Graph för etablering:

* [Startsida för Microsoft Graph](https://developer.microsoft.com/graph)

* [Översikt över Microsoft Graph](https://docs.microsoft.com/graph/overview)

* [Översikt över Microsoft Graph-auth](https://docs.microsoft.com/graph/auth/)

* [Komma igång med Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Använda SAML JIT för etablering

Om du bara vill etablera användare när du loggar in på ditt program och inte behöver avetablera användare automatiskt är SAML JIT ett alternativ. Ditt program måste ha stöd för SAML 2.0 som ett federationsprotokoll för att kunna använda SAML JIT.

SAML JIT använder anspråksinformationen i SAML-token för att skapa och uppdatera användarinformation i programmet. Kunder kan konfigurera dessa obligatoriska anspråk i Azure AD-programmet efter behov. Ibland måste JIT-etableringen aktiveras från programsidan så att kunden kan använda den här funktionen. SAML JIT är användbart för att skapa och uppdatera användare, men det kan inte ta bort eller inaktivera användarna i programmet.

## <a name="next-steps"></a>Efterföljande moment

* [Aktivera enkel inloggning för ditt program](../manage-apps/isv-sso-content.md)

* [Skicka in din programbeskrivning](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) och samarbeta med Microsoft för att skapa dokumentation på Microsofts webbplats.

* [Gå med i Microsoft Partner Network (gratis) och skapa din gå till marknadsplan](https://partner.microsoft.com/en-us/explore/commercial).
