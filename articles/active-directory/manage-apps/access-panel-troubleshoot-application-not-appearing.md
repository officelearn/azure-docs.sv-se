---
title: Ett tilldelat program visas inte på åtkomstpanelen | Microsoft-dokument
description: Felsöka varför ett program inte visas på åtkomstpanelen
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67272756"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Ett tilldelat program visas inte på åtkomstpanelen

Åtkomstpanelen är en webbaserad portal som gör det möjligt för en användare med ett arbets- eller skolkonto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har gett dem åtkomst till. Dessa program konfigureras för användarens räkning i Azure AD-portalen. Programmet måste konfigureras korrekt och tilldelas användaren eller en grupp som användaren är medlem i för att kunna se programmet på åtkomstpanelen.

Den typ av appar som en användare ser faller i följande kategorier:

-   Office 365-program

-   Microsoft- och tredjepartsprogram som konfigurerats med federationsbaserad SSO

-   Lösenordsbaserade SSO-program

-   Program med befintliga SSO-lösningar

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Om ett program har lagts till en användare försöker du logga in och ut igen i användarens åtkomstpanel efter några minuter för att se om programmet läggs till.

-   Om en licens just har tagits bort från en användare eller grupp kan det ta lång tid att användaren är medlem i den här, beroende på gruppens storlek och komplexitet för ändringar. Vänta på extra tid innan du loggar in på åtkomstpanelen.

## <a name="problems-related-to-application-configuration"></a>Problem relaterade till programkonfiguration

Ett program kanske inte visas på en användares åtkomstpanel eftersom det inte är korrekt konfigurerat. Nedan finns några sätt att felsöka problem relaterade till programkonfiguration:

