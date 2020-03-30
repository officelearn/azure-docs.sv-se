---
title: Problem med att konfigurera federerade SSO-appar för Azure AD Gallery
description: Åtgärda några av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning med SAML för program som visas i Azure AD Application Gallery
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
ms.openlocfilehash: 87c2497a781b0d46b3b2f1e281a3d7b327b60952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274644"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem med att konfigurera federerad enkel inloggning för ett Azure AD Gallery-program

Om du stöter på ett problem när du konfigurerar ett program. Kontrollera att du har följt alla steg i självstudien för programmet. I programmets konfiguration har du infogad dokumentation om hur du konfigurerar programmet. Du kan också komma åt [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en detaljerad steg-för-steg-vägledning.

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kan inte konfigurera samma identifierare som används för den första instansen.

-   Konfigurera ett annat certifikat än det som används för den första instansen.

Om programmet inte stöder något av ovanstående. Då kan du inte konfigurera en andra instans.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Det går inte att lägga till identifieraren eller svars-URL:en

Om du inte kan konfigurera identifieraren eller svars-URL:en bekräftar du att url-värdena identifierare och svar matchar de mönster som är förkonfigurerade för programmet.

Så här känner du till de mönster som är förkonfigurerade för programmet:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.** Gå till steg 7. Om du redan finns i programkonfigurationsbladet på Azure AD.

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj **SAML-baserad inloggning** i listrutan **Läge.**

9. Gå till textrutan **Identifierare** eller **Svara på URL** under avsnittet Domän och **webbadresser.**

10. Det finns tre sätt att känna till de mönster som stöds för programmet:

    * I textrutan visas de mönster som stöds som ett platshållarexempel: *Example:* <https://contoso.com>.

    * Om mönstret inte stöds visas ett rött utropstecken när du försöker ange värdet i textrutan. Om du håller musen över det röda utropstecknet visas de mönster som stöds.

    * I självstudien för programmet kan du också få information om de mönster som stöds. Under avsnittet **Konfigurera Azure AD-enkel inloggning.** Gå till steget för konfigurerade värden under avsnittet **Domän och webbadresser.**

Om värdena inte stämmer överens med de mönster som är förkonfigurerade på Azure AD. Du kan:

-   Arbeta med programleverantören för att hämta värden som matchar mönstret som är förkonfigurerat på Azure AD

-   Du kan också kontakta Azure <aadapprequest@microsoft.com> AD-teamet på eller lämna en kommentar i självstudien för att begära uppdatering av de mönster som stöds för programmet

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Var ställer jag in entityID-formatet (User Identifier)

Du kan inte välja det EntityID-format (User Identifier) som Azure AD skickar till programmet i svaret efter användarautentisering.

Azure AD väljer formatet för NameID-attributet (User Identifier) baserat på det valda värdet eller det format som begärs av programmet i SAML AuthRequest. Mer information finns i artikeln [Single Sign-On SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Det går inte att hitta Azure AD-metadata för att slutföra konfigurationen med programmet

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

Mer information om hur du anpassar SAML-attributanspråk som skickas till ditt program finns [i Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
