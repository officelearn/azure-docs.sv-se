---
title: Ett tilldelat program visas inte på åtkomst panelen | Microsoft Docs
description: Felsöka varför ett program inte visas på åtkomst panelen
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
ms.topic: article
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67272756"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Ett tilldelat program visas inte på åtkomst panelen

Åtkomst panelen är en webbaserad portal som gör det möjligt för en användare med ett arbets-eller skol konto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har beviljat åtkomst till. Dessa program är konfigurerade för användarens räkning i Azure AD-portalen. Programmet måste vara korrekt konfigurerat och tilldelat användaren eller en grupp som användaren är medlem i för att kunna se programmet i åtkomst panelen.

Den typ av appar som en användare kan se hamnar i följande kategorier:

-   Office 365-program

-   Program från Microsoft och tredje part som kon figurer ATS med Federation-baserad SSO

-   Lösenordsbaserade SSO-program

-   Program med befintliga SSO-lösningar

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrol lera först

-   Om ett program precis har lagts till i en användare kan du försöka logga in och ut igen i användarens åtkomst panel efter några minuter för att se om programmet har lagts till.

-   Om en licens precis har tagits bort från en användare eller grupp, kan användaren ta lång tid, beroende på storleken och komplexiteten i gruppen för att ändringar ska göras. Tillåt extra tid innan du loggar in på åtkomst panelen.

## <a name="problems-related-to-application-configuration"></a>Problem som rör program konfiguration

Ett program kanske inte visas i en användares åtkomst panel eftersom det inte har kon figurer ATS korrekt. Nedan visas några exempel på hur du kan felsöka problem som rör program konfigurationen:

