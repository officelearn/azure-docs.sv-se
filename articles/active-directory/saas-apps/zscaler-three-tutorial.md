---
title: 'Självstudier: Azure Active Directory-integration med Zscaler tre | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler tre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: b148967af0882993d8ab113bdf0fd3ad3835296f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092618"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Självstudier: Azure Active Directory-integration med tre Zscaler

I den här självstudien får du lära dig hur du integrerar Zscaler tre med Azure Active Directory (AD Azure).

Integrera Zscaler tre med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till tre Zscaler
- Du kan aktivera användarna att automatiskt få loggat in på tre Zscaler (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler tre, behöver du följande objekt:

- En Azure AD-prenumeration
- En Zscaler tre enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här: [– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till tre Zscaler från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zscaler-three-from-the-gallery"></a>Att lägga till tre Zscaler från galleriet

Om du vill konfigurera integreringen av Zscaler tre till Azure AD, som du behöver lägga till tre Zscaler från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till tre Zscaler från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

2. Gå till **företagsprogram** och välj sedan den **alla program** alternativet.

    ![Program][2]

3. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I resultatpanelen väljer **Zscaler tre**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler tre baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Zscaler tre är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i tre Zscaler upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler tre, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera proxyinställningar](#configuring-proxy-settings)**  – om du vill konfigurera proxyinställningarna i Internet Explorer
3. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en Zscaler tre testanvändare](#creating-a-zscaler-three-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler tre som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zscaler tre program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler tre:**

1. I Azure-portalen på den **Zscaler tre** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. Om du vill ändra till **SAML** -läge från ett annat ett annat läge, klickar du på **ändra enkel inloggning läge** på skärmen.

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    Ange URL: en i URL-textrutan inloggning: `https://login.zscalerthree.net/sfc_sso`

6. På den **SAML-signeringscertifikat** avsnittet, klicka på **hämta** att ladda ned **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. På den **konfigurera Zscaler tre** avsnittet, kopiera den **inloggnings-URL**.

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. I ett annat webbläsarfönster logga du in på webbplatsen Zscaler tre företag som administratör.

10. Klicka på menyn längst upp **Administration**.

    ![Administration](./media/zscaler-three-tutorial/ic800206.png "Administration")

9. Under **hanterar administratörer och roller**, klickar du på **hantera användare och autentisering**.

    ![Hantera användare och autentisering](./media/zscaler-three-tutorial/ic800207.png "hantera användare och autentisering")

10. I den **väljer autentiseringsalternativ för din organisation** avsnittet, utför följande steg:

    ![Autentisering](./media/zscaler-three-tutorial/ic800208.png "autentisering")

    a. Välj **autentisera med hjälp av SAML enkel inloggning**.

    b. Klicka på **konfigureras SAML enkel inloggning**.

11. På den **konfigurera SAML enkel inloggning parametrar** dialogrutan sida, utför följande steg och klicka sedan på **klar**

    ![Enkel inloggning](./media/zscaler-three-tutorial/ic800209.png "enkel inloggning")

    a. Klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen till den **URL för SAML-portalen som användarna skickas för autentisering** textrutan.

    b. I den **attributet som innehåller inloggningsnamn** textrutan typ **NameID**.

    c. Om du vill ladda upp din hämtade certifikatet klickar du på **Zscaler pem**.

    d. Välj **aktivera SAML Automatisk etablering**.

12. På den **konfigurera användarautentisering** dialogrutan utför följande steg:

    ![Administration](./media/zscaler-three-tutorial/ic800210.png "Administration")

    a. Klicka på **Spara**.

    b. Klicka på **aktivera nu**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurera proxyinställningarna i Internet Explorer

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** från den **verktyg** meny för att öppna den **Internetalternativ** dialogrutan.

     ![Internetalternativ](./media/zscaler-three-tutorial/ic769492.png "Internet-alternativ")

3. Klicka på den **anslutningar** fliken.
  
     ![Anslutningar](./media/zscaler-three-tutorial/ic769493.png "anslutningar")

4. Klicka på **LAN-inställningar** att öppna den **LAN-inställningar** dialogrutan.

5. Utför följande steg i avsnittet Proxy server:

    ![Proxyserver](./media/zscaler-three-tutorial/ic769494.png "proxyserver")

    a. Välj **använder en proxyserver för ditt lokala nätverk**.

    b. Ange i textrutan adress **gateway.zscalerthree.net**.

    c. Ange i textrutan Port **80**.

    d. Välj **Använd ingen proxyserver för lokala adresser**.

    e. Klicka på **OK** att Stäng den **inställningar för lokalt nätverk (LAN)** dialogrutan.

6. Klicka på **OK** att Stäng den **Internetalternativ** dialogrutan.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Klicka på **Skapa**.

### <a name="creating-a-zscaler-three-test-user"></a>Skapa en Zscaler tre testanvändare

Om du vill aktivera Azure AD-användare att logga in på tre Zscaler, måste de etableras till Zscaler tre. Om det finns tre Zscaler är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:

1. Logga in på din **Zscaler tre** klient.

2. Klicka på **Administration**.

    ![Administration](./media/zscaler-three-tutorial/ic781035.png "Administration")

3. Klicka på **Användarhantering**.

     ![Lägg till](./media/zscaler-three-tutorial/ic781036.png "Lägg till")

4. I den **användare** fliken **Lägg till**.

    ![Lägg till](./media/zscaler-three-tutorial/ic781037.png "Lägg till")

5. Utför följande steg i avsnittet Lägg till användare:

    ![Lägg till användare](./media/zscaler-three-tutorial/ic781038.png "lägga till användare")

    a. Skriv den **UserID**, **användarens visningsnamn**, **lösenord**, **Bekräfta lösenord**, och välj sedan **grupper**och **avdelning** av en giltig Azure AD-konto som du vill etablera.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra Zscaler tre användare konto verktyg för att skapa eller API: er som tillhandahålls av Zscaler tre för att etablera användarkonton i Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler tre.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Zscaler tre**.

    ![Konfigurera enkel inloggning](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till användare** knappen och välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan klickar du på den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler tre i åtkomstpanelen du bör få automatiskt loggat in på ditt Zscaler tre program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png