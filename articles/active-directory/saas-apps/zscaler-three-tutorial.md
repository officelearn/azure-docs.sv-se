---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler tre | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler tre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/09/2019
ms.author: jeedes
ms.openlocfilehash: 4a7ddc6b9f4a8638d18262e458d61108c00b618d
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564561"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Självstudier: Azure Active Directory-integrering med tre Zscaler

I den här självstudien får du lära dig hur du integrerar Zscaler tre med Azure Active Directory (AD Azure).
Integrera Zscaler tre med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Zscaler tre.
* Du kan aktivera användarna att vara automatiskt inloggad till tre Zscaler (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler tre, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Zscaler tre enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler tre stöder **SP** -initierad SSO

* Zscaler tre stöder **Just In Time** etableringen av användare

## <a name="adding-zscaler-three-from-the-gallery"></a>Att lägga till tre Zscaler från galleriet

Om du vill konfigurera integreringen av Zscaler tre till Azure AD, som du behöver lägga till tre Zscaler från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till tre Zscaler från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Zscaler tre**väljer **Zscaler tre** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Zscaler tre i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler tre baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i tre Zscaler upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler tre, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler tre enkel inloggning](#configure-zscaler-three-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa tre Zscaler testanvändare](#create-zscaler-three-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler tre som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler tre:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Zscaler tre** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Zscaler tre domän och URL: er med enkel inloggning för information](common/sp-intiated.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://login.zscalerthree.net/sfc_sso`

5. Zscaler tre programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

6. Förutom ovanstående Zscaler tre program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Namn | Källattribut |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.
    
    f. Klicka på **Spara**.

    > [!NOTE]
    > Klicka på [här](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) för att få veta hur du konfigurerar rollen i Azure AD

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. På den **konfigurera Zscaler tre** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zscaler-three-single-sign-on"></a>Konfigurera Zscaler tre enkel inloggning

1. I ett annat webbläsarfönster, loggar du in på webbplatsen Zscaler tre företag som administratör.

2. Gå till **Administration > Autentisering > Autentiseringsinställningar** och utför följande steg:
   
    ![Administration](./media/zscaler-three-tutorial/ic800206.png "Administration")

    a. Under autentiseringstyp väljer du **SAML**.

    b. Klicka på **Konfigurera SAML**.

3. I fönstret **Redigera SAML** utför du följande steg och klickar på Spara.  
            
    ![Hantera användare och autentisering](./media/zscaler-three-tutorial/ic800208.png "Hantera användare och autentisering")
    
    a. I textrutan för **URL:en för SAML-portalen** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **attribut för inloggningsnamn** anger du **NameID**.

    c. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    d. Reglera **Enable SAML Auto-Provisioning** (Aktivera automatisk etablering av SAML).

    e. I textrutan för **användarens visningsnamnattribut** anger du **displayName** om du vill aktivera automatisk SAML-etablering för attributet displayName.

    f. I textrutan **User Display Name Attribute** (Gruppnamnattribut) anger du **memberOf** om du vill aktivera automatisk SAML-etablering för attributet memberOf.

    g. I textrutan för **avdelningsnamnattribut** anger du **department** om du vill aktivera automatisk SAML-etablering för department-attribut.

    h. Klicka på **Spara**.

4. I dialogrutan **Konfigurera användarautentisering** utför du följande steg:

    ![Administration](./media/zscaler-three-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>För att konfigurera proxyinställningar i Internet Explorer

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** i menyn **Verktyg** för att öppna dialogrutan **Internetalternativ**.   
    
     ![Internetalternativ](./media/zscaler-three-tutorial/ic769492.png "Internetalternativ")

3. Klicka på fliken **Anslutningar**.   
  
     ![Anslutningar](./media/zscaler-three-tutorial/ic769493.png "Anslutningar")

4. Klicka på **LAN-inställningar** för att öppna dialogrutan **LAN-inställningar**.

5. I avsnittet Proxyserver utför du följande steg:   
   
    ![Proxyserver](./media/zscaler-three-tutorial/ic769494.png "Proxyserver")

    a. Välj **Use a proxy server for your LAN** (Använd en proxyserver för ditt lokala nätverk).

    b. Ange i textrutan adress **gateway. Zscaler Three.net**.

    c. I textrutan Port skriver du **80**.

    d. Välj **Bypass proxy server for local addresses** (Kringgå proxyservern för lokala adresser).

    e. Klicka på **OK** för att stänga dialogrutan **Local Area Network (LAN) Settings** (Inställningar för lokalt nätverk (LAN)).

6. Klicka på **OK** för att stänga dialogrutan **Internetalternativ**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler tre.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Zscaler tre**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zscaler tre**.

    ![Zscaler tre länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du användaren **Britta Simon** i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. I dialogrutan **Välj roll** väljer du lämplig användarroll i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="create-zscaler-three-test-user"></a>Skapa tre Zscaler testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Zscaler tre. Zscaler tre stöder just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i tre Zscaler, skapas en ny när du försöker komma åt Zscaler tre.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Zscaler tre supportteam](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler tre i åtkomstpanelen, bör det vara loggas in automatiskt till den Zscaler tre som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

