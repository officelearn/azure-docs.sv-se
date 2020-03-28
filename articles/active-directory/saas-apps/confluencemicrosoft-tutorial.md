---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Sammanflödet SAML SSO av Microsoft | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Confluence SAML SSO från Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6ea52474f3b352fabf19050f20012438e9d0c19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120651"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Sammanflödet SAML SSO av Microsoft

I den här självstudien får du lära dig hur du integrerar Sammanflödet av SAML SSO från Microsoft med Azure Active Directory (Azure AD). När du integrerar Saml SSO från Microsoft med Microsoft med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Sammanflödet SAML SSO av Microsoft.
* Gör att användarna automatiskt loggas in på SamL SSO för sammanflödet av Microsoft med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Beskrivning:

Använd ditt Microsoft Azure Active Directory-konto med Atlassian Confluence-servern för att aktivera enkel inloggning. På så sätt kan alla organisationens användare använda Azure AD-autentiseringsuppgifterna för att logga in på sammanflödet. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Confluence SAML SSO från Microsoft behöver du följande:

- En Azure AD-prenumeration
- Confluence-serverprogrammet installerat på en Windows 64-bitars server (lokalt eller i moln-IaaS-infrastrukturen)
- Confluence-servern är HTTPS-aktiverad
- Observera att de versioner som stöds för Confluence-plugin-programmet anges i avsnittet nedan.
- Confluence-servern kan nås via Internet, särskilt för Azure AD-inloggningssidan för autentisering, och bör kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter konfigureras i Confluence
- WebSudo är inaktiverat i Confluence
- Testanvändare har skapats i Confluence-serverprogrammet

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte att du använder en produktionsmiljö i Confluence. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

För att komma igång behöver du följande:

* Använd inte din produktionsmiljö om det inte behövs.
* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Sammanflödet SAML SSO av Microsoft single sign-on (SSO) aktiverat prenumeration.

## <a name="supported-versions-of-confluence"></a>Versioner av Confluence som stöds

För närvarande stöds följande versioner av Confluence:

- Sammanflödet: 5,0 till 5,10
- Sammanflödet: 6.0.1 till 6.15.9
- Sammanflödet: 7.0.1 till 7.1.0

> [!NOTE]
> Observera att vår Sammanflödet Plugin fungerar också på Ubuntu Version 16.04

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Confluence SAML SSO från Microsoft stöder **IDP**-initierad enkel inloggning

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Lägga till Confluence SAML SSO från Microsoft från galleriet

För att konfigurera integreringen av Confluence SAML SSO från Microsoft till Azure AD behöver du lägga till Confluence SAML SSO från Microsoft från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv Sammanflödet av **SAML SSO från Microsoft** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Sammanflödet AV SAML SSO från Microsoft** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Konfigurera och testa Azure AD enkel inloggning för Sammanflödet SAML SSO av Microsoft

Konfigurera och testa Azure AD SSO med Sammanflödet SAML SSO av Microsoft med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Sammanflödet AV SAML SSO från Microsoft.

Om du vill konfigurera och testa Azure AD SSO med Sammanflödet AV SAML SSO från Microsoft slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Sammanflödet AV SAML SSO av Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Sammanflödet SAML SSO av Microsoft testanvändare](#create-confluence-saml-sso-by-microsoft-test-user)** - att ha en motsvarighet till B.Simon i Sammanflödet SAML SSO av Microsoft som är kopplad till Azure AD representation av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Saml SSO för sammanflödet av Microsoft-program** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<domain:port>/plugins/servlet/saml/auth`

    b. Skriv en URL med följande mönster i rutan **Identifierare:**`https://<domain:port>/`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Porten är valfri ifall den är en namngiven URL. Dessa värden tas emot under konfigurationen av Confluence-pluginprogrammet, som beskrivs senare i självstudien.

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Sammanflödet AV SAML SSO av Microsoft.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Confluence SAML SSO från Microsoft**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Konfigurera Sammanflödet SAML SSO av Microsoft SSO

1. I ett annat webbläsarfönster loggar du in på sammanflödet som administratör.

1. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon1.png)

1. Ladda ned plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Ladda manuellt upp det plugin-program som tillhandahålls av Microsoft med hjälp av menyn **Upload add-on** (Ladda upp tillägg). Nedladdningen av plugin-programmet täcks av [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon12.png)

