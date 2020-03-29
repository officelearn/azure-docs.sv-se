---
title: Problem med att konfigurera federerade enkel inloggning för en icke-galleriansökan | Microsoft-dokument
description: Åtgärda några av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning till ditt anpassade SAML-program som inte finns med i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76711887"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problem med att konfigurera federerad enkel inloggning för ett program som inte är galleri

Om du stöter på ett problem när du konfigurerar ett program. Kontrollera att du har följt alla steg i artikeln [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory-programgalleriet.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kan inte konfigurera samma identifierare som används för den första instansen.

-   Konfigurera ett annat certifikat än det som används för den första instansen.

Om programmet inte stöder något av föregående kan du inte konfigurera en andra instans.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Var ställer jag in entityID-formatet (User Identifier)

Du kan inte välja det EntityID-format (User Identifier) som Azure AD skickar till programmet i svaret efter användarautentisering.

Azure AD väljer formatet för NameID-attributet (User Identifier) baserat på det valda värdet eller det format som begärs av programmet i SAML AuthRequest. Mer information finns i artikeln [Single Sign-On SAML protokoll](../develop/single-sign-on-saml-protocol.md#authnrequest) under avsnittet NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Var hämtar jag programmets metadata eller certifikat från Azure AD

Så här hämtar du programmets metadata eller certifikat från Azure AD:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumnvärde. Beroende på vad programmet kräver att du konfigurerar enkel inloggning visas antingen alternativet att hämta METADATA-XML:en eller certifikatet.

Azure AD tillhandahåller ingen URL för att hämta metadata. Metadata kan bara hämtas som en XML-fil.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Vet inte hur man anpassar SAML-anspråk som skickas till ett program

Mer information om hur du anpassar SAML-attributanspråk som skickas till ditt program finns [i Anspråksmappning i Azure Active Directory](../develop/active-directory-claims-mapping.md) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
