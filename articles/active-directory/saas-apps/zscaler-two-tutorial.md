---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler två | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler två.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25b69f13cbf742c2bb98367a3181e45162715d67
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192633"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Självstudier: Azure Active Directory-integrering med två Zscaler

I den här självstudien får du lära dig hur du integrerar Zscaler två med Azure Active Directory (AD Azure).

Integrera Zscaler två med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler två.
- Du kan aktivera användarna att automatiskt få loggat in på två Zscaler (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler två, behöver du följande objekt:

- En Azure AD-prenumeration
- En Zscaler två enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till två Zscaler från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zscaler-two-from-the-gallery"></a>Att lägga till två Zscaler från galleriet

För att konfigurera integrering av Zscaler två i Azure AD, som du behöver lägga till två Zscaler från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till två Zscaler från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zscaler två**väljer **Zscaler två** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Zscaler två i resultatlistan](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler två baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i två Zscaler är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i två Zscaler upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler två, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Zscaler två testanvändare](#creating-a-zscaler-two-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler två som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zscaler två program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler två:**

1. I Azure-portalen på den **Zscaler två** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Zscaler två domän och URL: er med enkel inloggning för information](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

    Skriv den URL som används av användarna logga in till ditt ZScaler två program i inloggnings-URL-textrutan.

    > [!NOTE] 
    > Du måste uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Zscaler två klienten supportteamet](https://www.zscaler.com/company/contact) att hämta dessa värden.

5. Zscaler två program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan för **användarattribut och anspråk**.

    ![Länken Attribut](./media/zscaler-two-tutorial/tutorial_zscalertwo_attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn  | Källattribut  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Från listan över **Källattribut** väljer du attributvärdet.

    c. Klicka på **OK**.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Klicka på [här](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) för att få veta hur du konfigurerar rollen i Azure AD

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

8. På den **ställa in två Zscaler** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![Zscaler två konfiguration](common/configuresection.png)

9. I ett annat webbläsarfönster logga du in på webbplatsen Zscaler två företag som administratör.

10. Gå till **Administration > Autentisering > Autentiseringsinställningar** och utför följande steg:
   
    ![Administration](./media/zscaler-two-tutorial/ic800206.png "Administration")

    a. Under autentiseringstyp väljer du **SAML**.

    b. Klicka på **Konfigurera SAML**.

11. I fönstret **Redigera SAML** utför du följande steg och klickar på Spara.  
            
    ![Hantera användare och autentisering](./media/zscaler-two-tutorial/ic800208.png "Hantera användare och autentisering")
    
    a. I textrutan för **URL:en för SAML-portalen** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **attribut för inloggningsnamn** anger du **NameID**.

    c. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    d. Reglera **Enable SAML Auto-Provisioning** (Aktivera automatisk etablering av SAML).

    e. I textrutan för **användarens visningsnamnattribut** anger du **displayName** om du vill aktivera automatisk SAML-etablering för attributet displayName.

    f. I textrutan **User Display Name Attribute** (Gruppnamnattribut) anger du **memberOf** om du vill aktivera automatisk SAML-etablering för attributet memberOf.

    g. I textrutan för **avdelningsnamnattribut** anger du **department** om du vill aktivera automatisk SAML-etablering för department-attribut.

    i. Klicka på **Spara**.

12. I dialogrutan **Konfigurera användarautentisering** utför du följande steg:

    ![Administration](./media/zscaler-two-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>För att konfigurera proxyinställningar i Internet Explorer

1. Starta **Internet Explorer**.

1. Välj **Internetalternativ** i menyn **Verktyg** för att öppna dialogrutan **Internetalternativ**.   
    
     ![Internetalternativ](./media/zscaler-two-tutorial/ic769492.png "Internetalternativ")

1. Klicka på fliken **Anslutningar**.   
  
     ![Anslutningar](./media/zscaler-two-tutorial/ic769493.png "Anslutningar")

1. Klicka på **LAN-inställningar** för att öppna dialogrutan **LAN-inställningar**.

1. I avsnittet Proxyserver utför du följande steg:   
   
    ![Proxyserver](./media/zscaler-two-tutorial/ic769494.png "Proxyserver")

    a. Välj **Use a proxy server for your LAN** (Använd en proxyserver för ditt lokala nätverk).

    b. Ange i textrutan adress **gateway. Zscaler Two.net**.

    c. I textrutan Port skriver du **80**.

    d. Välj **Bypass proxy server for local addresses** (Kringgå proxyservern för lokala adresser).

    e. Klicka på **OK** för att stänga dialogrutan **Local Area Network (LAN) Settings** (Inställningar för lokalt nätverk (LAN)).

1. Klicka på **OK** för att stänga dialogrutan **Internetalternativ**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

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

### <a name="creating-a-zscaler-two-test-user"></a>Skapa en Zscaler två testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Zscaler två. Zscaler två stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Zscaler två om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Zscaler två supportteam](https://www.zscaler.com/company/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler två.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Zscaler två**.

    ![Konfigurera enkel inloggning](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen och välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I dialogrutan **Användare och grupper** väljer du användaren **Britta Simon** i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. I dialogrutan **Välj roll** väljer du lämplig användarroll i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler två i åtkomstpanelen du bör få automatiskt loggat in på ditt Zscaler två program.
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
