---
title: Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory | Microsoft Docs
description: Lär dig hur du anpassar anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 485e4c52751b390ebd440718e7a55de7b5b5ffd7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory
Idag Azure Active Directory har stöd för enkel inloggning på med de flesta företagsprogram, inklusive både program redan integrerade i Azure AD app-galleriet och anpassade program. När en användare autentiseras till ett program via Azure AD med hjälp av SAML 2.0-protokollet, skickar en token i Azure AD till programmet (via en HTTP POST). Och sedan programmet validerar och använder token för att logga in användaren i stället för att fråga efter användarnamn och lösenord. Dessa SAML-token innehålla uppgifter om användaren som kallas ”anspråk”.

I identity-tala är ett ”anspråk” information som en identitetsleverantör tillstånd om en användare i den token som de utfärdar för användaren. I [SAML-token](http://en.wikipedia.org/wiki/SAML_2.0), dessa data finns vanligtvis i SAML Attribute-uttryck. Användarens unika ID: T är vanligtvis representeras i SAML-ämne som även kallas namnidentifierare.

Azure Active Directory utfärdar en SAML-token till ditt program som innehåller ett NameIdentifier-anspråk med ett värde på användarens användarnamn (AKA användarens huvudnamn) i Azure AD som standard. Det här värdet kan identifiera användaren. SAML-token innehåller även ytterligare anspråk som innehåller användarens e-postadress, Förnamn och efternamn.

Om du vill visa eller redigera anspråk som utfärdats i SAML-token till programmet genom att öppna programmet i Azure-portalen. Välj sedan den **visa och redigera andra användarattribut** kryssrutan i den **användarattribut** avsnitt i programmet.

![Användaren attribut avsnitt][1]

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:
* Programmet har skrivits till kräver en annan uppsättning anspråk URI: er eller anspråksvärden.
* Programmet har distribuerats på ett sätt som kräver NameIdentifier att anspråket ska vara något annat än användarnamnet (AKA användarens huvudnamn) lagras i Azure Active Directory.

Du kan redigera standardvärdena för anspråk. Välj anspråk raden i tabellen för SAML-token attribut. Då öppnas den **Redigera attribut** avsnittet och du sedan kan redigera anspråkets namn, värde och namnområde som hör till anspråket.

![Redigera användarattribut][2]

Du kan också ta bort anspråk (andra än NameIdentifier) med snabbmenyn som öppnas genom att klicka på den **...**  ikon. Du kan också lägga till nya anspråk med hjälp av den **Lägg till attributet** knappen.

![Redigera användarattribut][3]

## <a name="editing-the-nameidentifier-claim"></a>Redigera NameIdentifier anspråk
Att lösa problemet där programmet har distribuerats med hjälp av ett annat användarnamn, klicka på den **användar-ID** listrutan den **användarattribut** avsnittet. Den här åtgärden visar en dialogruta med flera olika alternativ:

![Redigera användarattribut][4]

Välj i listrutan, **user.mail** ange NameIdentifier begäran om att användarens e-postadress i katalogen. Eller välj **user.onpremisessamaccountname** till användare vars SAM-kontonamnet som har synkroniserats från lokala Azure AD.

Du kan också använda särskilda **ExtractMailPrefix()** funktion för att ta bort domänsuffix från e-postadress, SAM-kontonamn eller användarens huvudnamn. Detta extraherar bara den första delen av användarnamnet som skickas via (till exempel ”joe_smith” i stället för joe_smith@contoso.com).

![Redigera användarattribut][5]

Vi har nu lagt till den **join()** funktion för att ansluta till den verifierade domänen med identifierarvärde för användaren. När du väljer funktionen join() i den **användar-ID** först välja användar-ID som t.ex. e-postadress eller användaren huvudnamn och välj sedan din verifierade domän i andra listrutan. Om du markerar den e-postadressen med verifierade domän och Azure AD extraherar användarnamnet från det första värdet joe_smith från joe_smith@contoso.com och lägger till dem med contoso.onmicrosoft.com. Se följande exempel:

![Redigera användarattribut][6]

## <a name="adding-claims"></a>Lägga till anspråk
Du kan ange attributets namn (som endast inte behöver följa ett URI-mönster enligt SAML-specifikationen) när du lägger till ett anspråk. Ange värdet till valfria användarattribut som lagras i katalogen.

![Lägg till användarattribut][7]

Du måste till exempel skicka avdelningen som användaren tillhör i organisationen som ett anspråk (till exempel försäljning). Ange namnet anspråk som förväntades av programmet och välj sedan **user.department** som värde.

> [!NOTE]
> Om det finns inget värde som lagras i ett valt attribut för en viss användare, är inte det anspråket som utfärdats i token.

> [!TIP]
> Den **user.onpremisesecurityidentifier** och **user.onpremisesamaccountname** stöds endast när synkronisering av användardata från lokala Active Directory med hjälp av den [Azure AD Connect-verktyget](../active-directory-aadconnect.md).

## <a name="restricted-claims"></a>Begränsat anspråk

Det finns vissa begränsade anspråk i SAML. Om du lägger till dessa anspråk skickas dessa anspråk inte i Azure AD. Följande är uppsättningen SAML begränsad anspråk:

    | Anspråkstyp (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Nästa steg
* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)
* [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Felsökning av SAML-baserade enkel inloggning](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