-   [Så här konfigurerar du federerad enkel inloggning för ett Azure AD Gallery-program](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Så här konfigurerar du federerad enkel inloggning för ett program som inte är ett galleri program](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Konfigurera ett program för enkel inloggning med lösen ord för ett Azure AD-galleriprogram](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Så här konfigurerar du ett lösen ord för enkel inloggning för ett program som inte är ett galleri program](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett Azure AD Gallery-program

Alla program i Azure AD-galleriet som är aktiverade med funktionen för enkel inloggning i Enterprise har en stegvis själv studie kurs tillgänglig. Du kan få åtkomst till [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en detaljerad steg-för-steg-guide.

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmets metadata-värden i Azure AD (inloggnings-URL, identifierare, svars-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användar-ID och Lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD metadata-värden i programmet (inloggnings-URL, utfärdare, utloggnings-URL och certifikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  I text rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn i text rutan **namn** .

9.  Klicka på knappen **Lägg till** för att lägga till programmet.

Efter en kort period kan du se programmets konfigurations fönster.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill konfigurera enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj **SAML-baserad inloggning** från List rutan **läge** .

9. Ange de värden som krävs i **domän och URL: er.** Du bör hämta dessa värden från program leverantören.

   1. Om du vill konfigurera programmet som SP-initierad SSO måste inloggnings-URL: en vara ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

   2. Om du vill konfigurera programmet som IdP-initierad SSO måste svars-URL: en vara ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

10. **Valfritt:** Klicka på **Visa avancerade URL-inställningar** om du vill se de värden som inte krävs.

11. I användarattribut **väljer**du den unika identifieraren för dina användare i list rutan **användar identifierare** .

12. **Valfritt:** Klicka på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

13. Klicka **på &lt;Konfigurera program&gt; namn** för att få åtkomst till dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också de metadata-URL: er och det certifikat som krävs för att konfigurera SSO med programmet.

14. Klicka på **Spara** för att spara konfigurationen.

15. Tilldela användare till programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut som ska skickas till programmet

Följ stegen nedan om du vill välja användar-ID eller lägga till användarattribut:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

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

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikatet

Följ stegen nedan om du vill ladda ned metadata för programmet eller certifikatet från Azure AD:

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett program som inte är ett galleri program

Om du vill konfigurera ett program som inte är ett galleri program måste du ha Azure AD Premium och programmet har stöd för SAML 2,0. Mer information om Azure AD-versioner finns i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurera programmets metadata-värden i Azure AD (inloggnings-URL, identifierare, svars-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användar-ID och Lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD metadata-värden i programmet (inloggnings-URL, utfärdare, utloggnings-URL och certifikat)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurera programmets metadata-värden i Azure AD (inloggnings-URL, identifierare, svars-URL)

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program som inte finns i Azure AD-galleriet:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6. Klicka på **program som inte är Galleri** i avsnittet **Lägg till din egen app** .

7. Ange namnet på programmet i text rutan **namn** .

8. Klicka på knappen **Lägg till** för att lägga till programmet.

9. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

10. Välj **SAML-baserad inloggning** i list rutan **läge** .

11. Ange de värden som krävs i **domän och URL: er.** Du bör hämta dessa värden från program leverantören.

    1. Ange svars-URL och identifierare för att konfigurera programmet som IdP-initierad SSO.

    2.  **Valfritt:** Om du vill konfigurera programmet som SP-initierad SSO måste inloggnings-URL: en vara ett obligatoriskt värde.

12. I användarattribut **väljer**du den unika identifieraren för dina användare i list rutan **användar identifierare** .

13. **Valfritt:** Klicka på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

14. Klicka **på &lt;Konfigurera program&gt; namn** för att få åtkomst till dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också Azure AD-URL: er och certifikat som krävs för programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut som ska skickas till programmet

Följ stegen nedan om du vill välja användar-ID eller lägga till användarattribut:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

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

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikatet

Följ stegen nedan om du vill ladda ned metadata för programmet eller certifikatet från Azure AD:

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  I text rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn i text rutan **namn** .

9.  Klicka på knappen **Lägg till** för att lägga till programmet.

Efter en kort period kan du se programmets konfigurations fönster.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill konfigurera enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj läge för **lösenordsbaserad inloggning.**

9. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du enkel inloggning för lösen ord för ett program som inte är ett galleri program

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägg till ett program som inte är ett galleri program](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Lägg till ett program som inte är ett galleri program

Följ stegen nedan om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**.

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  Klicka på **program som inte är Galleri.**

7.  Ange namnet på ditt program i text rutan **namn** . Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurations fönster.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på **alla program** om du vill visa en lista över alla dina program.

    1.  Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning för.

7.  När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8.  Välj läge för **lösenordsbaserad inloggning.**

9.  Ange **inloggnings-URL: en**. Detta är URL: en där användarna anger sitt användar namn och lösen ord för att logga in på. Se till att inloggnings fälten visas på URL: en.

10. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

11. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problem som rör tilldelning av program till användare

En användare kanske inte ser något program på sin åtkomst panel eftersom de inte är kopplade till programmet. Nedan visas några sätt att kontrol lera:

-   [Kontrol lera om en användare är tilldelad till programmet](#check-if-a-user-is-assigned-to-the-application)

-   [Så här tilldelar du en användare till ett program direkt](#how-to-assign-a-user-to-an-application-directly)

-   [Kontrol lera om en användare har tilldelats en licens som är relaterad till programmet](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Tilldela en licens till en användare](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrol lera om en användare är tilldelad till programmet

Om du vill kontrol lera om en användare är tilldelad till programmet följer du stegen nedan:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

6. **Sök** efter namnet på programmet i fråga.

7. Klicka på **användare och grupper**.

8. Kontrol lera om användaren är tilldelad till programmet.

   * Om du inte följer stegen i "så här tilldelar du en användare till ett program direkt" för att göra det.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Så här tilldelar du en användare till ett program direkt

Om du vill tilldela en eller flera användare till ett program direkt följer du stegen nedan:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör**.

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill tilldela en användare till från listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Klicka på knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9. Klicka på Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Skriv in det **fullständiga namnet** eller **e-postadressen** för den användare som du är intresse rad av att tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **användaren** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid användarens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du in ett annat **fullständigt namn** eller **e-postadress** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du är färdig med att välja användare klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda användarna.

Efter en kort period kan de användare du har valt kunna starta dessa program på åtkomst panelen.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Kontrol lera om en användare har en licens som är relaterad till programmet

Följ stegen nedan om du vill kontrol lera en användares tilldelade licenser:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

   * Om användaren är tilldelad en Office-licens så gör det möjligt för första partens Office-program att visas på användarens åtkomst panel.

### <a name="how-to-assign-a-user-a-license"></a>Tilldela en licens för en användare 

Följ stegen nedan om du vill tilldela en licens till en användare:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

8.  Klicka på knappen **tilldela** .

9.  Välj **en eller flera produkter** i listan med tillgängliga produkter.

10. **Valfritt** Klicka på objektet **tilldelnings alternativ** om du vill tilldela produkter i detalj. Klicka på **OK** när det är klart.

11. Klicka på knappen **tilldela** för att tilldela dessa licenser till den här användaren.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problem som rör tilldelning av program till grupper

En användare kan se ett program på sin åtkomst panel eftersom de ingår i en grupp som har tilldelats programmet. Nedan visas några sätt att kontrol lera:

-   [Kontrol lera en användares grupp medlemskap](#check-a-users-group-memberships)

-   [Så här tilldelar du ett program till en grupp direkt](#how-to-assign-an-application-to-a-group-directly)

-   [Kontrol lera om en användare är en del av en grupp som har tilldelats en licens](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Så här tilldelar du en licens till en grupp](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Kontrol lera en användares grupp medlemskap

Följ stegen nedan om du vill kontrol lera en grupps medlemskap:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **grupper**.

8. Kontrol lera om användaren är en del av en grupp som tilldelats programmet.

   * Om du vill ta bort användaren från gruppen **klickar du på raden** i gruppen och väljer Ta bort.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Så här tilldelar du ett program till en grupp direkt

Om du vill tilldela en eller flera grupper till ett program direkt följer du stegen nedan:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör**.

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill tilldela en användare till från listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Klicka på knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9. Klicka på Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Skriv in det **fullständiga grupp namnet** för den grupp som du vill tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **gruppen** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid gruppens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt:** Om du vill **lägga till fler än en grupp**skriver du in ett annat **fullständigt grupp namn** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till den här gruppen i den **markerade** listan.

13. När du är färdig med att välja grupper klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som du vill tilldela till de grupper som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda grupperna.

Efter en kort period kan de användare du har valt kunna starta dessa program på åtkomst panelen.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Kontrol lera om en användare är en del av en grupp som har tilldelats en licens

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **grupper**.

8. Klicka på raden för en speciell grupp.

9. Klicka på **licenser** för att se vilka licenser gruppen har tilldelats till den.

   * Om gruppen är tilldelad en Office-licens kan detta medföra att vissa Office-program från första part visas på användarens åtkomst panel.

### <a name="how-to-assign-a-license-to-a-group"></a>Så här tilldelar du en licens till en grupp

Följ stegen nedan om du vill tilldela en licens till en grupp:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla grupper**.

6.  **Sök** efter den grupp du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **licenser** för att se vilka licenser som gruppen för närvarande har tilldelats.

8.  Klicka på knappen **tilldela** .

9.  Välj **en eller flera produkter** i listan med tillgängliga produkter.

10. **Valfritt** Klicka på objektet **tilldelnings alternativ** om du vill tilldela produkter i detalj. Klicka på **OK** när det är klart.

11. Klicka på knappen **tilldela** för att tilldela dessa licenser till den här gruppen. Detta kan ta lång tid, beroende på gruppens storlek och komplexitet.

>[!NOTE]
>För att göra detta snabbare bör du tillfälligt tilldela en licens till användaren direkt. 
>
>

## <a name="next-steps"></a>Nästa steg
[Lägga till nya användare i Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

