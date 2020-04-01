---
title: 'Självstudiekurs: Azure Active Directory-integrering med Amazon Business | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496912"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Självstudiekurs: Integrera Amazon Business med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Amazon Business med Azure Active Directory (Azure AD). När du integrerar [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Amazon Business.
* Gör det möjligt för användarna att automatiskt loggas in i Amazon Business med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* En Amazon Business enkel inloggning (SSO) aktiverat prenumeration. Gå till [Amazon Business-sidan](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) för att skapa ett Amazon Business-konto.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i ett befintligt Amazon Business-konto.

* Amazon Business stöder **SP och IDP** initierade SSO
* Amazon Business stöder **Just In Time** användare etablering

## <a name="adding-amazon-business-from-the-gallery"></a>Lägga till Amazon Business från galleriet

Om du vill konfigurera integreringen av Amazon Business i Azure AD måste du lägga till Amazon Business från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Amazon Business** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Amazon Business** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Amazon Business med en testanvändare som heter **B.Simon**.

Så här konfigurerar och testar du Azure AD SSO med Amazon Business:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Amazon Business SSO](#configure-amazon-business-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Amazon Business-testanvändare](#create-amazon-business-test-user)** – om du vill ha en motsvarighet till B.Simon i Amazon Business som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Gör följande i det **grundläggande SAML-konfigurationsavsnittet:** **IDP**

    1. I textrutan **Identifierare (Entitets-ID)** skriver du en URL med något av följande mönster:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. Skriv en URL med något av följande mönster i textrutan **Svara** URL:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > Värdet för svars-URL:en är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Du kommer `<idpid>` att få värdet från Amazon Business SSO konfiguration avsnitt, som förklaras senare i handledningen. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://www.amazon.com/`

1. I följande skärmbild visas listan över standardattribut. Redigera attributen genom att klicka på ikonen **Redigera** i avsnittet **Användarattribut & anspråk.**

    ![Attribut](media/amazon-business-tutorial/map-attribute3.png)

1. Redigera attribut och kopiera **namnområdesvärdet** för dessa attribut till Anteckningar.

    ![Attribut](media/amazon-business-tutorial/map-attribute4.png)

1. Förutom ovan, Amazon Business ansökan förväntar sig några fler attribut som skall skickas tillbaka i SAML svar. Gör följande i avsnittet **Användarattribut & anspråk** i dialogrutan **Gruppanspråk:**

    a. Klicka på **pennan** **bredvid Grupper som returneras i anspråk**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Välj **Alla grupper** i radiolistan.

    c. Välj **grupp-ID** som **källattribut**.

    d. Markera **Anpassa namnet på gruppanspråksrutan** och ange gruppnamnet enligt organisationens krav.

    e. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för metadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Amazon Business.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Konfigurera Amazon Business SSO

1. I ett annat webbläsarfönster loggar du in på amazonföretagswebbplatsen som administratör.

1. Klicka på **användarprofilen** och välj **Företagsinställningar**.

    ![Användarprofil](media/amazon-business-tutorial/user-profile.png)

1. I guiden **Systemintegrationer** väljer du **Enkel inloggning (SSO)**.

    ![Enkel inloggning (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. I guiden **Konfigurera SSO** väljer du leverantören enligt organisationens krav och klickar på **Nästa**.

    ![Standardgrupp](media/amazon-business-tutorial/default-group1.png)

1. I guiden Standard för **användarkonto** väljer du **standardgruppen** och väljer sedan **Standardköpsroll** enligt användarrollen i organisationen och klickar på **Nästa**.

    ![Standardgrupp](media/amazon-business-tutorial/dafault-group2.png)

1. Klicka på **Bläddra** i guiden Ladda upp **Metadata XML** metadatafilen i guiden **Ladda upp metadatafilen,** som du har hämtat från Azure-portalen och klicka på Ladda **upp**.

    ![Anslutningsdata](media/amazon-business-tutorial/connection-data1.png)

1. När du har laddat upp den nedladdade metadatafilen fylls fälten i avsnittet **Anslutningsdata** automatiskt. Efter det klickar du på **Nästa**.

    ![Anslutningsdata](media/amazon-business-tutorial/connection-data2.png)

1. Klicka på **Hoppa över**i guiden Ladda **upp attributets uttryck** .

    ![Attribut](media/amazon-business-tutorial/map-attribute1.png)

1. Lägg till kravfälten i guiden **Attributmappning** genom att klicka på alternativet **+ Lägg till ett fält.** Lägg till attributvärdena, inklusive namnområdet, som du har kopierat från avsnittet **Användarattribut & anspråk** i Azure-portalen i fältet **SAML-attributnamn** och klicka på **Nästa**.

    ![Attribut](media/amazon-business-tutorial/map-attribute2.png)

1. Klicka på **Nästa**i guiden **För Amazon-anslutningsdata** .

    ![Anslutning](media/amazon-business-tutorial/amazon-connect.png)

1. Kontrollera **status** för de steg som har konfigurerats och klicka på **Starta testning**.

    ![Anslutning](media/amazon-business-tutorial/sso-connection1.png)

1. Klicka på **Testa**i guiden **Testa SSO-anslutning.**

    ![Anslutning](media/amazon-business-tutorial/sso-connection2.png)

1. Kopiera **IDP initiated URL** värdet som har tilldelats **idpid** och klistra in i **parametern i idpid i idpid-parametern** i **avsnittet Svara** url i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. **Activate**

    ![Anslutning](media/amazon-business-tutorial/sso-connection3.png)

1. På **I have fully tested SSO and am ready to go live** **are you ready to switch to active SSO?** **Switch to active**

    ![Anslutning](media/amazon-business-tutorial/sso-connection4.png)

1. Slutligen visas **statusen som** **Aktiv**i avsnittet **SSO-anslutningsinformation** .

    ![Anslutning](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

> [!NOTE]
> Administratörer måste skapa testanvändarna i sin klientorganisation om det behövs. Följande steg visar hur du skapar en testanvändare.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure-portalen

1. Klicka på **Azure Active Directory > Alla grupper**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/amazon-business-tutorial/all-groups-tab.png)

1. Klicka på **Ny grupp:**

    ![Skapa en Azure AD-säkerhetsgrupp](./media/amazon-business-tutorial/new-group-tab.png)

1. Fyll i **grupptyp,** **Gruppnamn,** **Gruppbeskrivning**, **Medlemstyp**. Klicka på pilen för att välja medlemmar, sedan söka efter eller klicka på den medlem du vill lägga till i gruppen. Klicka på **Välj** om du vill lägga till de markerade medlemmarna och klicka sedan på **Skapa**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Amazon Business.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Amazon Business**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

    >[!NOTE]
    > Om du inte tilldelar användarna i Azure AD får du följande felmeddelande.

    ![Länken Lägg till användare](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Tilldela Azure AD-säkerhetsgruppen i Azure-portalen

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan Amazon **Business**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj Amazon **Business**i programlistan .

    ![Länken Amazon Business i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på **Lägg till användare**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Sök efter den säkerhetsgrupp som du vill använda och klicka sedan på gruppen för att lägga till den i avsnittet Välj medlemmar. Klicka på **Markera**och sedan på **Tilldela**.

    ![Sök i säkerhetsgruppen](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Kontrollera meddelandena i menyraden för att få ett meddelande om att gruppen har tilldelats Enterprise-programmet i Azure-portalen.

### <a name="create-amazon-business-test-user"></a>Skapa Amazon Business testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Amazon Business. Amazon Business stöder just-in-time användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Amazon Business skapas en ny efter autentisering.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Business i åtkomstpanelen ska du automatiskt loggas in på den Amazon Business som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
