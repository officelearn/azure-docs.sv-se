---
title: "Lägga till flera program i Azure AD application Gallery | Microsoft Docs"
description: Beskriver hur du kan ange flera anpassad utvecklat programmet i Azure AD application gallery.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: 82f7abbe5814f9b154b6888d5b599e7706eb879b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Lägga till flera program i Azure AD application Gallery

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD application gallery?

Azure Active Directory (AD Azure) är en molnbaserad identitetstjänst. Den [Azure AD application gallery](https://azure.microsoft.com/marketplace/active-directory/all/) i Azure Marketplace app store, där alla program kopplingar publiceras för enkel inloggning och användaretablering. Kunder som använder Azure AD som en identitetsleverantör hitta till olika SaaS-program kopplingar som publiceras här. IT-administratörer lägga till kopplingar från appgalleriet och sedan konfigurera och använda kopplingar för enkel inloggning och etablering. Azure AD stöder alla större federation protokoll, till exempel SAML 2.0, OpenID Connect, OAuth och WS-Fed för enkel inloggning. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Om programmet stöder SAML eller OpenIDConnect
Om du har en multitenant program som ska visas i Azure AD application gallery, måste du först kontrollera att ditt program stöder en av följande enkel inloggning tekniker:

- **OpenID Connect**: Om du vill att din app i listan, skapa flera program i Azure AD och genomföra den [Azure AD medgivande framework](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) för ditt program. Skicka inloggningsbegäran om till en gemensam slutpunkt så att en kund kan ge medgivande till programmet. Du kan kontrollera en användares åtkomst baserat på klient-ID och användarens UPN togs emot i token. Skicka programmet genom att använda processen som beskrivs i [visar en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: om programmet stöder SAML 2.0, appen kan visas i galleriet. Följ instruktionerna i [visar en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Om ditt program inte stöder SAML eller OpenIDConnect
Program som inte har stöd för SAML- eller OpenIDConnect kan fortfarande integreras i appgalleriet genom lösenord enkel inloggning teknik.

Lösenord för enkel inloggning, kallas även lösenordsvalv, gör att du kan hantera åtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto som din organisation sociala medier app konton. 

Om du vill visa ditt program med den här tekniken:
1. Skapa ett webbprogram som har en HTML-inloggningssida konfigurera [lösenord enkel inloggning](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Skicka förfrågan enligt beskrivningen i [visar en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskaleringar

Skicka e-post för alla eskaleringar [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) och vi kommer tillbaka till dig så snart som möjligt.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [visa ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
