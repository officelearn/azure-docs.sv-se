---
title: Konfigurera federerad enkel inloggning för ett program för Azure AD-galleriet problemet | Microsoft Docs
description: Vissa av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning med SAML för program som listas i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 581b3c4ff7cc7b25f238602560f30280efa34edd
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34053317"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem som konfigurerar federerad enkel inloggning för ett program för Azure AD-galleriet

Om du stöter på problem när du konfigurerar ett program. Kontrollera att du har följt alla steg i självstudiekursen för programmet. I programmets konfiguration har du infogat dokumentation om hur du konfigurerar programmet. Du kan också komma åt den [lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) en stegvisa instruktioner för detaljer.

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program, måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kan inte konfigurera den samma identifierare som används för den första instansen.

-   Konfigurera ett annat certifikat än den som används för den första instansen.

Om programmet inte stöder någon av ovanstående. Sedan kan du inte konfigurera en andra instans.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Det går inte att lägga till identifierare eller Reply-URL

Om du inte kunna konfigurera identifieraren eller URL för svar bekräfta identifierare och Reply URL-värdena matchar mönster förkonfigurerade för programmet.

Du behöver veta mönster förkonfigurerade för programmet:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.** Gå till steg 7. Om du redan är i bladet programmets konfiguration på Azure AD.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Välj **SAML-baserade inloggning** från den **läge** listrutan.

9.  Gå till den **identifierare** eller **Reply URL** textruta under den **domän och URL: er.**

10. Det finns tre sätt att veta stöds mönster för programmet:

   * I textrutan, stöds pattern(s) visas som platshållare *exempel:* <https://contoso.com>.

   * Om mönstret inte stöds, visas ett rött utropstecken vid försök att ange värdet i textrutan. Om du håller muspekaren över rött utropstecken ser du mönster som stöds.

   * Du kan också få information om de stöds i självstudierna för programmet. Under den **konfigurera Azure AD enkel inloggning** avsnitt. Gå till steg för konfigurerade värden under den **domän och URL: er** avsnitt.

Om värdena inte matchar mönster förkonfigurerade på Azure AD. Du kan:

-   Arbeta med programvaruleverantören för att hämta värden som matchar mönstret förkonfigurerade på Azure AD

-   Eller kontakta Azure AD-teamet på <aadapprequest@microsoft.com> eller lämna en kommentar i guiden för att begära att uppdatera mönster som stöds för programmet

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Där anger formatet ID för entiteterna (användar-ID)

Du kommer inte att kunna välja det ID för entiteterna (användar-ID)-format som Azure AD skickar till programmet i svaret efter autentisering av användare.

Azure AD-Välj format för attributet NameID (användar-ID) baserat på värdet valt eller formatet programmet har begärt i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Det går inte att hitta Azure AD-metadata för att slutföra konfigurationen med programmet

Följ dessa steg för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Gå till **SAML-signeringscertifikat** avsnittet och klicka sedan på **hämta** värde i kolumnen. Beroende på vilka programmet kräver att konfigurera enkel inloggning, finns antingen alternativet för att hämta Metadata XML eller certifikatet.

Azure AD Ange inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Vet inte hur du anpassar SAML anspråk skickas till ett program

Information om hur du anpassar SAML attributet anspråk som skickas till ditt program finns [anspråk mappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)
