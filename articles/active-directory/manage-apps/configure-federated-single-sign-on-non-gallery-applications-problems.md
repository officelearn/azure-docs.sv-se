---
title: Problem med att konfigurera federerad enkel inloggning för ett program som inte är ett galleri program | Microsoft Docs
description: Åtgärda några av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning till ditt anpassade SAML-program som inte visas i Azure AD-programgalleriet
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84a8ee8ca29046d26d200aaf853a3efe25f15768
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763575"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problem med att konfigurera federerad enkel inloggning för ett program som inte är ett galleri program

Om det uppstår ett problem när du konfigurerar ett program. Kontrol lera att du har följt alla steg i artikeln [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory program galleriet.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kan inte konfigurera samma identifierare som används för den första instansen.

-   Konfigurera ett annat certifikat än det som används för den första instansen.

Om programmet inte har stöd för någon av föregående kan du inte konfigurera en andra instans.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Var anger jag formatet för EntityID (användar identifierare)

Du kan inte välja det EntityID-format (User Identifier) som Azure AD skickar till programmet i svaret efter användarautentisering.

Azure AD väljer formatet för attributet NameID (användar identifierare) baserat på det värde som valts eller det format som begärdes av programmet i SAML-AuthRequest. Mer information finns i artikeln [Single Sign-on SAML Protocol](../develop/single-sign-on-saml-protocol.md#authnrequest) i avsnittet NameIDPolicy.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Var hämtar jag programmets metadata eller certifikat från Azure AD

Följ dessa steg om du vill ladda ned metadata för programmet eller certifikatet från Azure AD:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du har konfigurerat enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumn värde. Beroende på vad programmet kräver för att konfigurera enkel inloggning, ser du antingen alternativet att ladda ned XML-metadata eller certifikatet.

Azure AD tillhandahåller inte en URL för att hämta metadata. Det går bara att hämta metadata som en XML-fil.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Vet inte hur man anpassar SAML-anspråk som skickas till ett program

Information om hur du anpassar SAML-attributets anspråk som skickas till ditt program finns i [anspråks mappning i Azure Active Directory](../develop/active-directory-claims-mapping.md) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
