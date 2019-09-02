---
title: Så här konfigurerar du federerad enkel inloggning för ett Azure AD Gallery-program | Microsoft Docs
description: Så här konfigurerar du federerad enkel inloggning för ett befintligt Azure AD Gallery-program och hur du använder självstudier för att komma igång snabbt
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
ROBOTS: NOINDEX
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207130"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett Azure AD Gallery-program

Det finns en steg-för-steg-guide som är tillgänglig för alla program i Azure Active Directory (Azure AD)-galleriet som har funktioner för enkel inloggning i företag. Du kan komma åt [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för detaljerade steg-för-steg-instruktioner.

## <a name="overview-of-steps-required"></a>Översikt över de steg som krävs
Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmets metadata värden i Azure AD (inloggnings-URL, identifierare, svars-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användar-ID och Lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD metadata-värden i programmet (inloggnings-URL, utfärdare, utloggnings-URL och certifikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Tilldela användare till programmet](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ dessa steg om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller medadministratör.

2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn på vänster sida.

3.  Skriv "Azure Active Directory" i sökrutan och välj **Azure Active Directory**.

4.  Välj **företags program** från den vänstra navigerings menyn i Azure AD.

5.  Välj **Lägg till** i det övre högra hörnet i fönstret **företags program** .

6.  I rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn i rutan **namn** .

9.  Välj **Lägg till** för att lägga till programmet.

Efter en kort period bör du kunna se programmets konfigurations fönster.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som **Global administratör** eller medadministratör.

2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn på vänster sida.

3. Skriv "Azure Active Directory" i sökrutan och välj **Azure Active Directory**.

4. Välj **företags program** från Azure Active Directory-navigerings menyn på vänster sida.

5. Välj **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill använda kan du använda **filter** kontrollen längst upp i **listan Alla program** och ange alternativet **Visa** för **alla program.**

6. Välj det program som du vill konfigurera enkel inloggning för.

7. När programmet har lästs in väljer du **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj **SAML-baserad inloggning** från List rutan **läge** .

9. Ange de värden som krävs i **domän och URL: er.** Du bör hämta dessa värden från program leverantören.

   1. Om du vill konfigurera programmet som SP-initierad SSO måste inloggnings-URL: en vara ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

   2. Om du vill konfigurera programmet som IdP-initierad SSO måste svars-URL: en vara ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

10. **Valfritt**: Välj **Visa avancerade URL-inställningar** om du vill se de värden som inte krävs.

11. I användarattributväljer du den unika identifieraren för dina användare i list rutan **användar identifierare** .

12. **Valfritt**: Välj **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

    Så här lägger du till ett attribut:
   
    1. Välj **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

    1. Välj **Spara.** Det nya attributet visas i tabellen.

13. Välj **Konfigurera &lt;program namn&gt;**  för att få åtkomst till dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också de URL: er och certifikat för metadata som krävs för att konfigurera SSO med programmet.

14. Välj **Spara** för att spara konfigurationen.

15. Tilldela användare till programmet.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut som ska skickas till programmet

Följ dessa steg om du vill välja användar-ID eller lägga till användarattribut:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som **Global administratör** eller medadministratör **.**

2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn på vänster sida.

3. Skriv "Azure Active Directory" i sökrutan och välj **Azure Active Directory**.

4. Välj **företags program** från Azure Active Directory-navigerings menyn på vänster sida.

5. Välj **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill använda kan du använda **filter** kontrollen längst upp i **listan Alla program** och ange alternativet **Visa** för **alla program.**

6. Välj det program som du har konfigurerat med enkel inloggning.

7. När programmet har lästs in väljer du **enkel inloggning** från programmets vänstra navigerings meny.

8. Under avsnittet **användarattribut** väljer du den unika identifieraren för dina användare i list rutan **användar identifierare** . Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE] 
   >Azure AD väljer formatet för attributet NameID (användar identifierare) baserat på det värde som valts eller det format som begärdes av programmet i SAML-AuthRequest. Mer information finns i [SAML-protokoll för enkel inloggning](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) i avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut väljer du **Visa och redigera alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

   Så här lägger du till ett attribut:
  
   1. Välj **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

   2. Välj **Spara**. Det nya attributet visas i tabellen.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikatet

Följ dessa steg om du vill ladda ned metadata för programmet eller certifikatet från Azure AD:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som **Global administratör** eller medadministratör **.**

2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn på vänster sida.

3. Skriv "Azure Active Directory" i sökrutan och välj **Azure Active Directory**.

4. Välj **företags program** från Azure Active Directory-navigerings menyn på vänster sida.

5. Välj **alla program** om du vill visa en lista över alla dina program.

   *  Om du inte ser det program som du vill använda kan du använda **filter** kontrollen längst upp i **listan Alla program** och ange alternativet **Visa** för **alla program**.

6. Välj det program som du har konfigurerat med enkel inloggning.

7. När programmet har lästs in väljer du **enkel inloggning** från programmets vänstra navigerings meny.

8. Gå till avsnittet **SAML-signeringscertifikat** och välj sedan **Hämta** kolumn värde. Beroende på vad programmet kräver för att konfigurera enkel inloggning, ser du antingen alternativet att ladda ned XML-metadata eller certifikatet.

Azure AD tillhandahåller också en URL för åtkomst till metadata. Använd följande modell för att hämta URL: en för metadata som är specifik för programmet:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn på vänster sida.

3. Skriv "Azure Active Directory" i sökrutan och välj **Azure Active Directory**.

4. Välj **företags program** från Azure Active Directory-navigerings menyn på vänster sida.

5. Välj **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill använda kan du använda **filter** kontrollen längst upp i **listan Alla program** och ange alternativet **Visa** för **alla program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in väljer **du användare och grupper** från programmets vänstra navigerings meny.

8. Välj knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9. Välj Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Ange det **fullständiga namnet** eller **e-** postadressen för den användare som du vill tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **användaren** i listan för att visa en **kryss ruta**. Markera kryss rutan bredvid användarens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt**: Om du vill **lägga till fler än en användare**skriver du ett **nytt fullständigt namn** eller **e-postadress** till sökrutan **Sök efter namn eller e-postadress** och markerar kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du är klar med att välja användare väljer du knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt**: Välj **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Välj knappen **tilldela** för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program med hjälp av metoderna som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Anpassa SAML-anspråk som skickats till ett program

Information om hur du anpassar SAML-attributets anspråk som skickas till programmet finns i anspråks [mappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)



