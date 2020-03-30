---
title: Hantera klientprogram med Azure Active Directory | Microsoft Docs
description: I den här artikeln beskrivs fördelarna med att integrera Azure Active Directory med dina lokala, moln- och SaaS-program.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca73fac06649f801461e53130a67aa9ec0ad0d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063330"
---
# <a name="application-management-with-azure-active-directory"></a>Programhantering med Azure Active Directory

Azure Active Directory (Azure AD) förenklar hur du hanterar dina program genom att tillhandahålla ett enda identitetssystem för dina moln- och lokala appar. Du kan lägga till din programvara som en tjänst (SaaS) program, lokala program och branschappar (LOB) till Azure AD. Sedan loggar användarna in en gång för att få åtkomst till dessa program på ett säkert och smidigt sätt, tillsammans med Office 365 och andra företagsprogram från Microsoft. Du kan minska de administrativa kostnaderna genom [att automatisera etablering av användare](../app-provisioning/user-provisioning.md). Du kan också använda principer för multifaktorautentisering och villkorlig åtkomst för att ge säker programåtkomst.

![Diagram som visar appar som federerats via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Varför hantera program med en molnlösning?

Organisationer har ofta hundratals program som användarna behöver för att utföra sitt arbete. Användare använder dessa program på många enheter och platser. Nya program läggs till, utvecklas och tas ur bruk varje dag. Med så många program och åtkomstpunkter är det viktigare än någonsin att använda en molnbaserad lösning för att hantera användaråtkomst till alla program.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Vilka typer av program kan jag integrera med Azure AD?

Det finns fyra huvudtyper av program som du kan lägga till i dina **Enterprise-program** och hantera med Azure AD:

- **Azure AD Gallery-program** – Azure AD har ett galleri som innehåller tusentals program som har förintegrerats för enkel inloggning med Azure AD. Vissa av de program som din organisation använder finns förmodligen i galleriet. [Läs mer om hur du planerar appintegrering](plan-an-application-integration.md)eller få detaljerade integrationssteg för enskilda appar i [Självstudierna för SaaS-programmet](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Lokala program med programproxy** – Med Azure AD Application Proxy kan du integrera dina lokala webbappar med Azure AD för att stödja enkel inloggning. Sedan kan slutanvändare komma åt dina lokala webbappar på samma sätt som de får åtkomst till Office 365 och andra SaaS-appar. [Läs om varför du använder Programproxy och hur det fungerar](what-is-application-proxy.md).

- **Skräddarsydda program** – När du skapar egna affärsprogram kan du integrera dem med Azure AD för att stödja enkel inloggning. Genom att registrera ditt program med Azure AD har du kontroll över autentiseringsprincipen för programmet. Mer information finns i [vägledning för utvecklare](developer-guidance-for-integrating-applications.md).

- **Icke-Galleri applikationer** - Ta med egna program! Stöd för enkel inloggning för andra appar genom att lägga till dem i Azure AD. Du kan integrera vilken webblänk du vill, eller ett program som återger ett användarnamns- och lösenordsfält, stöder SAML- eller OpenID Connect-protokoll eller stöder SCIM. Mer information finns i [Konfigurera enkel inloggning för appar som inte är gallerier](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Hantera risker med principer för villkorlig åtkomst

Koppling av Azure AD enkel inloggning (SSO) med [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) ger hög säkerhetsnivå för åtkomst till program. Säkerhetsfunktioner omfattar identitetsskydd i molnskala, riskbaserad åtkomstkontroll, inbyggd multifaktorautentisering och principer för villkorlig åtkomst. De här funktionerna möjliggör detaljerade kontrollprinciper baserade på program eller på grupper som behöver högre säkerhet.

## <a name="improve-productivity-with-single-sign-on"></a>Förbättra produktiviteten med enkel inloggning

Aktivera enkel inloggning (SSO) för alla program och för Office 365 för bästa möjliga inloggning för befintliga användare genom färre eller inga inloggningsuppmaningar. Användarnas miljöer känns mer sammanhängande och mindre störande med färre uppmaningar och utan behov för flera lösenord. Affärsgruppen kan hantera och godkänna åtkomst via självbetjäning och dynamiska medlemskap. Då rätt personer i företaget hanterar åtkomst till program förbättras även säkerheten för ID-system.

Enkel inloggning ger bättre säkerhet. *Utan enkel inloggning* måste administratörer skapa och uppdatera användarkonton för varje enskilt program, vilket kräver tid. Användare måste dessutom hålla reda på flera autentiseringsuppgifter för att använda olika program. Därmed tenderar användarna att skriva ned sina lösenord eller använda andra lösningar för lösenordshantering, vilket medför datasäkerhetsrisker. [Läs mer om enkel inloggning](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Ta itu med styrning och efterlevnad

Med Azure AD kan du övervaka programinloggningar via rapporter som utnyttjar verktyg för säkerhetsinformation och händelsehantering (SIEM). Du kommer åt rapporterna i portalen eller med API:er. Granska programmatiskt vem som har åtkomst till dina program och ta bort åtkomst för inaktiva användare via åtkomstgranskningar.

## <a name="manage-costs"></a>Hantera kostnader

Genom att migrera till Azure AD kan du minska kostnaderna och slippa besväret med att hantera en lokal infrastruktur. Azure AD tillhandahåller även åtkomst till program via självbetjäning, vilket sparar tid för både administratörer och användare. Enkel inloggning eliminerar programspecifika lösenord. Den här möjligheten att logga in en enda gång sänker kostnaderna i samband med återställning av lösenord för program och förlorad produktivitet under hämtning av lösenord.

## <a name="next-steps"></a>Nästa steg

- [Vad är Application Proxy?](what-is-application-proxy.md)
- [Snabbstart: Lägga till ett galleriprogram i din Azure AD-klientorganisation](add-application-portal.md)
