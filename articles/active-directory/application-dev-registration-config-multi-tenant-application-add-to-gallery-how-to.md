---
title: Lägg till ett program för flera till Azure AD-programgalleriet | Microsoft Docs
description: Beskriver hur du kan visa dina anpassade program med flera klienter i Azure AD-programgalleriet.
services: active-directory
documentationCenter: na
author: barbkess
manager: mtillman
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 2f112a191770a2b469f4f8d1bce25973999f40d6
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366878"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Lägg till ett program för flera till Azure AD-programgalleriet

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD-programgalleriet?

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst. Den [Azure AD-programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/) är i Azure Marketplace app store, där publiceras alla anslutningsappar för programmet för enkel inloggning och etableringen av användare. Kunder som använder Azure AD som identitetsprovider hitta olika SaaS-programanslutningar publicerade här. IT-administratörer lägga till anslutningar från app-galleriet och sedan konfigurera och Använd kopplingar för enkel inloggning och etablering. Azure AD stöder alla större federation-protokoll, inklusive SAML 2.0, OpenID Connect, OAuth och WS-Fed för enkel inloggning. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Om ditt program har stöd för SAML- eller OpenIDConnect
Om du har ett program för flera som ska visas i Azure AD-programgalleriet, måste du först se till att ditt program har stöd för något av följande enkel inloggning för tekniker:

- **OpenID Connect**: för att få din app i listan ska du skapa program för flera klientorganisationer i Azure AD och implementera de [Azure AD-ramverket för medgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) för ditt program. Skicka inloggningsförfrågan till en gemensam slutpunkt så att alla kunder kan ge samtycke till programmet. Du kan styra en användares åtkomst baserat på klient-ID och användarens UPN tas emot i token. Skicka programmet med hjälp av den process som beskrivs i [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: om ditt program har stöd för SAML 2.0, appen kan visas i galleriet. Följ instruktionerna i [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Om ditt program inte stöder SAML eller OpenIDConnect
Program som inte har stöd för SAML- eller OpenIDConnect kan fortfarande integreras i appgalleriet tack vare lösenord för enkel inloggning tekniken.

Lösenord för enkel inloggning, även kallat lösenordsvalv, gör att du kan hantera användaråtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, till exempel till din organisations konton för sociala medier. 

Om du vill lista ditt program med den här tekniken:
1. Skapa ett webbprogram som har en HTML-inloggningssida konfigurera [lösenord för enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Skickar begäran enligt beskrivningen i [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Ökningar av

Alla ökningar av, skickar du e-postmeddelande till [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) och vi återkommer till dig så snart som möjligt.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
