---
title: 'Självstudiekurs: Azure Active Directory-integrering med SAP Analytics Cloud | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Analytics Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68347791"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Självstudiekurs: Integrera SAP Analytics Cloud med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar SAP Analytics Cloud med Azure Active Directory (Azure AD). När du integrerar SAP Analytics Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Analytics Cloud.
* Gör att användarna automatiskt loggas in i SAP Analytics Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Sap Analytics Cloud enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SAP Analytics Cloud stöder **SP** initierade SSO

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Lägga till SAP Analytics Cloud från galleriet

Om du vill konfigurera integreringen av SAP Analytics Cloud i Azure AD måste du lägga till SAP Analytics Cloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SAP Analytics Cloud** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **SAP Analytics Cloud** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med SAP Analytics Cloud med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Analytics Cloud.

Så här konfigurerar och testar du Azure AD SSO med SAP Analytics Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SAP Analytics Cloud SSO](#configure-sap-analytics-cloud-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa SAP Analytics Cloud-testanvändare](#create-sap-analytics-cloud-test-user)** – om du vill ha en motsvarighet till B.Simon i SAP Analytics Cloud som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan INTEGRERING av SAP **Manage** Analytics **Cloud-program**hittar du avsnittet Hantera och väljer Enkel inloggning . **SAP Analytics Cloud**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Värdena i dessa URL:er är bara exempel. Uppdatera värden med de faktisk inloggnings-URL och identifierar-URL. Kontakta [SUPPORTTEAMET](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)för SAP Analytics Cloud Client om du vill hämta inloggnings-URL:en . Du kan hämta identifierarens URL genom att hämta SAP Analytics Cloud-metadata från administratörskonsolen. Detta beskrivs senare i självstudien.

4. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera SAP Analytics Cloud.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Konfigurera SAP Analytics Cloud SSO

1. Logga in på din webbplats för SAP Analytics Cloud som administratör i ett annat webbläsarfönster.

2. Välj**System** > **Systemadministration** **på meny** > .
    
    ![Välj Meny, sedan System och därefter Administration](./media/sapboc-tutorial/config1.png)

3. På fliken **Säkerhet** väljer du ikonen **Redigera** (pennan).
    
    ![På fliken Säkerhet väljer du ikonen Redigera](./media/sapboc-tutorial/config2.png)  

4. För **Autentiseringsmetod** väljer du **SAML Single Sign-On (SSO)** (Enkel inloggning (SSO) med SAML).

    ![Välja enkel inloggning med SAML som autentiseringsmetod](./media/sapboc-tutorial/config3.png)  

5. Ladda ned metadata för tjänstleverantör (steg 1) genom att välja **Ladda ned**. I metadatafilen letar du upp och kopierar värdet för **entityID**. I dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen klistrar du in värdet i rutan **Identifierare**.

    ![Kopiera och klistra in värdet för entityID](./media/sapboc-tutorial/config4.png)  

6. Ladda upp metadata för tjänstleverantör (steg 2) i den fil som du laddade ned från Azure-portalen genom att gå till **Upload Identity Provider metadata** (Ladda upp metadata för tjänstleverantör) och välja **Ladda upp**.  

    ![Under Upload Identity Provider metadata (Ladda upp metadata för tjänstleverantör) väljer du Ladda upp](./media/sapboc-tutorial/config5.png)

7. I listan **Användarattribut** väljer du det användarattribut (steg 3) som du vill använda för implementeringen. Det här användarattributet mappar till identitetsprovidern. Du ange ett anpassat attribut på användarens sida genom att använda alternativet **Custom SAML Mapping** (Anpassad SAML-mappning). Eller så kan du välja antingen **Email** (E-post) eller **USER ID** (Användar-ID) som användarattribut. I vårt exempel valde vi **Email** (E-post) eftersom vi mappade anspråket för användar-ID med attributet **userprincipalname** i avsnittet **Användarattribut och anspråk** i Azure-portalen. Detta ger en unik användare e-post, som skickas till SAP Analytics Cloud ansökan i varje lyckad SAML svar.

    ![Välja användarattribut](./media/sapboc-tutorial/config6.png)

8. Verifiera kontot med identitetsprovidern (steg 4) genom att gå till rutan **Login Credential (Email)** (Autentiseringsuppgifter (e-post)) och ange användarens e-postadress. Välj sedan **Verify Account** (Verifiera konto). Systemet lägger till autentiseringsuppgifter till användarkontot.

    ![Ange e-postadress och välj Verify Account (Verifiera konto)](./media/sapboc-tutorial/config7.png)

9. Välj ikonen **Spara**.

    ![Ikonen Spara](./media/sapboc-tutorial/save.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Analytics Cloud.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj SAP Analytics **Cloud**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-sap-analytics-cloud-test-user"></a>Skapa TESTANVÄNDARE FÖR SAP Analytics Cloud

Azure AD-användare måste etableras i SAP Analytics Cloud innan de kan logga in på SAP Analytics Cloud. I SAP Analytics Cloud är etablering en manuell uppgift.

Så här etablerar du ett användarkonto:

1. Logga in på din SAP Analytics Cloud-företagswebbplats som administratör.

2. Välj **Användare av menysäkerhet** > **Security** > **.**

    ![Lägga till medarbetare](./media/sapboc-tutorial/user1.png)

3. På sidan **Användare** lägger du till nya användaruppgifter genom att välja **+**. 

    ![Sidan Lägg till användare](./media/sapboc-tutorial/user4.png)

    Utför sedan följande steg:

    a. I rutan **ANVÄNDAR-ID** anger du användarens användar-ID, till exempel **B**.

    b. Ange användarens förnamn i rutan **FÖRnamn,** till exempel **B**.

    c. I rutan **LAST NAME** (Efternamn) anger du efternamn för användaren: **Simon**.

    d. Ange användarens fullständiga namn i rutan **VISNINGSNAMN,** till exempel **B.Simon**.

    e. I rutan **E-MAIL** (E-postadress) anger du användarens e-postadress: `b.simon@contoso.com`.

    f. På sidan **Select Roles** (Välj roller) väljer du rätt roll för användaren och väljer sedan **OK**.

      ![Välja en roll](./media/sapboc-tutorial/user3.png)

    g. Välj ikonen **Spara**.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SAP Analytics Cloud på åtkomstpanelen bör du automatiskt loggas in i DET SAP Analytics-moln som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

