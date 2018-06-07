---
title: Aktivera utvärderingsversion med Azure AD | Azure
description: Aktivera utvärderingsversion listtyp med Azure Active Directory (AD Azure) i Azure Marketplace och AppSource för appen och tjänsten utgivare
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825230"
---
# <a name="enable-trial-using-azure-ad"></a>Aktivera utvärderingsversion med Azure AD  
Azure Active Directory (AD Azure) är en molntjänst identitet som aktiverar autentisering med ett Microsoft arbets- eller skolkonto genom att använda branschstandardiserad ramverk: OAuth och OpenID Connect.  
*   Mer information om Azure AD finns i Azure Active Directory sidan finns på [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

Du och dina kunder autentiseras på marketplace med hjälp av Azure AD. När kunden väljer din utvärderingsversion lista på marketplace, omdirigeras kunden till din utvärderingsversion miljö.  I din utvärderingsversion miljö kan du ställa in kunden direkt utan att ytterligare logga in-åtgärder. Din app eller erbjudandet får du en token från Azure AD under autentiseringen som innehåller värdefulla användarinformation för att skapa ett användarkonto i din app eller erbjudandet. Du kan automatisera din installation och öka sannolikheten för konvertering.  
*   Mer information om den token som skickas från Azure AD under autentiseringen finns exempel token som finns i avsnittet [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

Använda Azure AD för att aktivera autentisering för ett klick i din app eller en utvärderingsversion.  
*   Förenkla kundupplevelsen från marketplace till test.  
*   Underhålla känslan av en i produkten, även om användaren omdirigeras från Marketplace till din domän eller utvärderingsversion miljö.  
*   Minska risken för nedläggning på omdirigering, eftersom det finns inga ytterligare åtgärder inloggning.  
*   Minska distribution hinder för stor del av Azure AD-användare.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Kontrollera din Azure AD-integrering på marketplace: Multitenant-appar  
Stöd följande alternativ för din lösning med hjälp av Azure AD.  
*   Registrera din app i skyltfönster på marketplace.  
*   Aktivera stöd för multitenancy-funktionen i Azure AD för att hämta en enkelklickning utvärderingsversionen.  
    *   Mer information om app-registreringen finns integrera program med Azure Active Directory sidan finns på [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Om du är nybörjare till med hjälp av Azure AD federerad enkel inloggning (SSO) och sedan följa dessa steg.  
1.  Registrera din app på marketplace. 
2.  Utveckla enkel inloggning med Azure AD med hjälp av OAuth 2.0- eller OpenID Connect.  
    *   Mer information om OAuth 2.0 finns i OAuth 2.0 sidan finns på [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Mer information om öppna ID Connect finns i OpenID Connect sidan finns på [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  Aktivera stöd för multitenancy-funktionen i Azure AD för att tillhandahålla en enkelklickning utvärderingsversionen.  
    *   Mer information om AppSource certifiering finns AppSource certifikatutfärdare sidan finns på [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Kontrollera din Azure AD-integrering på marketplace: stöd för en innehavare appar  
Stöd för något av följande alternativ för din lösning för en klient.  
*   Lägga till användare i katalogen som gästanvändare med hjälp av Azure AD B2B.  
    *   Mer information om Azure AD B2B finns vad är Azure AD B2B-samarbete sidan finns på [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Manuellt konfigurera försök för kunder som använder kontakta Me.  
*   Utveckla en testkörning per kund.  
*   Skapa en multitenant exempelapp demo med enkel inloggning.  

## <a name="next-steps"></a>Nästa steg
*   Besök den [Azure Marketplace och AppSource Publisher Guide](./marketplace-publishers-guide.md) sidan.  
 
---  

