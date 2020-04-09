---
title: Lägga till app för flera data i Azure AD-programgalleriet
description: Förklarar hur du kan lista ditt specialutvecklade program med flera data i Azure AD-programgalleriet.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: 90f4d22ba782fd71460595f69a52e70be87a3047
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883261"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Lägga till ett program med flera erant i Azure AD-programgalleriet

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD-programgalleriet?

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst. [Azure AD-programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/) finns i Azure Marketplace App Store, där alla programkopplingar publiceras för enkel inloggning och användaretablering. Kunder som använder Azure AD som identitetsleverantör hittar de olika SaaS-programkopplingar som publiceras här. IT-administratörer lägger till kopplingar från appgalleriet och konfigurerar och använder sedan kopplingarna för enkel inloggning och etablering. Azure AD stöder alla större federationsprotokoll, inklusive SAML 2.0, OpenID Connect, OAuth och WS-Fed för enkel inloggning. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Om ditt program stöder SAML eller OpenIDConnect
Om du har ett program med flera följare som du vill ska visas i Azure AD-programgalleriet måste du först se till att ditt program stöder en av följande enstaka inloggningstekniker:

- **OpenID Connect**: Om du vill att din app ska visas skapar du multitenantprogrammet i Azure AD och implementerar [Azure AD-medgivanderamverket](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) för ditt program. Skicka inloggningsbegäran till en gemensam slutpunkt så att alla kunder kan ge samtycke till programmet. Du kan styra en användares åtkomst baserat på klient-ID och användarens UPN som tas emot i token. Skicka programmet med hjälp av processen som beskrivs i [Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Om ditt program stöder SAML 2.0 kan appen visas i galleriet. Följ instruktionerna i [Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Om ditt program inte stöder SAML eller OpenIDConnect
Program som inte stöder SAML eller OpenIDConnect kan fortfarande integreras i appgalleriet via lösenordsbaserad inloggningsteknik.

Med enkel inloggning för lösenord, även kallat lösenordsvalv, kan du hantera användaråtkomst och lösenord till webbprogram som inte stöder identitetsfederation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, till exempel till organisationens konton för sociala medier. 

Om du vill lista ditt program med den här tekniken:
1. Skapa ett webbprogram som har en HTML-inloggningssida för att konfigurera [lösenord enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Skicka begäran enligt beskrivningen i [Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Upptrappning

För alla eskaleringar skickar du e-post till [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) så återkommer vi till dig så snart som möjligt.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [listar ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
