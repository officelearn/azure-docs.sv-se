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
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986427"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Aktivera en Microsoft AppSource och Azure Marketplace lista med hjälp av Azure Active Directory

Microsoft Azure Active Directory (Azure AD) är en molnidentitetstjänst som möjliggör autentisering med ett Microsoft-konto genom att använda vanliga ramverk.  Läs mer om Azure AD, [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="benefits-of-using-azure-active-directory"></a>Fördelarna med att använda Azure Active Directory

Microsoft AppSource och Azure Marketplace-kunder använda i produkten upplevelser för att söka listan kataloger, vilket kräver att användaren att logga in till produkten.  Genom att integrera ditt program med Azure AD kan du påskynda engagement och optimera kundupplevelsen. Azure AD:

- Möjliggör enkel inloggning (SSO) för miljontals företagsanvändare.
- Möjliggör konsekvent användarens inloggning i program som publicerats av olika partners.
- Tillhandahåller skalbara och plattformsoberoende autentisering för mobil och molnet appar.

Enligt beskrivningen i avsnittet nedan krävs vissa erbjudanden för att implementera Azure AD för att publicera i Marketplace.

## <a name="azure-active-directory-requirements"></a>Krav för Azure Active Directory

Det finns olika [visa en lista över alternativ och erbjuder typer](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) för Microsoft AppSource och Azure Marketplace.  Azure AD-krav för dessa alternativ för kataloglista och typer av erbjudanden visas nedan:

| **Erbjudandetyp**    | **AAD-SSO krävs?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontakta mig | Utvärdering | Provkör | Transaktion |
| Virtuell dator | Gäller inte | Nej | Nej | Nej |
| Azure-appar (mall)  | Gäller inte | Saknas | Saknas | Gäller inte |
| Hanterade appar  | Gäller inte | Saknas | Gäller inte | Nej |
| SaaS  | Nej | Ja | Ja | Ja |
| Containrar  | Gäller inte | Saknas | Gäller inte | Nej |
| Konsulttjänster  | Nej | Gäller inte | Saknas | Gäller inte |

Läs mer om SaaS tekniska krav, [SaaS-program erbjuder publiceringsguide](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="integration-with-azure-active-directory"></a>Integrering med Azure Active Directory

Information om hur du integrerar med Azure AD för att aktivera enkel inloggning på https://aka.ms/aaddev.

Läs mer om Azure AD SSO [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)?

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Aktivera en utvärderingsversion lista med hjälp av Azure Active Directory

När en kund väljer din utvärderingsversion i Marketplace, omdirigeras kunderna till din utvärderingsmiljö. I din utvärderingsversion miljö, kan du ställa in din kund direkt utan att kräva ytterligare logga in-åtgärder. Din app eller erbjudandet får du en token från Azure AD under autentiseringen. Token innehåller värdefulla användarinformation som används för att skapa ett användarkonto i din app eller erbjudandet. Du kan automatisera kundens inställningar och öka sannolikheten för konvertering.

Läs mer om den token som skickas från Azure AD under autentiseringen [exempel token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Använd Azure AD för att aktivera ett klick i din app eller en utvärderingsversion. Azure AD ger följande fördelar: 
*   Effektivisera kundupplevelsen från Marketplace-utvärderingsversionen.
*   Underhålla känslan av en upplevelse i produkten, även om användaren omdirigeras från Marketplace till din domän- eller utvärderingsversion.
*   Minska sannolikheten att försämras vid omdirigering, eftersom det finns inga ytterligare åtgärder logga in.
*   Minska distribution hinder för stora befolkningen i Azure AD-användare.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Verifiera Azure AD-integrering i Marketplace: appar för flera innehavare
Använda Azure AD för att stödja följande alternativ för din lösning:
*   Registrera din app i butiker i Marketplace.
*   Aktivera stöd för multitenancy-funktionen i Azure AD för att få en testperiod för ett klick.

Läs mer om appregistrering [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Om du är nybörjare till med hjälp av Azure AD-federerad enkel inloggning (SSO), gör följande:
1.  Registrera din app i Marketplace. 
2.  Utveckla enkel inloggning med Azure AD med hjälp av OAuth 2.0 eller OpenID Connect.
    *   Läs mer om OAuth 2.0, [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Läs mer om att öppna ID Connect [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Aktivera stöd för multitenancy-funktionen i Azure AD för att tillhandahålla en utvärderingsmiljö med ett klick.
    
    Läs mer om AppSource-certifiering, [AppSource-certifiering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Verifiera Azure AD-integrering i Marketplace: appar för en enskild klient
Använda Azure AD till stöd för ett av följande alternativ för din enda klient-lösning: 
*   Lägga till användare i din katalog som gästanvändare med hjälp av Azure Active Directory B2B (Azure AD B2B). Läs mer om Azure AD B2B [vad är Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Installera manuellt utvärderingar för kunder med hjälp av kontakten mig publicera alternativet.
*   Utveckla en testenhet per kund.
*   Skapa en app för flera innehavare exempel demo som använder enkel inloggning.

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort det, 
- [Registrera](https://azuremarketplace.microsoft.com/sell) i marketplace.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig
- [Logga in på partnerportalen i molnet](https://cloudpartner.azure.com/) att skapa eller slutföra ditt erbjudande.

