---
title: "Hur du lägger till ett program för flera innehavare i Azure AD application Gallery | Microsoft Docs"
description: Beskriver hur du kan ange anpassade utvecklade flera innehavare program i Azure AD Application Gallery
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
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Hur du lägger till ett program för flera innehavare i Azure AD application Gallery

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD Application Gallery?

Azure AD är en molnbaserad identitetstjänst. [Azure AD app-galleriet](https://azure.microsoft.com/marketplace/active-directory/all/) är en gemensam lagring där alla program kopplingar publiceras för enkel inloggning och användaretablering. Våra ömsesidiga kunder som använder Azure AD som identitetsprovider leta efter olika SaaS-program kopplingar, vilka publiceras här. IT-administratören lägger till koppling från galleriet appen och konfigurerar och använder den för enkel inloggning och etablering. Azure AD stöder alla större federation protokoll som SAML 2.0, OpenID Connect, OAuth och WS-Fed för enkel inloggning. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Om programmet stöder SAML eller OpenIDConnect
Om du har ett program för flera innehavare som du vill visa i Azure AD Application Gallery, måste du först kontrollera att ditt program stöder en av följande enkel inloggning tekniker:

1. **OpenID Connect** – skapa flera innehavare program i Azure AD och implementera [Azure AD medgivande framework](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) för ditt program. Skicka inloggningsbegäran om till vanliga slutpunkten så att en kund kan ge medgivande till programmet. Du kan styra kunden användaråtkomst baserat på klient-ID och användarens UPN togs emot i token. Skicka programmet som anges i detta [artikel](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

2. **SAML** – om programmet stöder SAML 2.0 och vi kan visa ditt program i galleriet och instruktionerna visas [här](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)

Om programmet stöder en av dessa lägen för enkel inloggning och du vill visa en lista med flera innehavare programmet i Azure AD Application Gallery, du kan följa anvisningarna i [detta](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) artikel. 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Om ditt program inte stöder SAML eller OpenIDConnect
Även om programmet inte stöder en av dessa lägen, integrera vi fortfarande den i vår galleriet med vår lösenord Single Sign-on-teknik.

**Lösenord SSO** – skapa ett webbprogram som har en HTML-inloggningssida konfigurera [lösenordsbaserade enkel inloggning](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). Lösenordsbaserade SSO, även kallat lösenord vaulting, kan du hantera åtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbara för situationer där flera användare behöva dela ett enda konto som din organisation sociala medier app konton. 

Om du vill visa ditt program med den här tekniken sedan skicka begäran enligt beskrivningen i [detta](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) artikel.

## <a name="escalations"></a>Eskaleringar

Alla eskaleringar släppa i ett e-postmeddelande till [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) och vi gå tillbaka till du villkoret så SNART.

## <a name="next-steps"></a>Nästa steg
[Visa en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
