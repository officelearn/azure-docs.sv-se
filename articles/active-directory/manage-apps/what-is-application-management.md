---
title: Hantera klientprogram med Azure Active Directory | Microsoft Docs
description: I den här artikeln beskrivs fördelarna med att integrera Azure Active Directory med dina lokala, molnbaserade och SaaS-program.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f826f5cc3e56dcf88ee110265724779a9d1f624
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84762929"
---
# <a name="application-management-with-azure-active-directory"></a>Programhantering med Azure Active Directory

Azure Active Directory (Azure AD) fören klar hanteringen av dina program genom att tillhandahålla ett enda identitets system för dina molnbaserade och lokala appar. Du kan lägga till SaaS-program (program vara som en tjänst), lokala program och LOB-appar (Line of Business) till Azure AD. Användarna loggar sedan in en gång för att på ett säkert och smidigt sätt komma åt dessa program, tillsammans med Office 365 och andra företags program från Microsoft. Du kan minska administrativa kostnader genom att [Automatisera användar etableringen](../app-provisioning/user-provisioning.md). Du kan också använda Multi-Factor Authentication och principer för villkorlig åtkomst för att ge säker åtkomst till programmet.

![Diagram som visar federerade appar via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Varför hantera program med en molnlösning?

Organisationer har ofta hundratals program som användarna behöver för att utföra sitt arbete. Användare använder dessa program på många enheter och platser. Nya program läggs till, utvecklas och tas ur bruk varje dag. Med så många program och åtkomst punkter är det viktigare än någonsin att använda en molnbaserad lösning för att hantera användar åtkomst till alla program.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Vilka typer av program kan jag integrera med Azure AD?

Det finns fyra huvud typer av program som du kan lägga till i dina **företags program** och hantera med Azure AD:

- **Azure AD Gallery-program** – Azure AD har ett galleri som innehåller tusentals program som har integrerats för enkel inloggning med Azure AD. Vissa av de program som din organisation använder finns förmodligen i galleriet. [Lär dig mer om att planera din program integrering](plan-an-application-integration.md)eller få detaljerade integrerings steg för enskilda appar i [självstudierna för SaaS-program](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Lokala program med programproxy** – med Azure AD-programproxy kan du integrera dina lokala webbappar med Azure AD för att stödja enkel inloggning. Sedan kan slutanvändarna komma åt dina lokala webbappar på samma sätt som de kommer åt Office 365 och andra SaaS-appar. [Lär dig varför du ska använda Application Proxy och hur det fungerar](what-is-application-proxy.md).

- **Anpassade program** – när du skapar dina egna affärs program kan du integrera dem med Azure AD för att stödja enkel inloggning. Genom att registrera ditt program med Azure AD har du kontroll över autentiseringsprincip för programmet. Mer information finns i [vägledning för utvecklare](developer-guidance-for-integrating-applications.md).

- **Program som inte är gallerier** – ta med dina egna program! Stöd för enkel inloggning för andra appar genom att lägga till dem i Azure AD. Du kan integrera valfri webblänk eller ett program som återger ett användar namn och ett lösen ords fält, stöder SAML-eller OpenID Connect-protokoll eller stöder SCIM. Mer information finns i [Konfigurera enkel inloggning för appar som inte är gallerier](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Hantera risker med principer för villkorlig åtkomst

Koppling av enkel inloggning med Azure AD (SSO) med [villkorlig åtkomst](../conditional-access/concept-conditional-access-cloud-apps.md) ger hög säkerhet för åtkomst till program. Säkerhetsfunktionerna omfattar moln skalning av identitets skydd, riskfylld åtkomst kontroll, inbyggd Multi-Factor Authentication och principer för villkorlig åtkomst. De här funktionerna möjliggör detaljerade kontrollprinciper baserade på program eller på grupper som behöver högre säkerhet.

## <a name="improve-productivity-with-single-sign-on"></a>Förbättra produktiviteten med enkel inloggning

Aktivera enkel inloggning (SSO) för alla program och för Office 365 för bästa möjliga inloggning för befintliga användare genom färre eller inga inloggningsuppmaningar. Användarnas miljöer känns mer sammanhängande och mindre störande med färre uppmaningar och utan behov för flera lösenord. Affärsgruppen kan hantera och godkänna åtkomst via självbetjäning och dynamiska medlemskap. Då rätt personer i företaget hanterar åtkomst till program förbättras även säkerheten för ID-system.

Enkel inloggning ger bättre säkerhet. *Utan enkel inloggning* måste administratörer skapa och uppdatera användarkonton för varje enskilt program, vilket kräver tid. Användare måste dessutom hålla reda på flera autentiseringsuppgifter för att använda olika program. Därmed tenderar användarna att skriva ned sina lösenord eller använda andra lösningar för lösenordshantering, vilket medför datasäkerhetsrisker. [Läs mer om enkel inloggning](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Ta itu med styrning och efterlevnad

Med Azure AD kan du övervaka programinloggningar via rapporter som utnyttjar verktyg för säkerhetsinformation och händelsehantering (SIEM). Du kommer åt rapporterna i portalen eller med API:er. Granska programmatiskt vem som har åtkomst till dina program och ta bort åtkomst för inaktiva användare via åtkomstgranskningar.

## <a name="manage-costs"></a>Hantera kostnader

Genom att migrera till Azure AD kan du minska kostnaderna och slippa besväret med att hantera en lokal infrastruktur. Azure AD tillhandahåller även åtkomst till program via självbetjäning, vilket sparar tid för både administratörer och användare. Enkel inloggning eliminerar programspecifika lösenord. Den här möjligheten att logga in en enda gång sänker kostnaderna i samband med återställning av lösenord för program och förlorad produktivitet under hämtning av lösenord.

## <a name="next-steps"></a>Nästa steg

- [Vad är Application Proxy?](what-is-application-proxy.md)
- [Snabb start: lägga till ett galleri program i Azure AD-klienten](add-application-portal.md)
