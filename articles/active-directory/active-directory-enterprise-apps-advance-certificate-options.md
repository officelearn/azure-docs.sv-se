---
title: "Avancerade alternativ i SAML-token för förintegrerade appar i Azure Active Directory för Certifikatsignering | Microsoft Docs"
description: "Lär dig hur du använder avancerade alternativ i SAML-token för förintegrerade appar i Azure Active Directory om Certifikatsignering"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: c4fb4e220d03533bd73a663d12e2639e664b4dba
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Avancerade alternativ i SAML-token för galleriet appar i Azure Active Directory om Certifikatsignering
Azure Active Directory (AD Azure) stöder idag tusentals förintegrerade program i Appgalleriet för Azure Active Directory. Antalet inkluderar mer än 500 program som stöder enkel inloggning med hjälp av SAML 2.0-protokollet. När en användare autentiseras till ett program via Azure AD med hjälp av SAML skickar en token i Azure AD till programmet (via en HTTP POST). Programmet validerar och använder token för att logga in användaren i stället för att fråga efter användarnamn och lösenord. Dessa SAML-token har signerats med det unika certifikat som genereras i Azure AD och särskilda algoritmer som standard.

Azure AD använder vissa av standardinställningarna för galleriet program. Standardvärden ställs in beroende på programmets krav.

Azure AD stöder avancerade inställningar för certifikatsignering. Om du vill välja alternativen först välja den **visa avancerade inställningar för signering av certifikat** kryssrutan:

![Visa avancerade inställningar för Certifikatsignering][1]

När du har valt den här kryssrutan om kan du konfigurera alternativ för certifikatsignering och certifikatsignering.

## <a name="certificate-signing-options"></a>Alternativ för Certifikatsignering

Azure AD stöder tre alternativ för signering av certifikat:

* **Signera SAML assertion**. Det här standardalternativet har angetts för de flesta program gallery. Om det här alternativet väljs, Azure AD som en IdP loggar SAML-kontroll och certifikatet med X509 certifikat för programmet. Dessutom används Signeringsalgoritm som anges i den **signering algoritmen** listrutan.

* **Signera SAML-svaret**. Om det här alternativet väljs, Azure AD som en IdP loggar SAML-svaret med X509 certifikat för programmet. Dessutom används Signeringsalgoritm som anges i den **signering algoritmen** listrutan.

* **Signera SAML-svar och assertion**. Om det här alternativet väljs, Azure AD som en IdP loggar hela SAML-token med X509 certifikat för programmet. Dessutom används Signeringsalgoritm som anges i den **signering algoritmen** listrutan.

    ![Alternativ för Certifikatsignering][4]

## <a name="certificate-signing-algorithms"></a>Algoritmer för Certifikatsignering

Azure AD stöder två signering algoritmer för att signera SAML-svar:

* **SHA-256**. Azure AD använder standard-algoritmen för att signera SAML-svaret. Den senaste algoritmen och behandlas som mer säker än SHA-1. De flesta av program som stöd för SHA-256-algoritmen. Om ett program stöder endast SHA-1 som Signeringsalgoritm kan ändra du den. I annat fall rekommenderar vi att du använder SHA-256-algoritmen för att signera SAML-svaret.

    ![SHA-256 Certifikatsignering][3]

* **SHA-1**. Detta är den äldre algoritmen och behandlas som mindre säker än SH-256. Om ett program stöder endast den här Signeringsalgoritm, kan du välja det här alternativet i den **signering algoritmen** listrutan. Azure AD sedan loggar SAML-svaret med algoritmen SHA-1.

    ![SHA-1 Certifikatsignering][2]

## <a name="next-steps"></a>Nästa steg
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Konfigurera enkel inloggning för program som inte ingår i Appgalleriet för Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Felsökning av SAML-baserade enkel inloggning](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
