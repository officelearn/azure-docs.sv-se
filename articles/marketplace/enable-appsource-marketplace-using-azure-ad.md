---
title: Integrera ditt Microsoft Commercial Marketplace-erbjudande med Azure Active Directory
description: Använd Azure Active Directory för att autentisera din Microsoft AppSource och Azure Marketplace-erbjudanden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: 4c700a61de80968b17585faf92e268fef8d86f0e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323272"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integrera din handels Marketplace-lista med Azure Active Directory

 I den här artikeln beskrivs kraven för att integrera en lista över handels platser eller erbjudanden med Azure Active Directory (Azure AD). Azure AD är en moln identitets tjänst som använder bransch standard ramverk för att aktivera autentisering med en Microsoft-konto. [Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Fördelar med Azure AD

Microsoft AppSource och Azure Marketplace-kunder använder produkt upplevelser i butik för att söka efter kataloger i-listan. De här åtgärderna kräver att kunderna loggar in på produkten. Azure AD-integrering ger följande fördelar:

- Snabbare engagemang och en optimerad kund upplevelse
- Enkel inloggning (SSO) för miljon tals företags användare
- Konsekvent inloggnings upplevelse över program som publiceras av olika partners
- Skalbar, plattforms oberoende autentisering för mobila appar och molnappar

## <a name="offers-that-require-azure-ad"></a>Erbjudanden som kräver Azure AD

De olika [listorna över försäljnings platser och erbjudande typer](determine-your-listing-type.md) har olika krav för Azure AD-implementering. Se följande tabell för mer information.

| Erbjudandetyp    | Behöver du ringa till Azure AD SSO för att kontakta mig?  | Azure AD SSO krävs för utvärderings versionen? | Vill du att Azure AD SSO krävs för test enheten?  | Azure AD SSO krävs för Transact |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Virtuell dator | E.t. | Nej | Nej | Nej |
| Azure-appar (lösnings mal len)  | E.t. | E.t. | E.t. | E.t. |
| Hanterade appar  | E.t. | E.t. | E.t. | Nej |
| SaaS  | Nej | Ja | Ja | Ja |
| Containrar  | E.t. | E.t. | E.t. | Nej |
| Rådgivningstjänster  | Nej | E.t. | E.t. | E.t. |

Mer information om tekniska krav för SaaS finns [i Azure AD och transactable SaaS-erbjudanden på den kommersiella marknaden](./azure-ad-saas.md).

## <a name="azure-ad-integration"></a>Azure AD-integrering

- Mer information om hur du integrerar Azure AD för den program vara som en tjänst (SaaS) erbjuder finns i [Azure AD och transactable SaaS-erbjudanden på den kommersiella marknaden](./azure-ad-saas.md).
- Information om hur du aktiverar enkel inloggning genom att integrera Azure AD i din lista finns [Azure Active Directory för utvecklare](../active-directory/develop/index.yml).
- Information om enkel inloggning med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md).

## <a name="enable-a-trial-listing"></a>Aktivera en utvärderings lista

Automatiserad kund konfiguration kan öka sannolikheten för konvertering. När kunden väljer utvärderings listan och omdirigeras till din utvärderings miljö kan du ställa in kunden direkt utan att behöva ytterligare inloggnings steg.

Under autentiseringen skickar Azure AD en token till appen eller erbjudandet. Med hjälp av användar informationen från token kan du skapa ett användar konto i din app eller ett erbjudande. Läs mer i exempel- [token](../active-directory/develop/id-tokens.md).

När du använder Azure AD för att aktivera autentisering med ett klick i appen eller utvärderings listan kan du:

- Effektivisera kund upplevelsen från Marketplace till din utvärderings lista.
- Upprätthålla en produkt upplevelse även när användaren omdirigeras från Marketplace till din domän eller utvärderings miljö.
- Minska sannolikheten för att överges när användare omdirigeras eftersom det inte finns några ytterligare inloggnings steg.
- Minska distributions barriärerna för den stora populationen av Azure AD-användare.

## <a name="verify-azure-ad-integration"></a>Verifiera Azure AD-integrering

### <a name="multitenant-solutions"></a>Lösningar för flera innehavare

Använd Azure AD för att stödja följande åtgärder:

- Registrera din app på en av Marketplace-butiker. Visa [app-registrering](../active-directory/develop/quickstart-register-app.md) eller [AppSource-certifiering](../active-directory/azuread-dev/howto-get-appsource-certified.md) för mer information.
- Aktivera funktionen stöd för flera innehavare i Azure AD för att få en utvärderings version med ett klick.

Gör så här om du inte har använt Azure AD federerad enkel inloggning:

1. Registrera din app på Marketplace.
1. Utveckla SSO med Azure AD med hjälp av [OAuth 2,0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) eller [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Aktivera funktionen stöd för flera innehavare i Azure AD för att tillhandahålla en utvärderings version med ett klick.

### <a name="single-tenant-solutions"></a>Lösningar för enskild klient

Använd Azure AD för att stödja någon av följande åtgärder:

- Lägg till gäst användare i din katalog med hjälp av [Azure AD B2B](../active-directory/b2b/what-is-b2b.md).
- Konfigurera utvärderings versioner manuellt för kunder med hjälp av publicerings alternativet **kontakta mig** .
- Utveckla en test enhet per kund.
- Bygg en app med flera klient exempel demonstrationer som använder SSO.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Lär dig mer](https://azuremarketplace.microsoft.com/sell) om Marketplace.

Om du vill registrera dig i Partner Center börjar du med att skapa ett nytt erbjudande eller arbeta med en befintlig:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
