---
title: Konfigurera federerad enkel inloggning för en app som inte är galleri
description: Konfigurera federerade enkel inloggning för ett anpassat program som inte är galleri som du vill integrera med Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274600"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Konfigurera federerad enkel inloggning för ett program som inte är galleri

Om du vill konfigurera enkel inloggning för ett program som inte är ett galleri utan att *skriva kod*måste du ha en prenumeration eller Azure AD Premium och programmet måste ha stöd för SAML 2.0. Mer information om Azure AD-versioner finns i [Azure AD-priser](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Översikt över de steg som krävs
Nedan följer en översikt på hög nivå över de steg som krävs för att konfigurera federerade enkel inloggning med SAML 2.0 för ett icke-galleri (t.ex. anpassat) program.

-   Konfigurera programmets metadatavärden i Azure AD (Sign on URL, Identifier, Reply URL)

-   [Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och -certifikat](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurera Azure AD-metadatavärden i programmet (Logga in på URL, Utfärdare, URL för utloggning och certifikat)

-   Tilldela användare till programmet

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurera enkel inloggning till program som inte är gallerier

Så här konfigurerar du enkel inloggning för ett program som inte finns i Azure AD-galleriet nedan:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6. klicka på **Program för icke-galleri** i avsnittet **Lägg till en egen app**

7. Ange namnet på programmet i textrutan **Namn.**

8. Klicka på **Lägg** till om du vill lägga till programmet.

9. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

10. Välj **SAML-baserad inloggning** i listrutan **Läge.**

11. Ange de värden som krävs i **domän och webbadresser.** Du bör hämta dessa värden från programleverantören.

    1. Om du vill konfigurera programmet som IdP-initierad SSO anger du svars-URL:en och identifieraren.

    2. **Valfritt:** Om du vill konfigurera programmet som SP-initierad SSO är inloggnings-URL:en ett obligatoriskt värde.

12. I **användarattributen**väljer du den unika identifieraren för användarna i **listrutan Användaridentifierare.**

13. **Valfritt:** Klicka på **Visa och redigera alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

14. Klicka på ** &lt;Konfigurera programnamn&gt; ** för att komma åt dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också Azure AD-URL:er och certifikat som krävs för programmet.

15. [Tilldela användare till programmet.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet

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

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller -certifikatet

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

Azure AD tillhandahåller också en URL för att hämta metadata. Följ det här mönstret för att `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`hämta metadata-URL:en som är specifik för programmet: .

## <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Så här tilldelar du en eller flera användare ett program direkt:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

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

Efter en kort tid kan de användare som du har valt starta dessa program med de metoder som beskrivs i avsnittet lösningsbeskrivning.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Anpassa SAML-anspråk som skickas till ett program

Mer information om hur du anpassar SAML-attributanspråk som skickas till ditt program finns [i Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)
