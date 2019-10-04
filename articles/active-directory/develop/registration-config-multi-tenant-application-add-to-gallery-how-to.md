---
title: Lägg till ett program med flera klienter i program galleriet för Azure AD | Microsoft Docs
description: Förklarar hur du kan visa en lista över ditt anpassade program för flera innehavare i program galleriet för Azure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: def5dc82f8cbda93f6ac18f8a2af41d5c82ce5da
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936757"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Lägg till ett program med flera klienter i program galleriet för Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD-programgalleriet?

Azure Active Directory (Azure AD) är en molnbaserad identitets tjänst. [Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) -programgalleriet finns i App Store i Azure Marketplace där alla Application Connectors publiceras för enkel inloggning och användar etablering. Kunder som använder Azure AD som identitets leverantör hittar de olika SaaS Application Connectors som publiceras här. IT-administratörer lägger till anslutningar från App-galleriet och konfigurerar och använder sedan anslutningarna för enkel inloggning och etablering. Azure AD stöder alla större Federations protokoll, inklusive SAML 2,0, OpenID Connect, OAuth och WS-utfodras för enkel inloggning. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Om programmet har stöd för SAML eller OpenIDConnect
Om du har ett program för flera innehavare som du vill ska visas i Azure AD-programgalleriet, måste du först se till att programmet har stöd för någon av följande tekniker för enkel inloggning:

- **OpenID Connect**: Om du vill att din app ska visas skapar du ett program för flera innehavare i Azure AD och implementerar [Azure AD medgivande Framework](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) för ditt program. Skicka inloggningsbegäran till en gemensam slut punkt så att alla kunder kan ge sitt medgivande till programmet. Du kan styra en användares åtkomst baserat på klient-ID och användarens UPN som togs emot i token. Skicka programmet genom att använda processen som beskrivs i ange [ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Om ditt program stöder SAML 2,0, kan appen listas i galleriet. Följ anvisningarna i [att lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Om programmet inte har stöd för SAML eller OpenIDConnect
Program som inte stöder SAML eller OpenIDConnect kan fortfarande integreras i app-galleriet via lösen ords teknik för enkel inloggning.

Med enkel inloggning med lösen ord, även kallat lösen ords valv, kan du hantera användar åtkomst och lösen ord för webb program som inte stöder identitets Federation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, t. ex. till din organisations konton för sociala media. 

Om du vill lista ditt program med den här tekniken:
1. Skapa ett webb program som har en HTML-inloggnings sida för att konfigurera [enkel inloggning med lösen ord](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Skicka begäran enligt beskrivningen i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Förfrågningar

För alla eskaleringar skickar du e-post till [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) så kommer vi tillbaka till dig så snart som möjligt.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [visar ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