-   [Konfigurera federerad enkel inloggning för ett Azure AD-galleriprogram](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Konfigurera federerad enkel inloggning för ett program som inte är galleri](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Konfigurera ett lösenordsbaserat inloggningsprogram för ett Azure AD-galleriprogram](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Konfigurera ett lösenordsansökningsprogram för ett program som inte är galleri](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera federerad enkel inloggning för ett Azure AD-galleriprogram

Alla program i Azure AD-galleriet som är aktiverade med Enterprise Single Sign-On-funktionen har en steg-för-steg-självstudie. Du kan komma åt [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en detaljerad steg-för-steg-vägledning.

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmets metadatavärden i Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och -certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärden i programmet (Logga in på URL, Utfärdare, URL för utloggning och certifikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Skriv namnet på programmet i textrutan **Ange ett namn** från **galleriet.**

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn från textrutan **Namn.**

9.  Klicka på **Lägg** till om du vill lägga till programmet.

Efter en kort period kan du se programmets konfigurationsfönster.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Så här konfigurerar du enkel inloggning för ett program:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj **SAML-baserad inloggning** i listrutan **Läge.**

9. Ange de värden som krävs i **domän och webbadresser.** Du bör hämta dessa värden från programleverantören.

   1. Om du vill konfigurera programmet som SP-initierad SSO är inloggnings-URL:en ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

   2. Om du vill konfigurera programmet som IdP-initierad SSO är svars-URL:en ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

10. **Valfritt:** Klicka på **Visa avancerade URL-inställningar** om du vill visa de värden som inte krävs.

11. I **användarattributen**väljer du den unika identifieraren för användarna i **listrutan Användaridentifierare.**

12. **Valfritt:** Klicka på **Visa och redigera alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

13. Klicka på ** &lt;Konfigurera programnamn&gt; ** för att komma åt dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också de metadata-URL:er och certifikat som krävs för att konfigurera SSO med programmet.

14. Klicka på **Spara** om du vill spara konfigurationen.

15. Tilldela användare till programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet

Så här väljer du användaridentifieraren eller lägger till användarattribut:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Under avsnittet **Användarattribut** väljer du den unika identifieraren för användarna i listrutan **Användaridentifierare.** Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE] 
   >Azure AD väljer formatet för NameID-attributet (User Identifier) baserat på det valda värdet eller det format som begärs av programmet i SAML AuthRequest. Mer information finns i artikeln [Single Sign-On SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut klickar du på **Visa och redigerar alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

   Så här lägger du till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

   2. Klicka på **Spara.** Du kommer att se det nya attributet i tabellen.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller -certifikatet

Så här hämtar du programmets metadata eller certifikat från Azure AD:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumnvärde. Beroende på vad programmet kräver att du konfigurerar enkel inloggning visas antingen alternativet att hämta METADATA-XML:en eller certifikatet.

   Azure AD tillhandahåller ingen URL för att hämta metadata. Metadata kan bara hämtas som en XML-fil.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Konfigurera federerad enkel inloggning för ett program som inte är galleri

Om du vill konfigurera ett program som inte är galleri måste du ha Azure AD-premium och programmet stöder SAML 2.0. Mer information om Azure AD-versioner finns i [Azure AD-priser](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurera programmets metadatavärden i Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och -certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärden i programmet (Logga in på URL, Utfärdare, URL för utloggning och certifikat)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurera programmets metadatavärden i Azure AD (Sign on URL, Identifier, Reply URL)

Så här konfigurerar du enkel inloggning för ett program som inte finns i Azure AD-galleriet nedan:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6. Klicka på **Program för icke-galleri** i avsnittet **Lägg till din egen app.**

7. Ange namnet på programmet i textrutan **Namn.**

8. Klicka på **Lägg** till om du vill lägga till programmet.

9. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

10. Välj **SAML-baserad inloggning** i listrutan **Läge.**

11. Ange de värden som krävs i **domän och webbadresser.** Du bör hämta dessa värden från programleverantören.

    1. Om du vill konfigurera programmet som IdP-initierad SSO anger du svars-URL:en och identifieraren.

    2.  **Valfritt:** Om du vill konfigurera programmet som SP-initierad SSO är inloggnings-URL:en ett obligatoriskt värde.

12. I **användarattributen**väljer du den unika identifieraren för användarna i **listrutan Användaridentifierare.**

13. **Valfritt:** Klicka på **Visa och redigera alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

14. Klicka på ** &lt;Konfigurera programnamn&gt; ** för att komma åt dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också Azure AD-url:er och certifikat som krävs för programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet

Så här väljer du användaridentifieraren eller lägger till användarattribut:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Under avsnittet **Användarattribut** väljer du den unika identifieraren för användarna i listrutan **Användaridentifierare.** Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE] 
   >Azure AD väljer formatet för NameID-attributet (User Identifier) baserat på det valda värdet eller det format som begärs av programmet i SAML AuthRequest. Mer information finns i artikeln [Single Sign-On SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut klickar du på **Visa och redigerar alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

   Så här lägger du till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

   2. Klicka på **Spara.** Det nya attributet visas i tabellen.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller -certifikatet

Så här hämtar du programmets metadata eller certifikat från Azure AD:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumnvärde. Beroende på vad programmet kräver att du konfigurerar enkel inloggning visas antingen alternativet att hämta METADATA-XML:en eller certifikatet.

Azure AD tillhandahåller ingen URL för att hämta metadata. Metadata kan bara hämtas som en XML-fil.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera lösenordsbaserad inloggning för ett Azure AD-galleriprogram

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmet för enkel inloggning för lösenord](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Skriv namnet på programmet i textrutan **Ange ett namn** från **galleriet.**

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn från textrutan **Namn.**

9.  Klicka på **Lägg** till om du vill lägga till programmet.

Efter en kort period kan du se programmets konfigurationsfönster.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning för lösenord

Så här konfigurerar du enkel inloggning för ett program:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj läge **Lösenordsbaserad inloggning.**

9. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Dessutom kan du också ange autentiseringsuppgifter för användaren genom att välja användarnas rader och klicka på **Uppdatera autentiseringsuppgifter** och ange användarnamn och lösenord för användarnas räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurera lösenordsbaserad inloggning för ett program som inte är galleri

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program som inte är galleri](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning för lösenord](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Lägga till ett program som inte är galleri

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**.

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Klicka på **Program för icke-galleri.**

7.  Ange namnet på programmet i textrutan **Namn.** Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurationsfönster.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Konfigurera programmet för enkel inloggning för lösenord

Så här konfigurerar du enkel inloggning för ett program:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på **Alla program om** du vill visa en lista över alla dina program.

    1.  Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8.  Välj läge **Lösenordsbaserad inloggning.**

9.  Ange **inloggnings-URL.** Det här är webbadressen där användarna anger sitt användarnamn och lösenord för att logga in på. Kontrollera att inloggningsfälten visas på webbadressen.

10. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

11. Dessutom kan du också ange autentiseringsuppgifter för användaren genom att välja användarnas rader och klicka på **Uppdatera autentiseringsuppgifter** och ange användarnamn och lösenord för användarnas räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problem med att tilldela program till användare

En användare kanske inte ser ett program på åtkomstpanelen eftersom de inte har tilldelats programmet. Nedan följer några sätt att kontrollera:

-   [Kontrollera om en användare har tilldelats programmet](#check-if-a-user-is-assigned-to-the-application)

-   [Så här tilldelar du en användare till ett program direkt](#how-to-assign-a-user-to-an-application-directly)

-   [Kontrollera om en användare har tilldelats en licens som är relaterad till programmet](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Så här tilldelar du en licens till en användare](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrollera om en användare har tilldelats programmet

Så här kontrollerar du om en användare har tilldelats programmet:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

6. **Sök efter** namnet på programmet i fråga.

7. klicka på **Användare och grupper**.

8. Kontrollera om användaren har tilldelats programmet.

   * Om inte följa stegen i "Hur man tilldelar en användare till ett program direkt" för att göra det.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Så här tilldelar du en användare till ett program direkt

Så här tilldelar du en eller flera användare ett program direkt:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör**.

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill tilldela en användare till i listan.

7. När programmet har läses in klickar du på **Användare och grupper** från programmets navigeringsmeny till vänster.

8. Klicka på knappen **Lägg** till högst upp i listan **Användare och grupper** för att öppna fönstret Lägg till **tilldelning.**

9. Klicka på väljaren **Användare och grupper** i fönstret Lägg till **tilldelning.**

10. Skriv in det **fullständiga namnet** eller **e-postadressen** till den användare som du är intresserad av att tilldela sökrutan Sök efter **namn eller e-postadress.**

11. Hovra över **användaren** i listan för att visa en **kryssruta**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp om du vill lägga till användaren i listan **Markerad.**

12. **Valfritt:** Om du vill lägga till **fler än en användare**skriver du in ett annat fullständigt namn eller en annan **fullständig** **adress** i sökrutan Sök efter namn **eller e-postadress** och klickar på kryssrutan om du vill lägga till den här användaren i listan **Markerad.**

13. När du är klar med att välja användare klickar du på knappen **Välj** om du vill lägga till dem i listan över användare och grupper som ska tilldelas programmet.

14. **Valfritt:** Klicka på **selectorn Välj roll** i fönstret Lägg till **tilldelning** om du vill välja en roll som du vill tilldela de användare som du har markerat.

15. Klicka på knappen **Tilldela** om du vill tilldela programmet till de markerade användarna.

Efter en kort period kan de användare som du har valt starta dessa program på åtkomstpanelen.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Kontrollera om en användare har en licens som är relaterad till programmet

Så här kontrollerar du en användares tilldelade licenser:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. klicka på **Alla användare**.

6. **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7. Klicka på **Licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

   * Om användaren har tilldelats en Office-licens gör detta att Office-program för första part kan visas på användarens åtkomstpanel.

### <a name="how-to-assign-a-user-a-license"></a>Så här tilldelar du en användare en licens 

Så här tilldelar du en licens till en användare:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  klicka på **Alla användare**.

6.  **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

8.  klicka på knappen **Tilldela.**

9.  Välj **en eller flera produkter** i listan över tillgängliga produkter.

10. **Valfritt** klicka på **tilldelningsalternativsartikeln** om du vill tilldela produkter på ett detaljerat sätt. Klicka på **Ok** när detta är klart.

11. Klicka på knappen **Tilldela** om du vill tilldela dessa licenser till den här användaren.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problem med att tilldela program till grupper

En användare kan se ett program på åtkomstpanelen eftersom de ingår i en grupp som har tilldelats programmet. Nedan följer några sätt att kontrollera:

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Så här tilldelar du ett program till en grupp direkt](#how-to-assign-an-application-to-a-group-directly)

-   [Kontrollera om en användare ingår i en grupp som tilldelats en licens](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Så här tilldelar du en licens till en grupp](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Så här kontrollerar du en grupps medlemskap:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. klicka på **Alla användare**.

6. **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7. klicka på **Grupper**.

8. Kontrollera om användaren ingår i en grupp som har tilldelats programmet.

   * Om du vill ta bort användaren från gruppen **klickar du på gruppens rad** och väljer ta bort.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Så här tilldelar du ett program till en grupp direkt

Så här tilldelar du en eller flera grupper till ett program direkt:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör**.

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill tilldela en användare till i listan.

7. När programmet har läses in klickar du på **Användare och grupper** från programmets navigeringsmeny till vänster.

8. Klicka på knappen **Lägg** till högst upp i listan **Användare och grupper** för att öppna fönstret Lägg till **tilldelning.**

9. Klicka på väljaren **Användare och grupper** i fönstret Lägg till **tilldelning.**

10. Skriv in det **fullständiga gruppnamnet** för den grupp som du är intresserad av att tilldela sökrutan Sök efter **namn eller e-postadress.**

11. Hovra över **gruppen** i listan för att visa en **kryssruta**. Klicka på kryssrutan bredvid gruppens profilfoto eller logotyp om du vill lägga till användaren i listan **Markerad.**

12. **Valfritt:** Om du vill lägga till **mer än en grupp**skriver du in ett annat fullständigt **gruppnamn** i sökrutan **Sök efter namn eller e-postadress** och klickar på kryssrutan om du vill lägga till den här gruppen i listan **Markerad.**

13. När du är klar med att välja grupper klickar du på knappen **Välj** om du vill lägga till dem i listan över användare och grupper som ska tilldelas programmet.

14. **Valfritt:** Klicka på **selectorn Välj roll** i fönstret Lägg till **tilldelning** om du vill välja en roll som ska tilldelas de grupper som du har markerat.

15. Klicka på knappen **Tilldela** om du vill tilldela programmet till de markerade grupperna.

Efter en kort period kan de användare som du har valt starta dessa program på åtkomstpanelen.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Kontrollera om en användare ingår i en grupp som tilldelats en licens

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. klicka på **Användare och grupper** i navigeringsmenyn.

5. klicka på **Alla användare**.

6. **Sök efter** den användare du är intresserad av och **klicka på raden** för att välja.

7. klicka på **Grupper**.

8. klicka på raden för en viss grupp.

9. Klicka på **Licenser** för att se vilka licenser gruppen har tilldelat den.

   * Om gruppen har tilldelats en Office-licens kan detta göra det möjligt för vissa Office-program från första part att visas på användarens åtkomstpanel.

### <a name="how-to-assign-a-license-to-a-group"></a>Så här tilldelar du en licens till en grupp

Så här tilldelar du en licens till en grupp:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  klicka på **Användare och grupper** i navigeringsmenyn.

5.  Klicka på **Alla grupper**.

6.  **Sök efter** den grupp du är intresserad av och **klicka på raden** för att välja.

7.  Klicka på **Licenser** för att se vilka licenser gruppen har tilldelat.

8.  klicka på knappen **Tilldela.**

9.  Välj **en eller flera produkter** i listan över tillgängliga produkter.

10. **Valfritt** klicka på **tilldelningsalternativsartikeln** om du vill tilldela produkter på ett detaljerat sätt. Klicka på **Ok** när detta är klart.

11. Klicka på knappen **Tilldela** om du vill tilldela dessa licenser till den här gruppen. Detta kan ta lång tid, beroende på gruppens storlek och komplexitet.

>[!NOTE]
>Om du vill göra detta snabbare bör du tillfälligt tilldela en licens till användaren direkt. 
>
>

## <a name="next-steps"></a>Nästa steg
[Lägga till nya användare i Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

