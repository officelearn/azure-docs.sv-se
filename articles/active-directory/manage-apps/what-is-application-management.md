---
title: Hantera klientprogram med Azure Active Directory | Microsoft Docs
description: Den här artikeln beskriver fördelarna med att integrera Azure Active Directory med din lokala, moln och SaaS-program.
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
ms.openlocfilehash: 9246d7bd48579def171986606e88c09593029aa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108158"
---
# <a name="application-management-with-azure-active-directory"></a>Programhantering med Azure Active Directory

Azure Active Directory (Azure AD) blir det enklare för hur du hanterar dina program genom att tillhandahålla en enda identitetssystem för molnet och lokala appar. Du kan lägga till din programvara som en tjänst (SaaS)-program, lokala program och branschspecifika (LOB) appar till Azure AD. Användare logga sedan in en gång på ett säkert och smidigt kan du komma åt dessa program, tillsammans med Office 365 och andra affärsapplikationer från Microsoft. Du kan minska administrativa kostnader genom att automatisera etableringen av användare. Du kan också använda multifaktorautentisering och principer för villkorlig åtkomst för att ge säker åtkomst.

![Appar som federeras via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Varför hantera program med en molnlösning?

Organisationer har ofta hundratals program som användarna behöver för att utföra sitt arbete. Användare använder dessa program på många enheter och platser. Nya program läggs till, utvecklas och tas ur bruk varje dag. Med så många program och åtkomstpunkter är det mer kritiska än någonsin att använda en molnbaserad lösning för att hantera åtkomst till alla program.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Vilka typer av program kan integreras med Azure AD?
Det finns fyra huvudsakliga typer av program som du kan lägga till din **företagsprogram** och hantera med Azure AD:

-   **Azure AD-galleriprogram** – Azure AD har ett galleri som innehåller tusentals program som är förintegrerade för enkel inloggning med Azure AD. Vissa av de program som din organisation använder finns förmodligen i galleriet. [Läs om hur du planerar din appintegrering](plan-an-application-integration.md), eller hämta detaljerade integreringen för enskilda appar i den [självstudier för SaaS-program](https://docs.microsoft.com/azure/active-directory/saas-apps/). 

-   **Lokala program med Application Proxy** – med Azure AD Application Proxy kan du integrera dina lokala webbprogram med Azure AD för enkel inloggning. Slutanvändare kan sedan komma åt dina lokala webbprogram på samma sätt som de har åtkomst till Office 365 och andra SaaS-appar. [Lär dig varför ska man använda Application Proxy och hur det fungerar](what-is-application-proxy.md).

-   **Egenutvecklade program** – när du skapar dina egna line-of-business-program, kan du integrera dem med Azure AD för enkel inloggning. Du har kontroll över autentiseringsprincip för programmet genom att registrera ditt program med Azure AD. Mer information finns i [vägledning för utvecklare](developer-guidance-for-integrating-applications.md).

-   **Icke-galleriprogram** – Använd dina egna program! Stöd för enkel inloggning för andra appar genom att lägga till dem till Azure AD. Du kan integrera alla webblänk som du vill eller alla program som återger ett fält för användarnamn och lösenord, har stöd för SAML- eller OpenID Connect-protokoll eller stöder SCIM. Mer information finns i [Konfigurera enkel inloggning för appar som inte är ett galleriprogram](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Hantera risker med principer för villkorlig åtkomst
Koppling Azure AD enkel inloggning (SSO) med [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) ger hög säkerhet för åtkomst till program. Säkerhetsfunktioner molnskala identitetsskydd, riskbaserad åtkomstkontroll, interna multifaktorautentisering och principer för villkorlig åtkomst. De här funktionerna möjliggör detaljerade kontrollprinciper baserade på program eller på grupper som behöver högre säkerhet.

## <a name="improve-productivity-with-single-sign-on"></a>Förbättra produktiviteten med enkel inloggning
Aktivera enkel inloggning (SSO) för alla program och för Office 365 för bästa möjliga inloggning för befintliga användare genom färre eller inga inloggningsuppmaningar. Användarnas miljöer känns mer sammanhängande och mindre störande med färre uppmaningar och utan behov för flera lösenord. Affärsgruppen kan hantera och godkänna åtkomst via självbetjäning och dynamiska medlemskap. Då rätt personer i företaget hanterar åtkomst till program förbättras även säkerheten för ID-system.

Enkel inloggning ger bättre säkerhet. *Utan enkel inloggning* måste administratörer skapa och uppdatera användarkonton för varje enskilt program, vilket kräver tid. Användare måste dessutom hålla reda på flera autentiseringsuppgifter för att använda olika program. Därmed tenderar användarna att skriva ned sina lösenord eller använda andra lösningar för lösenordshantering, vilket medför datasäkerhetsrisker. [Läs mer om enkel inloggning](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Ta itu med styrning och efterlevnad
Med Azure AD kan du övervaka programinloggningar via rapporter som utnyttjar verktyg för säkerhetsinformation och händelsehantering (SIEM). Du kommer åt rapporterna i portalen eller med API:er. Granska programmatiskt vem som har åtkomst till dina program och ta bort åtkomst för inaktiva användare via åtkomstgranskningar.

## <a name="manage-costs"></a>Hantera kostnader
Genom att migrera till Azure AD kan du minska kostnaderna och slippa besväret med att hantera en lokal infrastruktur. Azure AD tillhandahåller även åtkomst till program via självbetjäning, vilket sparar tid för både administratörer och användare. Enkel inloggning eliminerar programspecifika lösenord. Den här möjligheten att logga in en enda gång sänker kostnaderna i samband med återställning av lösenord för program och förlorad produktivitet under hämtning av lösenord.

## <a name="next-steps"></a>Nästa steg

- [Vad är Application Proxy?](what-is-application-proxy.md)
- [Snabbstart: Lägga till ett galleriprogram till Azure AD-klienten](add-application-portal.md)
