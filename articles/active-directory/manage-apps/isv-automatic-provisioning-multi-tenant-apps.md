---
title: Aktivera automatisk användar etablering för Azure Active Directory program med flera klienter
description: En guide för oberoende program varu leverantörer för att aktivera automatisk etablering
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: c44006b20f4c0ef186f406e554ff555cda0c1dd8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373394"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Aktivera automatisk användar etablering för ditt program för flera klienter

Automatisk användar etablering är en process där du automatiserar genereringen, underhållet och borttagningen av användar identiteter i mål systemen, till exempel program vara som en tjänst.

## <a name="why-enable-automatic-user-provisioning"></a>Varför ska jag aktivera automatisk användar etablering?

Program som kräver att en användar post finns i programmet innan en användares första inloggning kräver användar etablering. Det finns fördelar med dig som en tjänste leverantör och förmåner till dina kunder.

### <a name="benefits-to-you-as-the-service-provider"></a>Fördelar till dig som tjänst leverantör

* Öka säkerheten för ditt program med hjälp av Microsoft Identity Platform.

* Sänk faktisk och uppfattad kund ansträngning för att anta ditt program.

* Minska kostnaderna i integrering med flera identitets leverantörer (IDP: er) för automatisk användar etablering med hjälp av system för SCIM-baserad etablering (Cross-Domain Identity Management).

* Minska support kostnaderna genom att tillhandahålla omfattande loggar som hjälper kunderna att felsöka problem med användar etablering.

* Öka synligheten för ditt program i [Azure AD App-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps).

* Få en prioriterad lista på sidan med program självstudier.

### <a name="benefits-to-your-customers"></a>Förmåner till dina kunder

* Öka säkerheten genom att automatiskt ta bort åtkomsten till programmet för användare som ändrar roller eller lämna organisationen till ditt program.

* Förenkla användar hanteringen för ditt program genom att undvika mänskligt fel och repetitivt arbete som är kopplat till manuell etablering.

* Sänk kostnaderna för att vara värd för och underhålla anpassade etablerings lösningar.

## <a name="choose-a-provisioning-method"></a>Välj en etablerings metod

Azure AD tillhandahåller flera integrerings vägar för att aktivera automatisk användar etablering för programmet.

* [Azure AD Provisioning-tjänsten](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) hanterar etableringen och avetableringen av användare från Azure AD till ditt program (utgående etablering) och från ditt program till Azure AD (inkommande etablering). Tjänsten ansluter till systemet med API-slutpunkter för användar hantering i SCIM (Cross-Domain Identity Management) som tillhandahålls av ditt program.

