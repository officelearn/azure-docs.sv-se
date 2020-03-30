---
title: Avancerade alternativ för signering av SAML-tokencertifikat för Azure AD-appar
description: Lär dig hur du använder avancerade certifikatsigneringsalternativ i SAML-token för förintegrerade appar i Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159207"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Avancerade alternativ för certifikatsignering i SAML-token för galleriappar i Azure Active Directory

Idag stöder Azure Active Directory (Azure AD) tusentals förintegrerade program i Azure Active Directory App Gallery. Över 500 av programmen stöder enkel inloggning med hjälp av SAML-protokollet [(Security Assertion Markup Language),](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) till exempel [NetSuite-programmet.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) När en kund autentiserar till ett program via Azure AD med hjälp av SAML skickar Azure AD en token till programmet (via ett HTTP POST). Programmet validerar och använder sedan token för att logga in kunden i stället för att fråga efter ett användarnamn och lösenord. Dessa SAML-token signeras med det unika certifikat som genereras i Azure AD och av specifika standardalgoritmer.

Azure AD använder några av standardinställningarna för galleriprogrammen. Standardvärdena ställs in baserat på programmets krav.

I Azure AD kan du ställa in certifikatsigneringsalternativ och certifikatsigneringsalgoritmen.

## <a name="certificate-signing-options"></a>Alternativ för certifikatsignering

Azure AD stöder tre alternativ för certifikatsignering:

* **Underteckna SAML påstående**. Det här standardalternativet är inställt för de flesta galleriprogram. Om du väljer det här alternativet signerar Azure AD som identitetsprovider (IdP) SAML-lysa och certifikat med [X.509-certifikatet](https://wikipedia.org/wiki/X.509) för programmet.

* **Signera SAML-svar**. Om du väljer det här alternativet signerar Azure AD som ett IdP SAML-svaret med X.509-certifikatet för programmet.

* **Signera SAML-svar och påstående**. Om du väljer det här alternativet signerar Azure AD som ett IdP hela SAML-token med X.509-certifikatet för programmet.

## <a name="certificate-signing-algorithms"></a>Algoritmer för signering av certifikat

Azure AD stöder två signeringsalgoritmer, eller säkra hash-algoritmer (SHAs), för att signera SAML-svaret:

* **SHA-256**. Azure AD använder den här standardalgoritmen för att signera SAML-svaret. Det är den nyaste algoritmen och är säkrare än SHA-1. De flesta av programmen stöder SHA-256-algoritmen. Om ett program bara stöder SHA-1 som signeringsalgoritm kan du ändra den. Annars rekommenderar vi att du använder SHA-256-algoritmen för att signera SAML-svaret.

* **SHA-1**. Denna algoritm är äldre, och det behandlas som mindre säker än SHA-256. Om ett program bara stöder den här signeringsalgoritmen kan du välja det här alternativet i listrutan **Signeringsalgoritm.** Azure AD signerar sedan SAML-svaret med SHA-1-algoritmen.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Ändra certifikatsigneringsalternativ och signeringsalgoritm

Om du vill ändra ett programs SAML-certifikatsigneringsalternativ och certifikatsigneringsalgoritmen väljer du programmet i fråga:

1. Logga in på ditt konto i [Azure Active Directory-portalen.](https://aad.portal.azure.com/) Sidan **Azure Active Directory admin center** visas.
1. I den vänstra rutan väljer du **Företagsprogram**. En lista över företagsprogram i ditt konto visas.
1. Välj ett program. En översiktssida för programmet visas.

   ![Exempel: Sidan Översikt över program](./media/certificate-signing-options/application-overview-page.png)

Ändra sedan certifikatsigneringsalternativen i SAML-token för det programmet:

1. I den vänstra rutan på sidan för programöversikt väljer du **Enkel inloggning**.
1. Om sidan **Konfigurera enkel inloggning med SAML - Förhandsgranska** visas går du till steg 5.
1. Om sidan **Välj en enskild inloggningsmetod** inte visas väljer du **Ändra enkla inloggningslägen** för att visa den sidan.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML** om det är tillgängligt. (Om **SAML** inte är tillgängligt stöder programmet inte SAML och du kan ignorera resten av den här proceduren och artikeln.)
1. Leta reda på rubriken **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML - Förhandsgranska** och välj ikonen **Redigera** (en penna). Sidan **SAML-signeringscertifikat** visas.

   ![Exempel: SIDAN SAML-signeringscertifikat](./media/certificate-signing-options/saml-signing-page.png)

1. I listrutan **Signeringsalternativ** väljer du **Signera SAML-svar,** **Signera SAML-påstående**eller **Signera SAML-svar och -- -eller signera SAML-svar och -- -eller signera SAML-svar och -- -eller signera SAML-svar och -- -eller signera SAML-svar och -- -.** Beskrivningar av dessa alternativ visas tidigare i den här artikeln i [alternativen för signering av certifikat](#certificate-signing-options).
1. I listrutan **Signeringsalgoritm** väljer du **SHA-1** eller **SHA-256**. Beskrivningar av dessa alternativ visas tidigare i den här artikeln i avsnittet [Algoritmer för certifikatsignering.](#certificate-signing-algorithms)
1. Om du är nöjd med dina val väljer du **Spara** för att tillämpa de nya SAML-signeringscertifikatinställningarna. Annars väljer du **X** för att ignorera ändringarna.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory App Gallery](configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
