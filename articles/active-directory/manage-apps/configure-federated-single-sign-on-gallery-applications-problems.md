---
title: Problem med att konfigurera federerad SSO för Azure AD Gallery-appar
description: Åtgärda några av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning med SAML för program som listas i Azure AD-programgalleriet
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
ms.openlocfilehash: 858e61c6371aac28d709a9016e4b9cbed6befe3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763609"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem med att konfigurera federerad enkel inloggning för ett Azure AD Gallery-program

Om det uppstår ett problem när du konfigurerar ett program. Kontrol lera att du har följt alla steg i självstudien för programmet. I programmets konfiguration har du infogat dokumentation om hur du konfigurerar programmet. Du kan också få åtkomst till [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en detaljerad steg-för-steg-guide.

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kommer inte att kunna konfigurera samma identifierare som används för den första instansen.

-   Konfigurera ett annat certifikat än det som används för den första instansen.

Om programmet inte stöder något av ovanstående. Sedan kan du inte konfigurera en andra instans.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Det går inte att lägga till identifieraren eller svars-URL

Om du inte kan konfigurera identifieraren eller svars-URL: en bekräftar du ID: t och svars-URL-värdena matchar de mönster som är förkonfigurerade för programmet.

För att veta vilka mönster som är förkonfigurerade för programmet:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.** Gå till steg 7. Om du redan finns på bladet program konfiguration på Azure AD.

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill konfigurera enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj **SAML-baserad inloggning** från List rutan **läge** .

9. Gå till text rutan **identifierare** eller **svars-URL** , under **avsnittet domän och URL: er.**

10. Det finns tre sätt att känna till de mönster som stöds för programmet:

    * I text rutan ser du de mönster som stöds som ett exempel på en plats hållare *:* <https://contoso.com> .

    * Om mönstret inte stöds visas ett rött utrops tecken när du försöker ange värdet i text rutan. Om du hovrar med musen över det röda utrops tecknet ser du de mönster som stöds.

    * I självstudien för programmet kan du också få information om de mönster som stöds. Under avsnittet **Konfigurera enkel inloggning i Azure AD** . Gå till steget för konfigurerade värden under avsnittet **domän och URL: er** .

Om värdena inte matchar de mönster som förkonfigureras i Azure AD. Du kan:

-   Arbeta med program leverantören för att hämta värden som matchar mönstret förkonfigurerat i Azure AD

-   Alternativt kan du kontakta Azure AD-teamet på <aadapprequest@microsoft.com> eller lämna en kommentar i självstudien för att begära uppdateringen av de mönster som stöds för programmet

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Var anger jag formatet för EntityID (användar identifierare)

Du kan inte välja det EntityID-format (User Identifier) som Azure AD skickar till programmet i svaret efter användarautentisering.

Azure AD väljer formatet för attributet NameID (användar identifierare) baserat på det värde som valts eller det format som begärdes av programmet i SAML-AuthRequest. Mer information finns i artikeln [Single Sign-on SAML Protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) i avsnittet NameIDPolicy.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Det går inte att hitta Azure AD-metadata för att slutföra konfigurationen med programmet

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

Information om hur du anpassar SAML-attributets anspråk som skickas till ditt program finns i [anspråks mappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
