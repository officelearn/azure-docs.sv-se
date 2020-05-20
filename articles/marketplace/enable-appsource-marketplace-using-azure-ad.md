---
title: Integrera ditt Microsoft Commercial Marketplace-erbjudande med Azure Active Directory
description: Använd Azure Active Directory för att autentisera din Microsoft AppSource och Azure Marketplace-erbjudanden.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 17cbfe92744ad96f2b5651b7e2f47a6443337068
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658039"
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

De olika [listorna över försäljnings platser och erbjudande typer](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) har olika krav för Azure AD-implementering. Se följande tabell för mer information.

| **Erbjudande typ**    | **Krävs Azure AD SSO?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontakta mig | Utvärdering | Test Drive | Transact |
| Virtuell dator | Ej tillämpligt | Inga | Inga | Inga |
| Azure-appar (lösnings mal len)  | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |
| Hanterade appar  | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Inga |
| SaaS  | Inga | Ja | Ja | Ja |
| Containrar  | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Inga |
| Rådgivningstjänster  | Inga | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |

Mer information om tekniska krav för SaaS finns i [publicerings guiden för SaaS-program](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Azure AD-integrering

- Information om hur du aktiverar enkel inloggning genom att integrera Azure AD i din lista finns [Azure Active Directory för utvecklare]( https://docs.microsoft.com/azure/active-directory/develop/).
- Information om enkel inloggning med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Aktivera en utvärderings lista

Automatiserad kund konfiguration kan öka sannolikheten för konvertering. När kunden väljer utvärderings listan och omdirigeras till din utvärderings miljö kan du ställa in kunden direkt utan att behöva ytterligare inloggnings steg.

Under autentiseringen skickar Azure AD en token till appen eller erbjudandet. Med hjälp av användar informationen från token kan du skapa ett användar konto i din app eller ett erbjudande. Läs mer i exempel- [token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

När du använder Azure AD för att aktivera autentisering med ett klick i appen eller utvärderings listan kan du:

- Effektivisera kund upplevelsen från Marketplace till din utvärderings lista.
- Upprätthålla en produkt upplevelse även när användaren omdirigeras från Marketplace till din domän eller utvärderings miljö.
- Minska sannolikheten för att överges när användare omdirigeras eftersom det inte finns några ytterligare inloggnings steg.
- Minska distributions barriärerna för den stora populationen av Azure AD-användare.

## <a name="verify-azure-ad-integration"></a>Verifiera Azure AD-integrering

### <a name="multitenant-solutions"></a>Lösningar för flera innehavare

Använd Azure AD för att stödja följande åtgärder:

- Registrera din app på en av Marketplace-butiker. Visa [app-registrering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) eller [AppSource-certifiering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) för mer information.
- Aktivera funktionen stöd för flera innehavare i Azure AD för att få en utvärderings version med ett klick.

Gör så här om du inte har använt Azure AD federerad enkel inloggning:

1. Registrera din app på Marketplace.
1. Utveckla SSO med Azure AD med hjälp av [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) eller [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Aktivera funktionen stöd för flera innehavare i Azure AD för att tillhandahålla en utvärderings version med ett klick.

### <a name="single-tenant-solutions"></a>Lösningar för enskild klient

Använd Azure AD för att stödja någon av följande åtgärder:

- Lägg till gäst användare i din katalog med hjälp av [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Konfigurera utvärderings versioner manuellt för kunder med hjälp av publicerings alternativet **kontakta mig** .
- Utveckla en test enhet per kund.
- Bygg en app med flera klient exempel demonstrationer som använder SSO.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Lär dig mer](https://azuremarketplace.microsoft.com/sell) om Marketplace.

Om du vill registrera dig i Partner Center börjar du med att skapa ett nytt erbjudande eller arbeta med en befintlig:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
