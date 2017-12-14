---
title: "Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du anpassar anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory"
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7394857f55493b072e6ea549c8eeec54a808f5e5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
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

Du kan också ta bort anspråk (andra än NameIdentifier) med snabbmenyn som öppnas genom att klicka på den **...**  ikon.  Du kan också lägga till nya anspråk med hjälp av den **Lägg till attributet** knappen.

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
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expiration |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/EXPIRED |
    | http://schemas.microsoft.com/Identity/Claims/accesstoken |
    | http://schemas.microsoft.com/Identity/Claims/openid2_id |
    | http://schemas.microsoft.com/Identity/Claims/identityprovider |
    | http://schemas.microsoft.com/Identity/Claims/objectidentifier |
    | http://schemas.microsoft.com/Identity/Claims/PUID |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/NameIdentifier [MR1] |
    | http://schemas.microsoft.com/Identity/Claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationInstant |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationMethod |
    | http://schemas.microsoft.com/AccessControlService/2010/07/Claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/groups |
    | http://schemas.microsoft.com/Claims/groups.Link |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/role |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/Claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/Claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/Claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/Claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/Identity/Claims/Actor |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SID |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/UPN |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/GroupSID |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SPN |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/Identity/Claims/scope |

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
