---
title: Hur du konfigurerar federerad enkel inloggning för ett Azure AD-galleriprogram | Microsoft Docs
description: Hur du konfigurerar federerad enkel inloggning för ett befintligt Azure AD-galleriet och Använd självstudier för att komma igång snabbt
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: e1555830fb8e355ec4e291dc49b7d6983f87ffc5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168404"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Hur du konfigurerar federerad enkel inloggning för ett Azure AD-galleriprogram

Alla program i Azure AD-galleriet som aktiverats med Enterprise enkel inloggning för funktionen har en stegvis självstudiekurs som är tillgängliga. Du kan komma åt den [lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) detaljerade stegvisa instruktioner.

## <a name="overview-of-steps-required"></a>Översikt över steg som krävs
Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmets metadatavärden i Azure AD (inloggning URL, identifierare svars-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärdena i programmet (inloggning URL, utfärdare, utloggnings-URL och certifikatet)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Tilldela användare till programmet](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  I den **anger du ett namn** textrutan från den **Lägg till från galleriet** Skriv namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textrutan.

9.  Klicka på **Lägg till** för att lägga till programmet.

Du att kunna se programmets konfigurationsruta efter en kort tidsperiod.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj **SAML-baserad inloggning** från den **läge** listrutan.

9.  Ange värdena som krävs i **domän och URL: er.** Du bör få värdena från programleverantören.

   1. Det är ett obligatoriskt värde för att konfigurera programmet som inloggnings-URL: en till SP-initierad SSO. Identifieraren är också ett obligatoriskt värde för vissa program.

   2. Det är ett obligatoriskt värde för att konfigurera programmet som IdP-initierad SSO svars-URL. Identifieraren är också ett obligatoriskt värde för vissa program.

10. **Valfritt:** klickar du på **visa avancerade URL-inställningar** om du vill se de icke krävs.

11. I den **användarattribut**, Välj den unika identifieraren för dina användare i den **användaridentifierare** listrutan.

12. **Valfritt:** klickar du på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

  Lägga till ett attribut:
   
   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   1. Klicka på **spara.** Det nya attributet visas i tabellen.

13. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Dessutom har du metadata-URL: er och certifikat som krävs för att konfigurera enkel inloggning med programmet.

14. Klicka på **spara** att spara konfigurationen.

15. Tilldela användare till programmet.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet

Välj användar-ID eller lägga till användarattribut kan du följa stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Under den **användarattribut** väljer du den unika identifieraren för dina användare i den **användaridentifierare** listrutan. Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

  >[!NOTE] 
  >Azure AD väljer du formatet för NameID-attributet (användaridentifierare) baserat på värdet valt eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
  >
  >

9.  Lägg till användarattribut, klicka på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:
  
   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **Spara**. Det nya attributet visas i tabellen.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikat

Följ stegen nedan för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  *  Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program**.

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **SAML-signeringscertifikat** och sedan klicka på **hämta** kolumnvärde. Beroende på vilka programmet kräver att du konfigurerar enkel inloggning, se du antingen välja att hämta Metadata XML eller certifikatet.

Azure AD tillhandahåller också en URL för att hämta metadata. Följ det här mönstret för att få Metadata_url som är specifika för programmet: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program med hjälp av metoderna som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Anpassa SAML-anspråk som skickas till ett program

Läs hur du anpassar SAML attributet anspråk som skickas till programmet i [Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)



