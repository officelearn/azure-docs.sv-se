---
title: Aktivera en Microsoft AppSource och Azure Marketplace lista med hjälp av Azure Active Directory | Azure
description: Aktivera en lista typ med hjälp av Azure Active Directory i Azure Marketplace och AppSource för appen och tjänsten utgivare.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 3ad9308f3bc714ee2877627da8fdb328459b9fe4
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351957"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Aktivera en AppSource och Marketplace-lista med hjälp av Azure Active Directory

 Azure Active Directory (Azure AD) är en molnidentitetstjänst som möjliggör autentisering med ett Microsoft-konto. Azure AD använder vanliga ramverk. [Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Fördelarna med Azure AD

Microsoft AppSource och Azure Marketplace-kunder använda i produkten upplevelser för att söka igenom listan kataloger. Dessa åtgärder kräver kunder att logga in på produkten. Azure AD-integrering ger följande fördelar:

- Snabbare engagemanget och en optimerad kundupplevelse
- Enkel inloggning (SSO) för miljontals företagsanvändare
- Enhetlig inloggning i program som publiceras av olika partners
- Skalbar, plattformsöverskridande autentisering för mobila enheter och appar i molnet

## <a name="offers-that-require-azure-ad"></a>Erbjudanden som kräver Azure AD

De olika [visa en lista över alternativ och erbjuder typer](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) för AppSource och Azure Marketplace har olika krav för Azure AD-implementering. Se tabellen nedan för mer information:

| **Erbjudandetyp**    | **Azure AD enkel inloggning krävs?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontakta mig | Utvärdering | Test Drive | Transaktion |
| Virtuell dator | Gäller inte | Nej | Nej | Nej |
| Azure-appar (mall)  | Gäller inte | Saknas | Saknas | Gäller inte |
| Hanterade appar  | Gäller inte | Saknas | Gäller inte | Nej |
| SaaS  | Nej | Ja | Ja | Ja |
| Containrar  | Gäller inte | Saknas | Gäller inte | Nej |
| Konsulttjänster  | Nej | Gäller inte | Saknas | Gäller inte |

Läs mer om SaaS tekniska krav, [SaaS-program erbjuder publiceringsguide](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Azure AD-integrering

- Information om hur du aktiverar enkel inloggning genom att integrera Azure AD i din registrering finns i [Azure Active Directory för utvecklare]( https://aka.ms/aaddev).
- Om du vill få information om Azure AD enkel inloggning, se [vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Aktivera en utvärderingsversion lista

Automatiserad kundens inställningar kan öka sannolikheten för konvertering. När kunden väljer din utvärderingsversion registrering och omdirigeras till din utvärderingsmiljö kan konfigurera du kunden direkt utan att kräva ytterligare logga in-åtgärder.

Under autentiseringen skickar en token till din app eller ett erbjudande i Azure AD. Användarinformation från token används för att skapa ett användarkonto i din app eller erbjudandet. Mer information finns i [exempel token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

När du använder Azure AD för att aktivera ett klick i din app eller en utvärderingsversion lista du:

- Effektivisera kundupplevelsen från Marketplace till din utvärderingsversion notering.
- Underhålla känslan av en i produkten upplevelse även om användaren omdirigeras till din domän- eller utvärderingsversion från Marketplace.
- Minska sannolikheten för nedläggning när användare omdirigeras eftersom det finns inga ytterligare åtgärder logga in.
- Minska distribution hinder för stora befolkningen i Azure AD-användare.

## <a name="verify-azure-ad-integration"></a>Verifiera Azure AD-integrering

### <a name="multitenant-solutions"></a>Lösningar för flera innehavare

Använda Azure AD för att stödja följande åtgärder:

- Registrera din app i en av molnmarknadsplatsen. Visa [appregistrering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) eller [AppSource-certifiering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) för mer information.
- Aktivera stöd för multitenancy-funktionen i Azure AD för att få en testperiod för ett klick.

Om du är van vid att använda Azure AD-federerad enkel inloggning kan du göra följande:

1. Registrera din app i Marketplace.
1. Utveckla enkel inloggning med Azure AD med hjälp av [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) eller [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Aktivera stöd för multitenancy-funktionen i Azure AD för att tillhandahålla en utvärderingsmiljö med ett klick.

### <a name="single-tenant-solutions"></a>Lösningar för enstaka klientorganisationer

Använd Azure AD för att stöd för någon av följande åtgärder:

- Lägga till gästanvändare i katalogen med hjälp av [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Manuellt konfigurera utvärderingar för kunder med hjälp av den **kontakta mig** publicering av alternativet.
- Utveckla en testenhet per kund.
- Skapa en app för flera innehavare exempel demo som använder enkel inloggning.

## <a name="next-steps"></a>Nästa steg

- Kontrollera att du har [registrerad i Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/) att skapa eller slutföra ditt erbjudande.
