---
title: Ett tilldelat program inte visas på åtkomstpanelen | Microsoft Docs
description: Felsöka anledningen till att ett program inte visas i åtkomstpanelen
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2018
ms.author: barbkess
ms.reviwer: japere
ms.openlocfilehash: aebf41668ab63179b356fc31022e0c988ca863f9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297136"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Ett tilldelat program visas inte på åtkomstpanelen

Åtkomstpanelen är en webbaserad portal, vilket gör att en användare med ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program att Azure AD-administratör har gett dem åtkomst till. Dessa program är konfigurerade för användarens räkning på Azure AD-portalen. Programmet måste korrekt konfigurerat och tilldelat till användaren eller en grupp som användaren är medlem i vill se programmet på åtkomstpanelen.

Typ av appar som visas kanske en användare finns i följande kategorier:

-   Office 365-program

-   Microsoft och tredje parts program som har konfigurerats med federation-baserad enkel inloggning

-   Lösenordsbaserad SSO-program

-   Program med befintliga lösningar för enkel inloggning

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Om ett program har lagt till en användare, kan du försöka logga in och ut igen i åtkomstpanelen för användarens efter ett par minuter att se om programmet har lagts till.

-   Om bara en licens har tagits bort från en användare eller grupp som användaren är medlem i det här kan ta lång tid, beroende på storleken och komplexiteten i gruppen för ändringar görs. Tillåt extra tid innan du loggar in på åtkomstpanelen.

## <a name="problems-related-to-application-configuration"></a>Problem som rör programkonfiguration

Ett program kan inte visas i åtkomstpanelen för en användares eftersom den inte har konfigurerats korrekt. Nedan visas några sätt som du kan felsöka problem som rör programkonfiguration:

