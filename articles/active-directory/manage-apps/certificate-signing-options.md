---
title: Avancerad certifikatsignering alternativ i SAML-token för förintegrerade appar i Azure Active Directory | Microsoft Docs
description: Lär dig hur du använder avancerade alternativ i SAML-token för förintegrerade appar i Azure Active Directory för Certifikatsignering
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bede53ef2bc05750be21f831fc0cb790a001c6c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442785"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Avancerade alternativ i SAML-token för gallery-appar i Azure Active Directory för Certifikatsignering

Idag stöd för tusentals förintegrerade program i Appgalleriet för Azure Active Directory i Azure Active Directory (AD Azure). Över 500 program stöder enkel inloggning med den [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0-protokollet, till exempel den [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) program. När en kund autentiserar till ett program via Azure AD med hjälp av SAML, skickar Azure AD en token till programmet (via en HTTP-POST). Programmet sedan validerar och använder token för inloggning av kunden för ett användarnamn och lösenord. Dessa SAML-token har signerats med det unika certifikat som genereras i Azure AD och av specifika standard algoritmer.

Azure AD använder några av standardinställningarna för galleriprogram. Standardvärdena ställs in beroende på programmets krav.

I Azure AD kan du konfigurera alternativ för certifikatsignering och certifikatsignering.

## <a name="certificate-signing-options"></a>Alternativ för certifikatsignering

Azure AD stöder tre alternativ för signering av certifikat:

* **Signera SAML-försäkran**. Det här alternativet har angetts för de flesta av de galleriprogram. Om du väljer det här alternativet kan Azure AD som identitetsprovider (IdP) loggar SAML-försäkran och certifikatet med den [X.509](https://wikipedia.org/wiki/X.509) certifikatet för programmet.

* **Signera SAML-svar**. Om du väljer det här alternativet signerar Azure AD som en IdP SAML-svar med X.509-certifikat för programmet.

* **Signera SAML-svar och försäkran**. Om du väljer det här alternativet signerar Azure AD som en IdP hela SAML-token med X.509-certifikat för programmet.

## <a name="certificate-signing-algorithms"></a>Algoritmer för Certifikatsignering

Azure AD stöder två Signeringsalgoritmer eller säkra hash-algoritmer (SHA) att signera SAML-svar:

* **SHA-256**. Azure AD använder den här Standardalgoritmen för att signera SAML-svar. Det är den senaste algoritmen och är säkrare än SHA-1. De flesta av program som stöder SHA-256-algoritmen. Om ett program stöder endast SHA-1 som Signeringsalgoritm, kan du ändra den. I annat fall rekommenderar vi att du använder SHA-256-algoritmen för att signera SAML-svar.

* **SHA-1**. Den här algoritmen är äldre och behandlas den som mindre säkert än SHA-256. Om ett program har stöd för endast det här Signeringsalgoritm, kan du välja det här alternativet i den **signering algoritmen** listrutan. Sedan Azure AD signerar SAML-svar med algoritmen SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Ändra certifikat Signeringsalternativ och Signeringsalgoritm

Välj programmet som är i fråga om du vill ändra ett programs alternativ för SAML-certifikatsignering och certifikatsignering:

1. I den [Azure Active Directory-portalen](https://aad.portal.azure.com/), logga in på ditt konto. Den **Azure Active Directory Administrationscenter** visas.
1. I den vänstra rutan väljer du **Företagsprogram**. En lista över enterprise-program i ditt konto visas.
1. Välj ett program. En översiktssida för programmet visas.

   ![Översiktssidan för program](./media/certificate-signing-options/application-overview-page.png)

Sedan ändra certifikatsignering alternativ i SAML-token för programmet:

1. I den vänstra rutan på översiktssidan för program, Välj **enkel inloggning**.

2. Om den **ange in enkel inloggning med SAML - förhandsversion** visas, går du till steg 5.

3. Om den **väljer du en metod för enkel inloggning** sidan inte visas, Välj **ändra lägen för enkel inloggning** att visa den sidan.

4. I den **väljer du en metod för enkel inloggning** väljer **SAML** om det är tillgängligt. (Om **SAML** är inte tillgänglig, programmet stöder inte SAML och du kan ignorera resten av den här proceduren och artikel.)

5. I den **ange in enkel inloggning med SAML - förhandsversion** sidan, hitta den **SAML-signeringscertifikat** rubrik- och väljer den **redigera** ikon (en penna). Den **SAML-signeringscertifikat** visas.

   ![SAML-signeringscertifikat sidan](./media/certificate-signing-options/saml-signing-page.png)

6. I den **signering alternativet** listrutan väljer du **inloggning SAML-svar**, **inloggning SAML-försäkran**, eller **inloggning SAML-svar och försäkran**. Beskrivningar av dessa alternativ visas tidigare i den här artikeln i den [alternativ för certifikatsignering](#certificate-signing-options).

7. I den **signering algoritmen** listrutan väljer du **SHA-1** eller **SHA-256**. Beskrivningar av dessa alternativ visas tidigare i den här artikeln i den [algoritmer för certifikatsignering](#certificate-signing-algorithms) avsnittet.

8. Om du är nöjd med dina val väljer **spara** att tillämpa den nya SAML-signeringscertifikat certifikatinställningar. Annars väljer du den **X** ignorera ändringarna.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera enkel inloggning till program som inte ingår i Azure Active Directory App-galleriet](configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](../develop/howto-v1-debug-saml-sso-issues.md)
