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
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fc60d137c45abb99dd029a42c45e8575fc9cede
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182075"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Avancerade alternativ i SAML-token för gallery-appar i Azure Active Directory för Certifikatsignering
Idag stöd för tusentals förintegrerade program i Appgalleriet för Azure Active Directory i Azure Active Directory (AD Azure). Det här värdet innefattar fler än 500 program som stöder enkel inloggning med hjälp av SAML 2.0-protokollet. När en användare autentiseras till ett program via Azure AD med hjälp av SAML skickar Azure AD en token till programmet (via en HTTP-POST). Programmet validerar och använder token för att logga in användaren i stället för att fråga om ett användarnamn och lösenord. Dessa SAML-token har signerats med det unika certifikat som genereras i Azure AD och av specifika standard algoritmer.

Azure AD använder några av standardinställningarna för galleriprogram. Standardvärdena ställs in beroende på programmets krav.

Azure AD stöder avancerade inställningar för certifikatsignering. För att välja dessa alternativ, väljer du först den **visa avancerade inställningar för signering av certifikat** kryssrutan:

![Visa avancerade inställningar för certifikatsignering](./media/certificate-signing-options/saml-advance-certificate.png)

När du har valt den här kryssrutan, kan du konfigurera alternativ för certifikatsignering och certifikatsignering.

## <a name="certificate-signing-options"></a>Alternativ för certifikatsignering

Azure AD stöder tre alternativ för signering av certifikat:

* **Signera SAML-försäkran**. Det här alternativet har angetts för de flesta av de galleriprogram. Om det här alternativet väljs, Azure AD som en IdP loggar SAML-försäkran och certifikatet med X509 certifikatet för programmet. Dessutom används den Signeringsalgoritm som anges i den **signering algoritmen** listrutan.

* **Signera SAML-svar**. Om det här alternativet väljs, Azure AD som en IdP loggar SAML-svar med X509 certifikatet för programmet. Dessutom används den Signeringsalgoritm som anges i den **signering algoritmen** listrutan.

* **Signera SAML-svar och försäkran**. Om det här alternativet väljs, Azure AD som en IdP loggar hela SAML-token med X509 certifikatet för programmet. Dessutom används den Signeringsalgoritm som anges i den **signering algoritmen** listrutan.

    ![Alternativ för certifikatsignering](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>Algoritmer för Certifikatsignering

Azure AD stöder två Signeringsalgoritmer för att signera SAML-svar:

* **SHA-256**. Azure AD använder den här Standardalgoritmen för att signera SAML-svar. Det är den senaste algoritmen och behandlas som mer säker än SHA-1. De flesta av program som stöder SHA-256-algoritmen. Om ett program stöder endast SHA-1 som Signeringsalgoritm, kan du ändra den. I annat fall rekommenderar vi att du använder SHA-256-algoritmen för att signera SAML-svar.

    ![SHA-256 Certifikatsignering](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **SHA-1**. Det här är den äldre algoritmen och behandlas den som mindre säkert än SH-256. Om ett program har stöd för endast det här Signeringsalgoritm, kan du välja det här alternativet i den **signering algoritmen** listrutan. Sedan Azure AD signerar SAML-svar med algoritmen SHA-1.

    ![SHA-1 Certifikatsignering](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>Nästa steg
* [Konfigurera enkel inloggning till program som inte ingår i Azure Active Directory App-galleriet](configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](../develop/howto-v1-debug-saml-sso-issues.md)


