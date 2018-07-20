---
title: Aktivera en utvärderingsversion på Azure Marketplace med hjälp av Azure Active Directory | Azure
description: Aktivera en utvärderingsversion listtyp med hjälp av Azure Active Directory i Azure Marketplace och AppSource för appen och tjänsten utgivare.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160475"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Aktivera en utvärderingsversion lista med hjälp av Azure Active Directory

Azure Active Directory (Azure AD) är en molnidentitetstjänst som möjliggör autentisering med ett Microsoft arbets- eller skolkonto genom att använda vanliga ramverk. Azure AD stöder OAuth och OpenID Connect-autentisering. Den [Azure Marketplace](https://azuremarketplace.microsoft.com) använder Azure AD för att autentisera dig och dina kunder.

Läs mer om Azure AD, [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

När en kund väljer din utvärderingsversion i Marketplace, omdirigeras kunderna till din utvärderingsmiljö. I din utvärderingsversion miljö, kan du ställa in din kund direkt utan att kräva ytterligare logga in-åtgärder. Din app eller erbjudandet får du en token från Azure AD under autentiseringen. Token innehåller värdefulla användarinformation som används för att skapa ett användarkonto i din app eller erbjudandet. Du kan automatisera kundens inställningar och öka sannolikheten för konvertering.

Läs mer om den token som skickas från Azure AD under autentiseringen [exempel token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Använd Azure AD för att aktivera ett klick i din app eller en utvärderingsversion. Azure AD ger följande fördelar: 
*   Effektivisera kundupplevelsen från Marketplace-utvärderingsversionen.
*   Underhålla känslan av en upplevelse i produkten, även om användaren omdirigeras från Marketplace till din domän- eller utvärderingsversion.
*   Minska sannolikheten att försämras vid omdirigering, eftersom det finns inga ytterligare åtgärder logga in.
*   Minska distribution hinder för stora befolkningen i Azure AD-användare.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Verifiera Azure AD-integrering i Marketplace: appar för flera innehavare
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

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Verifiera Azure AD-integrering i Marketplace: appar för en enskild klient
Använda Azure AD till stöd för ett av följande alternativ för din enda klient-lösning: 
*   Lägga till användare i din katalog som gästanvändare med hjälp av Azure Active Directory B2B (Azure AD B2B).
    
    Läs mer om Azure AD B2B [vad är Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Installera manuellt utvärderingar för kunder med hjälp av kontakten mig publicera alternativet.
*   Utveckla en testenhet per kund.
*   Skapa en multitenant demo-exempelapp som använder enkel inloggning.

## <a name="next-steps"></a>Nästa steg
*   Granska den [publiceringsguide för Azure Marketplace och AppSource](./marketplace-publishers-guide.md).
