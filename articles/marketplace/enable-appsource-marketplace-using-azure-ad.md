---
title: Aktivera en Microsoft AppSource- och Azure Marketplace-lista med hjälp av Azure Active Directory | Azure
description: Aktivera en listtyp med hjälp av Azure Active Directory i Azure Marketplace och AppSource för app- och tjänstutgivare.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: d2f33fc8b1bdd15b53ad9130b5cc598dd6d5ee1a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684630"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Aktivera en AppSource- och Marketplace-lista genom att använda Azure Active Directory

 Azure Active Directory (Azure AD) är en molnidentitetstjänst som möjliggör autentisering med ett Microsoft-konto. Azure AD använder branschstandardramverk. [Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Azure AD-fördelar

Microsoft AppSource- och Azure Marketplace-kunder använder produktupplevelser för att söka i listkatalogerna. Dessa åtgärder kräver att kunderna loggar in på produkten. Azure AD-integrering ger följande fördelar:

- Snabbare engagemang och en optimerad kundupplevelse
- Enkel inloggning (SSO) för miljontals företagsanvändare
- Konsekvent inloggningsupplevelse över program som publiceras av olika partner
- Skalbar autentisering över flera plattformar för mobila appar och molnappar

## <a name="offers-that-require-azure-ad"></a>Erbjudanden som kräver Azure AD

De olika [listningsalternativen och erbjudandetyperna](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) för AppSource och Azure Marketplace har olika krav för Azure AD-implementering. Mer information finns i följande tabell:

| **Typ av erbjudande**    | **Azure AD SSO krävs?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontakta mig | Utvärdering | Test Drive | Transact |
| Virtuell dator | Ej tillämpligt | Inga | Inga | Inga |
| Azure Apps (lösningsmall)  | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |
| Hanterade appar  | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Inga |
| SaaS  | Inga | Ja | Ja | Ja |
| Containrar  | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Inga |
| Rådgivningstjänster  | Inga | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |

Mer information om SaaS tekniska krav finns i [SaaS-program Offer Publishing Guide](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Azure AD-integrering

- Information om hur du aktiverar enkel inloggning genom att integrera Azure AD i din lista finns i [Azure Active Directory för utvecklare]( https://aka.ms/aaddev).
- Information om azure AD-enkel inloggning finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Aktivera en utvärderingsversion

Automatiserad kundinställning kan öka sannolikheten för konvertering. När kunden väljer provinformation och omdirigeras till utvärderingsmiljön kan du konfigurera kunden direkt utan att kräva ytterligare inloggningssteg.

Under autentiseringen skickar Azure AD en token till din app eller ditt erbjudande. Användarinformationen som tillhandahålls av token gör det möjligt att skapa ett användarkonto i din app eller ditt erbjudande. Mer information finns i [Exempeltoken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

När du använder Azure AD för att aktivera autentisering med ett klick i din app eller utvärderingsversion får du:

- Effektivisera kundupplevelsen från Marketplace till dina utvärderingsuppgifter.
- Behåll känslan av en produktupplevelse även när användaren omdirigeras från Marketplace till din domän eller utvärderingsmiljö.
- Minska risken för nedläggning när användare omdirigeras eftersom det inte finns några ytterligare inloggningssteg.
- Minska distributionshinder för den stora populationen av Azure AD-användare.

## <a name="verify-azure-ad-integration"></a>Verifiera Azure AD-integrering

### <a name="multitenant-solutions"></a>Lösningar för flera trogna

Använd Azure AD för att stödja följande åtgärder:

- Registrera din app i ett av Marketplace-skyltfönster. Visa [appregistrering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) eller [AppSource-certifiering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) för mer information.
- Aktivera supportfunktionen för flera funktioner i Azure AD för att få en utvärderingsupplevelse med ett klick.

Om du inte har använt Azure AD-federerad enkel inloggning gör du så här:

1. Registrera din app på Marketplace.
1. Utveckla SSO med Azure AD med [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) eller [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Aktivera multitenancy-supportfunktionen i Azure AD för att ge en utvärderingsupplevelse med ett klick.

### <a name="single-tenant-solutions"></a>Lösningar för en enda klient

Använd Azure AD för att stödja någon av följande åtgärder:

- Lägg till gästanvändare i din katalog med hjälp av [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Konfigurera utvärderingsversioner manuellt för kunder med hjälp av alternativet **Kontakta mig.**
- Utveckla en testkörning per kund.
- Skapa en exempeldemoapp för flera innehavare som använder SSO.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, 

- [Läs mer](https://azuremarketplace.microsoft.com/sell) om marknadsplatsen.

Om du vill registrera dig i Partner center börjar du skapa ett nytt erbjudande eller arbetar på ett befintligt:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra erbjudandet.
