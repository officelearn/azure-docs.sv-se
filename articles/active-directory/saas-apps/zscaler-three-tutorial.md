---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler tre | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler tre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: 442d7478669a206f04cb799cb86b807cfe7b0624
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790205"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Självstudier: Azure Active Directory-integrering med tre Zscaler

I den här självstudien får du lära dig hur du integrerar Zscaler tre med Azure Active Directory (AD Azure).

Integrera Zscaler tre med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler tre.
- Du kan aktivera användarna att automatiskt få loggat in på tre Zscaler (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler tre, behöver du följande objekt:

- En Azure AD-prenumeration
- En Zscaler tre enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till tre Zscaler från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zscaler-three-from-the-gallery"></a>Att lägga till tre Zscaler från galleriet

Om du vill konfigurera integreringen av Zscaler tre till Azure AD, som du behöver lägga till tre Zscaler från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till tre Zscaler från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zscaler tre**väljer **Zscaler tre** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Zscaler tre i resultatlistan](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler tre baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Zscaler tre är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i tre Zscaler upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler tre, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler tre enkel inloggning](#configure-zscaler-three-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa tre Zscaler testanvändare](#create-zscaler-three-test-user)**  – du har en motsvarighet för Britta Simon i Cisco samlingsnamnet som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zscaler tre program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler tre:**

1. I Azure-portalen på den **Zscaler tre** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Zscaler tre domän och URL: er med enkel inloggning för information](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://login.zscalerthree.net/sfc_sso`

5. Zscaler tre program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan för **användarattribut och anspråk**.

    ![Länken Attribut](./media/zscaler-three-tutorial/tutorial_zscalerthree_attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn  | Källattribut  |
    | ---------| ------------ |
    | MemberOf     | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Från listan över **Källattribut** väljer du attributvärdet.

    c. Klicka på **OK**.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Klicka på [här](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) för att få veta hur du konfigurerar rollen i Azure AD

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png) 

8. På den **konfigurera Zscaler tre** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![Zscaler tre konfiguration](common/configuresection.png)

### <a name="configure-zscaler-three-single-sign-on"></a>Konfigurera Zscaler tre enkel inloggning

9. I ett annat webbläsarfönster logga du in på webbplatsen Zscaler tre företag som administratör.

10. Gå till **Administration > Autentisering > Autentiseringsinställningar** och utför följande steg:
   
    ![Administration](./media/zscaler-three-tutorial/ic800206.png "Administration")

    a. Under autentiseringstyp väljer du **SAML**.

    b. Klicka på **Konfigurera SAML**.

11. På den **redigera SAML** och utför följande steg: och klicka på Spara.  
            
    ![Hantera användare och autentisering](./media/zscaler-three-tutorial/ic800208.png "Hantera användare och autentisering")
    
    a. I textrutan för **URL:en för SAML-portalen** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **attribut för inloggningsnamn** anger du **NameID**.

    c. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    d. Reglera **Enable SAML Auto-Provisioning** (Aktivera automatisk etablering av SAML).

    e. I textrutan för **användarens visningsnamnattribut** anger du **displayName** om du vill aktivera automatisk SAML-etablering för attributet displayName.

    f. I textrutan **User Display Name Attribute** (Gruppnamnattribut) anger du **memberOf** om du vill aktivera automatisk SAML-etablering för attributet memberOf.

    g. I textrutan för **avdelningsnamnattribut** anger du **department** om du vill aktivera automatisk SAML-etablering för department-attribut.

    i. Klicka på **Spara**.

12. I dialogrutan **Konfigurera användarautentisering** utför du följande steg:

    ![Administration](./media/zscaler-three-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurera proxyinställningarna i Internet Explorer

1. Starta **Internet Explorer**.

1. Välj **Internetalternativ** från den **verktyg** meny för att öppna den **Internetalternativ** dialogrutan.   
    
     ![Internetalternativ](./media/zscaler-three-tutorial/ic769492.png "Internet-alternativ")

1. Klicka på den **anslutningar** fliken.   
  
     ![Anslutningar](./media/zscaler-three-tutorial/ic769493.png "anslutningar")

1. Klicka på **LAN-inställningar** att öppna den **LAN-inställningar** dialogrutan.

1. Utför följande steg i avsnittet Proxy server:   
   
    ![Proxyserver](./media/zscaler-three-tutorial/ic769494.png "proxyserver")

    a. Välj **använder en proxyserver för ditt lokala nätverk**.

    b. Ange i textrutan adress **gateway. Zscaler Three.net**.

    c. Ange i textrutan Port **80**.

    d. Välj **Använd ingen proxyserver för lokala adresser**.

    e. Klicka på **OK** att Stäng den **inställningar för lokalt nätverk (LAN)** dialogrutan.

1. Klicka på **OK** att Stäng den **Internetalternativ** dialogrutan.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-zscaler-three-test-user"></a>Skapa tre Zscaler testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Zscaler tre. Zscaler tre stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Zscaler tre om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Zscaler tre supportteam](https://www.zscaler.com/company/contact).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler tre.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Zscaler tre**.

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen och välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan användaren som **Britta Simon** i listan och klicka sedan på den **Välj** längst ned på skärmen.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. Från den **Välj roll** dialogrutan Välj en lämplig användarroll i listan och klicka sedan på den **Välj** längst ned på skärmen.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler tre i åtkomstpanelen du bör få automatiskt loggat in på ditt Zscaler tre program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
