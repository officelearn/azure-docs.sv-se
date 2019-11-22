---
title: Så här konfigurerar du federerad enkel inloggning för en app som inte är en Galleri
description: Så här konfigurerar du federerad enkel inloggning för ett anpassat icke-galleriprogram som du vill integrera med Azure AD
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
ms.openlocfilehash: c1d49ec5ef80b284aa6b1a305b037d19dae34870
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274600"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett program som inte är ett galleri program

Om du vill konfigurera enkel inloggning för ett program som inte är ett galleri program *utan att skriva kod*, måste du ha en prenumeration eller Azure AD Premium och programmet måste ha stöd för SAML 2,0. Mer information om Azure AD-versioner finns i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Översikt över de steg som krävs
Nedan visas en översikt över de steg som krävs för att konfigurera federerad enkel inloggning med SAML 2,0 för ett icke-Galleri program (t. ex. anpassat).

-   Konfigurera programmets metadata-värden i Azure AD (inloggnings-URL, identifierare, svars-URL)

-   [Välj användar-ID och Lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurera Azure AD metadata-värden i programmet (inloggnings-URL, utfärdare, utloggnings-URL och certifikat)

-   Tilldela användare till programmet

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurera enkel inloggning till program som inte är gallerier

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program som inte finns i Azure AD-galleriet:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6. Klicka på **program som inte är Galleri** i avsnittet **Lägg till din egen app**

7. Ange namnet på programmet i text rutan **namn** .

8. Klicka på knappen **Lägg till** för att lägga till programmet.

9. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

10. Välj **SAML-baserad inloggning** i list rutan **läge** .

11. Ange de värden som krävs i **domän och URL: er.** Du bör hämta dessa värden från program leverantören.

    1. Ange svars-URL och identifierare för att konfigurera programmet som IdP-initierad SSO.

    2. **Valfritt:** Om du vill konfigurera programmet som SP-initierad SSO måste inloggnings-URL: en vara ett obligatoriskt värde.

12. I användarattribut **väljer**du den unika identifieraren för dina användare i list rutan **användar identifierare** .

13. **Valfritt:** Klicka på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

14. Klicka på **konfigurera &lt;program namn&gt;** för att få åtkomst till dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också Azure AD-URL: er och certifikat som krävs för programmet.

15. [Tilldela användare till programmet.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut som ska skickas till programmet

Följ stegen nedan om du vill välja användar-ID eller lägga till användarattribut:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du har konfigurerat enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Under avsnittet **användarattribut** väljer du den unika identifieraren för dina användare i list rutan **användar identifierare** . Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE]
   >Azure AD väljer formatet för attributet NameID (användar identifierare) baserat på det värde som valts eller det format som begärdes av programmet i SAML-AuthRequest. Mer information finns i artikeln [Single Sign-on SAML Protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) i avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut klickar du på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

   Så här lägger du till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

   2. Klicka på **Spara.** Det nya attributet visas i tabellen.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikatet

Följ stegen nedan om du vill ladda ned metadata för programmet eller certifikatet från Azure AD:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du har konfigurerat enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumn värde. Beroende på vad programmet kräver för att konfigurera enkel inloggning, ser du antingen alternativet att ladda ned XML-metadata eller certifikatet.

Azure AD tillhandahåller också en URL för att hämta metadata. Följ det här mönstret för att hämta metadata-URL: en som är specifik för programmet: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

## <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program med hjälp av metoderna som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Anpassa SAML-anspråk som skickats till ett program

Information om hur du anpassar SAML-attributets anspråk som skickas till ditt program finns i [anspråks mappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
