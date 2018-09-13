---
title: Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory | Microsoft Docs
description: Lär dig att anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory
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
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: ba4d8d46a6423a932eceea7fbe20ae985445c6a3
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718890"
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory
Idag Azure Active Directory har stöd för enkel inloggning på med de flesta företagsprogram, inklusive båda programmen som är förintegrerade i appgalleriet för Azure AD samt anpassade program. När en användare autentiseras till ett program via Azure AD med hjälp av protokollet SAML 2.0, skickar Azure AD en token till programmet (via en HTTP-POST). Och sedan programmet validerar och använder token för att logga in användaren i stället för att fråga om ett användarnamn och lösenord. Dessa SAML-token innehåller uppgifter om den användare som kallas ”anspråk”.

I identity-talar, ett ”anspråk” är information som en identitetsprovider som anger om en användare i de token som de utfärda för användaren. I [SAML-token](http://en.wikipedia.org/wiki/SAML_2.0), dessa data finns vanligtvis i instruktionen SAML-attribut. Användarens unika ID: T är vanligtvis representeras i SAML-ämne som även kallas för namnidentifierare.

Som standard utfärdar en SAML-token till ditt program som innehåller en NameIdentifier-anspråket, med ett värde på användarens användarnamn (AKA användarens huvudnamn) i Azure AD i Azure Active Directory. Det här värdet kan unikt identifiera användaren. SAML-token innehåller också ytterligare anspråk som innehåller användarens e-postadress, Förnamn och efternamn.

Om du vill visa eller redigera de anspråk som utfärdats i SAML-token till programmet genom att öppna programmet i Azure-portalen. Välj sedan den **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnittet av programmet.

![Användaren attributavsnittet][1]

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:
* Programmet har skrivits för att kräva en annan uppsättning anspråk URI: er eller anspråksvärden.
* Programmet har distribuerats på ett sätt som kräver NameIdentifier-anspråket ska vara något annat än användarnamnet (AKA användarens huvudnamn) lagras i Azure Active Directory.

Du kan redigera standardvärdena för anspråk. Välj raden anspråk i SAML-tokenattribut tabellen. Då öppnas det **redigera attributet** avsnittet och sedan du kan redigera anspråkets namn, värde och namnområde som är associerade med anspråket.

![Redigera användarattribut][2]

Du kan också ta bort anspråk (andra än NameIdentifier) med hjälp av snabbmenyn, vilket öppnar genom att klicka på den **...**  ikon. Du kan också lägga till nya anspråk med hjälp av den **Lägg till attribut** knappen.

![Redigera användarattribut][3]

## <a name="editing-the-nameidentifier-claim"></a>Redigera NameIdentifier-anspråket
Att lösa problem där programmet har distribuerats med hjälp av ett annat användarnamn, klicka på den **användaridentifierare** nedrullningsbar listruta den **användarattribut** avsnittet. Den här åtgärden visar en dialogruta med flera olika alternativ:

![Redigera användarattribut][4]

I listrutan, väljer **user.mail** att ställa in NameIdentifier-anspråket är användarens e-postadress i katalogen. Eller välj **user.onpremisessamaccountname** s SAM-kontonamn som har synkroniserats från den lokala Azure AD ska anges till användaren.

Du kan också använda särskilda **ExtractMailPrefix()** att ta bort domänsuffix från e-postadressen, SAM-kontonamn eller användarens huvudnamn. Då extraheras bara den första delen av användarnamnet som skickas via (till exempel ”joe_smith” i stället för joe_smith@contoso.com).

![Redigera användarattribut][5]

Vi har nu även lagt till den **join()** funktionen för att ansluta till den verifierade domänen med användar-ID-värde. När du väljer funktionen join() i den **användaridentifierare** först välja användar-ID som t.ex. e-postadress eller användarnamn huvudnamn och välj sedan din verifierade domän i andra listrutan. Om du markerar den e-postadressen med en verifierad domän och Azure AD extraherar användarnamnet från det första värdet joe_smith från joe_smith@contoso.com och lägger till dem med contoso.onmicrosoft.com. Se följande exempel:

![Redigera användarattribut][6]

## <a name="adding-claims"></a>Att lägga till anspråk
När du lägger till ett anspråk, kan du ange attributets namn (som strikt inte behöver följer ett mönster för URI enligt SAML-specifikationen). Ange värdet till valfria användarattribut som lagras i katalogen.

![Lägg till användarattribut][7]

Exempelvis kan behöva du skicka avdelning som användaren tillhör i deras organisation, som ett anspråk (till exempel försäljning). Ange namnet på anspråk som förväntat av programmet och välj sedan **user.department** som värde.

> [!NOTE]
> Om det finns inget värde som lagras i ett valt attribut för en viss användare, är inte det anspråket som utfärdas i token.

> [!TIP]
> Den **user.onpremisesecurityidentifier** och **user.onpremisesamaccountname** stöds endast när synkronisering av användardata från en lokal Active Directory med hjälp av den [Azure AD Anslut verktyget](../active-directory-aadconnect.md).

## <a name="restricted-claims"></a>Begränsad anspråk

Det finns vissa begränsade anspråk i SAML. Om du lägger till dessa anspråk skickas de här anspråken inte i Azure AD. Nedan följer SAML begränsade anspråksuppsättningen:

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
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