1. För att köra scenariot Sammanflödet återför proxy eller belastningsutjämnad scenario utföra följande steg:

    > [!NOTE]
    > Du bör konfigurera servern först med instruktionerna nedan och därefter installera plugin-programmet.

    a. Lägg till attributen nedan i **anslutningsappens** port i filen **server.xml** för JIRA-serverprogram.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Ändra **grundläggande URL** i **Systeminställningar** enligt proxy/lastbalanserare.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. När plugin-programmet har installerats visas det i avsnittet för **användarinstallerade** tillägg i avsnittet **Manage Add-on** (Hantera tillägg). Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon15.png)

1. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Kontrollera att det bara finns ett certifikat mappat mot appen så att det inte förekommer några fel vid lösningen av metadata. Om det finns flera certifikat får administratören ett fel vid lösningen av metadata.

    1. I textrutan **Metadata URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på knappen **Lös**. Den läser metadata-URL för IdP och fyller i information i alla fält.

    1. Kopiera värdena för **identifierare, svars-URL och inloggnings-URL** och klistra in dem respektive textrutor för **identifierare, svars-URL och inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    1. I **Login Button Name** (Namn på inloggningsknapp) skriver du namnet på den knapp som organisationen vill att användarna ser på inloggningsskärmen.
    
    1. I **Inloggningsknapp Beskrivning** skriver beskrivningen av knappen som din organisation vill att användarna ska se på inloggningsskärmen.

    1. I **SAML User ID Locations** (Platser för SAML-användar-ID) väljer du antingen **User ID is in the NameIdentifier element of the Subject statement** (Användar-ID finns i elementet NameIdentifieri instruktionen Ämne) eller **User ID is in an Attribute element** (Användar-ID finns i ett Attribut-element).  Detta ID måste vara confluence användar-ID. Om användar-ID:et inte matchas tillåter systemet inte att användare loggar in. 

       > [!Note]
       > Standardplatsen för SAML-användar-ID är Name Identifier (Namnidentifierare). Du kan ändra den till ett attributalternativ och ange lämpligt attributnamn.

    1. Om du väljer **Användar-ID finns i alternativet Attributelement** skriver du namnet på det attribut där användar-ID förväntas i textrutan **Attributnamn.** 

    1. Om du använder den federerade domänen (till exempel ADFS osv) med Azure AD klickar du på alternativet **Enable Home Realm Discovery** (Aktivera identifiering av hemsfär) och konfigurerar **Domännamn**.

    1. I **Domännamn** anger du domännamnet för ADFS-baserad inloggning.

    1. Markera **Aktivera enkel inloggning** om du vill logga ut från Azure AD när en användare loggar ut från sammanflödet. 

    1. Aktivera **Force Azure-inloggningsruta** om du bara vill logga in via Azure AD-autentiseringsuppgifter.

       > [!Note]
       > Om du vill aktivera standardinloggningsformuläret för administratörsinloggning på inloggningssidan när force azure-inloggningen är aktiverad lägger du till frågeparametern i webbläsarens URL.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Klicka på knappen **Spara** för att spara inställningarna.

       > [!NOTE]
       > Mer information om installation och felsökning finns i [MS Confluence SSO Connector Admin Guide](../ms-confluence-jira-plugin-adminguide.md). Det finns också en [FAQ](../ms-confluence-jira-plugin-faq.md) för din hjälp.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Skapa Confluence SAML SSO från Microsoft-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på den lokala servern för sammanflödet måste de etableras i Sammanflödet av SAML SSO av Microsoft. När det gäller Confluence SAML SSO från Microsoft är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på den lokala servern som administratör.

1. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/confluencemicrosoft-tutorial/user1.png)

1. Klicka på **Fliken Lägg till användare** under avsnittet Användare. Gör följande på dialogrutan **Lägg till en användare:**

    ![Lägga till medarbetare](./media/confluencemicrosoft-tutorial/user2.png)

    a. Skriv e-postmeddelandet till användare som B.Simon i textrutan **Användarnamn.**

    b. Skriv det fullständiga namnet på användaren som B.Simon i textrutan **Fullständigt namn.**

    c. I textrutan **E-post** skriver du e-postadressen för användaren: B.Simon@contoso.com.

    d. Skriv lösenordet för B.Simon i textrutan **Lösenord.**

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.

    f. Klicka på knappen **Lägg till**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Confluence SAML SSO från Microsoft-panelen i åtkomstpanelen bör du automatiskt loggas in på Confluence SAML SSO från Microsoft som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Sammanflödet SAML SSO från Microsoft med Azure AD](https://aad.portal.azure.com/)