-   [Hur du konfigurerar federerad enkel inloggning för ett Azure AD-galleriprogram](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Hur du konfigurerar federerad enkel inloggning för en icke-galleriprogram](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Så här konfigurerar du ett lösenord för inloggning för program för ett Azure AD-galleriprogram](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Så här konfigurerar du ett lösenord för inloggning för program för en icke-galleriprogram](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Hur du konfigurerar federerad enkel inloggning för ett Azure AD-galleriprogram

Alla program i Azure AD-galleriet som aktiveras med Enterprise Single Sign-On-funktionen har en stegvis självstudiekurs som är tillgängliga. Du kan komma åt den [lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en stegvis vägledning i detalj.

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmets metadatavärden i Azure AD (inloggning URL, identifierare svars-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärdena i programmet (inloggning URL, utfärdare, utloggnings-URL och certifikatet)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

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

Du att kunna se programmets konfigurationsruta efter en kort period.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj **SAML-baserad inloggning** från den **läge** listrutan.

9.  Ange värdena som krävs i **domän och URL: er.** Du bör få värdena från programleverantören.

   1. Det är ett obligatoriskt värde för att konfigurera programmet som URL: en inloggning till SP-initierad SSO. Identifieraren är också ett obligatoriskt värde för vissa program.

   2. Det är ett obligatoriskt värde för att konfigurera programmet som IdP-initierad SSO svars-URL. Identifieraren är också ett obligatoriskt värde för vissa program.

10. **Valfritt:** klickar du på **visa avancerade URL-inställningar** om du vill se de icke krävs.

11. I den **användarattribut**, Välj den unika identifieraren för dina användare i den **användaridentifierare** listrutan.

12. **Valfritt:** klickar du på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **spara.** Det nya attributet visas i tabellen.

13. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Dessutom har du metadata-URL: er och certifikat som krävs för att konfigurera enkel inloggning med programmet.

14. Klicka på **spara** att spara konfigurationen.

15. Tilldela användare till programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet

Välj användar-ID eller lägga till användarattribut kan du följa stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet du vill visas här, Använd den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att  **Alla program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Under den **användarattribut** väljer du den unika identifieraren för dina användare i den **användaridentifierare** listrutan. Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE] 
   >Azure AD väljer formatet för NameID-attributet (användaridentifierare) baserat på värdet som valts eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9.  Lägg till användarattribut, klicka på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **spara.** Du kan se det nya attributet i tabellen.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikat

Följ stegen nedan för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **SAML-signeringscertifikat** och sedan klicka på **hämta** kolumnvärde. Beroende på vilka programmet kräver att du konfigurerar enkel inloggning, se du antingen välja att hämta Metadata XML eller certifikatet.

    Azure AD tillhandahåller inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Hur du konfigurerar federerad enkel inloggning för en icke-galleriprogram

Om du vill konfigurera en icke-galleriprogram, måste du ha Azure AD premium och programmet stöder SAML 2.0. Mer information om Azure AD-versioner finns [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurera programmets metadatavärden i Azure AD (inloggning URL, identifierare svars-URL)](#configuring-single-sign-on)

-   [Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärdena i programmet (inloggning URL, utfärdare, utloggnings-URL och certifikatet)](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurera programmets metadatavärden i Azure AD (inloggning URL, identifierare svars-URL)

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program som inte är i Azure AD-galleriet:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  Klicka på **icke-galleriprogram** i den **lägga till din egen app** avsnittet.

7.  Ange namnet på programmet i den **namn** textrutan.

8.  Klicka på **Lägg till** för att lägga till programmet.

9.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

10. Välj **SAML-baserad inloggning** i den **läge** listrutan.

11. Ange värdena som krävs i **domän och URL: er.** Du bör få värdena från programleverantören.

   1. Ange svars-URL och identifierare för att konfigurera programmet som en IdP-initierad SSO.

   2.  **Valfritt:** för att konfigurera programmet som SP-initierad SSO URL: en inloggning som det är ett obligatoriskt värde.

12. I den **användarattribut**, Välj den unika identifieraren för dina användare i den **användaridentifierare** listrutan.

13. **Valfritt:** klickar du på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **spara.** Det nya attributet visas i tabellen.

14. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Dessutom har du URL: er med Azure AD och certifikat som krävs för programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet

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
   >Azure AD väljer formatet för NameID-attributet (användaridentifierare) baserat på värdet som valts eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9.  Lägg till användarattribut, klicka på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **spara.** Det nya attributet visas i tabellen.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikat

Följ stegen nedan för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **SAML-signeringscertifikat** och sedan klicka på **hämta** kolumnvärde. Beroende på vilka programmet kräver att du konfigurerar enkel inloggning, se du antingen välja att hämta Metadata XML eller certifikatet.

Azure AD tillhandahåller inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett Azure AD-galleriprogram

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

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

Du kan se programmets konfigurationsruta efter en kort period.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för en icke-galleriprogram

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägg till en icke-galleriprogram](#add-a-non-gallery-application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Lägg till en icke-galleriprogram

Följ stegen nedan om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  Klicka på **icke-galleriprogram.**

7.  Ange namnet på ditt program i den **namn** textrutan. Välj **lägga till.**

Du att kunna se programmets konfigurationsruta efter en kort period.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

    1.  Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Ange den **inloggnings-URL**. Det här är URL: en där användare anger sina användarnamn och lösenord för inloggning. Kontrollera fälten inloggning syns i URL: en.

10. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

11. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problem som rör tilldela program till användare

En användare kan inte se ett program på sina Åtkomstpaneler eftersom de inte är tilldelade till programmet. Nedan visas några metoder för att kontrollera:

-   [Kontrollera om en användare är kopplad till programmet](#check-if-a-user-is-assigned-to-the-application)

-   [Tilldela en användare till ett program direkt](#how-to-assign-a-user-to-an-application-directly)

-   [Kontrollera om en användare har tilldelats en licens som rör programmet](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Tilldela en licens till en användare](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrollera om en användare är kopplad till programmet

Du kan kontrollera om en användare har tilldelats till programmet genom att följa stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

6.  **Sök** för namnet på programmet i fråga.

7.  Klicka på **användare och grupper**.

8.  Kontrollera om dina användare tilldelas till programmet.

   * Annars följer du stegen i ”så här tilldelar du en användare till ett program direkt” att göra detta.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör**.

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

12. **Valfritt:** om du skulle vilja **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program i åtkomstpanelen efter en kort period.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Kontrollera om en användare är under en licens som rör programmet

Följ stegen nedan om du vill kontrollera en användares tilldelade licenser:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser du för närvarande har tilldelats.

  * Om användaren har tilldelats en licens för Office detta gör det möjligt för den första part Office-program ska visas på användarens åtkomstpanelen.

### <a name="how-to-assign-a-user-a-license"></a>Så här tilldelar du en användare en licens 

Följ stegen nedan om du vill tilldela en licens till en användare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser du för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfritt** klickar du på den **tilldelningsalternativ** objektet om du vill tilldela detaljerat produkter. Klicka på **Ok** när det är klart.

11. Klicka på den **tilldela** knappen för att tilldela dessa licenser till den här användaren.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problem som rör tilldela program till grupper

En användare kan se ett program på sina Åtkomstpaneler eftersom de är en del av en grupp som har tilldelats programmet. Nedan visas några metoder för att kontrollera:

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Tilldela ett program till en grupp direkt](#how-to-assign-an-application-to-a-group-directly)

-   [Kontrollera om en användare är en del av gruppen som har tilldelats en licens](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Tilldela en licens till en grupp](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Följ stegen nedan om du vill kontrollera en grupps medlemskap:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper**.

8.  Kontrollera om dina användare är medlem i en grupp som tilldelats programmet.

  * Om du vill ta bort användaren från gruppen **klickar du på raden** av gruppen och välj Ta bort.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Tilldela ett program till en grupp direkt

Följ stegen nedan om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständiga namn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid gruppen profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till mer än en grupp**, typ i en annan **fullständiga namn** till den **Sök efter namn eller e-postadress** sökrutan och Klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll tilldelas till de grupper som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till valda grupper.

De användare som du har valt att kunna starta dessa program i åtkomstpanelen efter en kort period.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Kontrollera om en användare är en del av gruppen som har tilldelats en licens

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper**.

8.  Klicka på raden i en specifik grupp.

9.  Klicka på **licenser** att se vilka licenser gruppen har tilldelats.

   * Om gruppen har tilldelats en licens för Office som det här kan vissa första part Office-program ska visas på användarens åtkomstpanelen.

### <a name="how-to-assign-a-license-to-a-group"></a>Tilldela en licens till en grupp

Följ stegen nedan om du vill tilldela en licens till en grupp:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** att se vilka licenser gruppen för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfritt** klickar du på den **tilldelningsalternativ** objektet om du vill tilldela detaljerat produkter. Klicka på **Ok** när det är klart.

11. Klicka på den **tilldela** knappen för att tilldela dessa licenser till den här gruppen. Det kan ta lång tid, beroende på storleken och komplexiteten i gruppen.

>[!NOTE]
>Överväg att tillfälligt tilldela en licens till användaren direkt om du vill göra detta snabbare. 
>
>

## <a name="next-steps"></a>Nästa steg
[Lägga till nya användare i Azure Active Directory](./fundamentals/add-users-azure-active-directory.md)