* När du använder [Microsoft Graph](https://docs.microsoft.com/graph/)hanterar ditt program inkommande och utgående etablering av användare och grupper från Azure AD till ditt program genom att fråga Microsoft Graph-API: et.

* Användar etableringen för Security Assertion Markup Language just-in-Time (SAML JIT) kan aktive ras om programmet använder SAML för federation. Den använder anspråk information som skickas i SAML-token för att etablera användare.

För att avgöra vilket integrations alternativ som ska användas för programmet, se jämförelse tabellen på hög nivå och se mer detaljerad information om varje alternativ.

| Funktioner som är aktiverade eller utökade genom automatisk etablering| Azure AD Provisioning-tjänst (SCIM 2,0)| Microsoft Graph API (OData v 4.0)| SAML JIT |
|---|---|---|---|
| Hantering av användare och grupper i Azure AD| √| √| Endast användare |
| Hantera användare och grupper som synkroniseras från lokala Active Directory| √| √| Endast användare * |
| Få åtkomst till data utanför användare och grupper under etablerings åtkomst till O365-data (team, SharePoint, e-post, kalender, dokument osv.)| X +| √| X |
| Skapa, läsa och uppdatera användare baserat på affärs regler| √| √| √ |
| Ta bort användare baserat på affärs regler| √| √| X |
| Hantera automatisk användar etablering för alla program från Azure Portal| √| X| √ |
| Stöd för flera identitets leverantörer| √| X| √ |
| Stöd gäst konton (B2B)| √| √| √ |
| Stöd för icke-Enterprise-konton (B2C)| X| √| √ |

<sup>*</sup> – Azure AD Connect installations programmet krävs för att synkronisera användare från AD till Azure AD.  
<sup>+</sup >– genom att använda scim för etablering förhindrar du att du integrerar ditt program med Microsoft Graph i andra syfte.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD Provisioning-tjänst (SCIM)

Azure AD Provisioning Services använder [SCIM] (https://aka.ms/SCIMOverview, ett protokoll som stöds av många identitets leverantörer (IDP: er). Vi rekommenderar att du använder Azure AD Provisioning-tjänsten om du vill stödja IDP: er utöver Azure AD, eftersom alla SCIM-kompatibla IdP kan ansluta till din SCIM-slutpunkt.

Mer information om hur Azure AD Provisioning-tjänsten användare SCIM finns i: 

* [Läs mer om SCIM-standarden](https://aka.ms/SCIMOverview)

* [Använda systemet för SCIM (Cross-Domain Identity Management) för att automatiskt etablera användare och grupper från Azure Active Directory till program](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

* [Förstå Azure AD SCIM-implementeringen](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph för etablering

När du använder Microsoft Graph för etablering har du till gång till alla omfattande användar data som är tillgängliga i Graph. Förutom information om användare och grupper kan du också hämta ytterligare information, t. ex. användarens roller, chef och direkt rapporter, ägda och registrerade enheter och hundratals andra data delar som är tillgängliga i [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Fler än 15 000 000 organisationer och 90% av Fortune 500-företag använder Azure AD för att prenumerera på Microsofts moln tjänster som Office 365, Microsoft Azure, Enterprise Mobility Suite eller Microsoft 365. Du kan använda Microsoft Graph för att integrera din app med administrativa arbets flöden, till exempel medarbetarnas onboarding (och terminering), profil underhåll med mera. 

Läs mer om hur du använder Microsoft Graph för etablering:

* [Microsoft Graph start sida](https://developer.microsoft.com/graph)

* [Översikt över Microsoft Graph](https://docs.microsoft.com/graph/overview)

* [Översikt över Microsoft Graph auth](https://docs.microsoft.com/graph/auth/)

* [Komma igång med Microsoft Graph](https://developer.microsoft.com/graph/get-started)

* [Använd Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)

* [Microsoft Graph Explorer](https://aka.ms/ge) 

* [Referens för Microsoft Graph behörigheter](https://docs.microsoft.com/graph/permissions-reference)

* [Metod tips för att arbeta med Microsoft Graph](https://docs.microsoft.com/graph/best-practices-concept)

* [Aktuella scenarier för Microsoft Graph](https://developer.microsoft.com/graph/examples)

## <a name="using-saml-jit-for-provisioning"></a>Använda SAML JIT för etablering

Om du bara vill etablera användare vid första inloggningen till ditt program och inte behöver avetablera användare automatiskt, är SAML JIT ett alternativ. Ditt program måste ha stöd för SAML 2,0 som ett Federations protokoll för att använda SAML JIT.

SAML JIT använder anspråks informationen i SAML-token för att skapa och uppdatera användar information i programmet. Kunder kan konfigurera dessa nödvändiga anspråk i Azure AD-programmet efter behov. Ibland måste JIT-etableringen aktive ras från program sidan så att kunden kan använda den här funktionen. SAML JIT är användbart för att skapa och uppdatera användare, men det går inte att ta bort eller inaktivera användare i programmet.

## <a name="next-steps"></a>Nästa steg

* [Aktivera enkel inloggning för ditt program](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-sso-content)

* [Skicka in din program lista](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) och partner med Microsoft för att skapa dokumentation på Microsofts webbplats.

* [Gå med i Microsoft Partner Network (kostnads fri) och skapa en marknads plan](https://partner.microsoft.com/en-us/explore/commercial).
