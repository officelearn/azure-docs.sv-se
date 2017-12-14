---
title: "Självstudier: Integrera Azure Active Directory med vxMaintain | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och vxMaintain."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: a987247d2c8a76161f9c8a5a027e34b3a8e25b30
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-vxmaintain"></a>Självstudier: Integrera Azure Active Directory med vxMaintain

I kursen får lära du att integrera vxMaintain med Azure Active Directory (AD Azure).

Den här integreringen ger flera viktiga fördelar. Du kan:

- Kontrollera i Azure AD som har åtkomst till vxMaintain.
- Ge användarna logga in automatiskt vxMaintain med enkel inloggning (SSO) med hjälp av sina Azure AD-konton.
- Hantera dina konton i en central plats: Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med vxMaintain, behöver du följande:

- En Azure AD-prenumeration
- VxMaintain SSO-aktiverade prenumeration

> [!NOTE]
> När du testar stegen i den här självstudiekursen, rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

* Att lägga till vxMaintain från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-vxmaintain-from-the-gallery"></a>Lägg till vxMaintain från galleriet
Du måste lägga till vxMaintain från galleriet i listan över hanterade SaaS-appar för att konfigurera vxMaintain-integrering med Azure AD.

Lägg till vxMaintain från galleriet genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** knappen. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret ”företagsprogram”][2]
    
3. Att lägga till ett program i den **alla program** dialogrutan **nytt program**.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **vxMaintain**.

    ![Listrutan ”enkel inloggning läge”](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Välj i resultatlistan **vxMaintain**, och välj sedan **Lägg till**.

    ![Länken vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD SSO med hjälp av vxMaintain, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vxMaintain motsvarighet till Azure AD-användare. Det vill säga måste du upprätta en länk relation mellan Azure AD-användare och motsvarande vxMaintain användaren.

Om du vill upprätta en länk relation, tilldela vxMaintain **användarnamn** värde som Azure AD **användarnamn** värde.

Slutför följande byggblock för att konfigurera och testa Azure AD SSO med vxMaintain.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD för enkel inloggning

I det här avsnittet kan du både aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet vxMaintain genom att göra följande:

1. I Azure-portalen på den **vxMaintain** programmet integration anger **enkel inloggning**.

    ![Kommandot ”enkel inloggning”][4]

2. Att aktivera enkel inloggning, i den **läget för enkel inloggning** listrutan, Välj **SAML-baserade inloggning**.
 
    ![Kommandot ”SAML-baserade inloggning”](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. Under **vxMaintain domän och URL: er**, gör du följande:

    ![VxMaintain domän och URL: er](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. I den **identifierare** skriver en URL som har följande syntax:`https://<company name>.verisae.com`

    b. I den **Reply URL** skriver en URL som har följande syntax:`https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med den faktiska identifieraren och reply URL. För att få värdena kan kontakta den [vxMaintain supportteamet](http://www.verisae.com/contact-us).
 
4. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**, och spara sedan metadatafilen till datorn.

    ![Avsnittet ”SAML signeringscertifikat”](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Välj **Spara**.

    ![Knappen Spara](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Så här konfigurerar du **vxMaintain** SSO skicka den hämtade **XML-Metadata för** filen till den [vxMaintain supportteamet](http://www.verisae.com/contact-us).

> [!TIP]
> När du skapar appen kan du läsa en kortare version av föregående anvisningarna i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan använda den inbäddade dokumentation från den **Configuration** avsnitt. 
>
>Mer information om funktionen inbäddade dokumentation finns [hantera enkel inloggning för företagsappar](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen genom att göra följande:

![Azure AD-testanvändare][100]

1. I den **Azure-portalen**, i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Knappen ”Azure Active Directory”](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** > **alla användare**.
    
    ![Länken ”alla användare”](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    Den **alla användare** öppnas. 

3. Öppna den **användare** dialogrutan **Lägg till**.
 
    ![Knappen Lägg till](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan Gör följande:
 
    ![Dialogrutan användare](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver testanvändare Britta Simon e-postadress.

    c. Välj den **visa lösenordet** och anteckna värdet som har genererats i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-vxmaintain-test-user"></a>Skapa en testanvändare vxMaintain

I det här avsnittet skapar du testanvändare Britta Simon i vxMaintain. Om du vill lägga till användare i plattformen vxMaintain arbeta med den [vxMaintain supportteamet](http://www.verisae.com/contact-us). Innan du använder SSO, skapa och aktivera användarna.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera testanvändare Britta Simon att använda Azure SSO genom att bevilja åtkomst till vxMaintain. Det gör du genom att göra följande:

![Testanvändare i listan visningsnamn][200] 

1. I Azure portal **program** visa, gå till **Directory** Visa > **företagsprogram** > **alla program**.

    ![Länken ”alla program”][201] 

2. I den **program** väljer **vxMaintain**.

    ![Länken vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. I den vänstra rutan, Välj **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Välj **Lägg till** och sedan, i den **Lägg uppdrag** väljer **användare och grupper**.

    ![Länken ”användare och grupper”][203]

5. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**, och välj sedan den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan **tilldela**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testa din Azure AD enkel inloggning

I det här avsnittet testa du konfigurationen av Azure AD SSO med hjälp av åtkomstpanelen.

Att välja den **vxMaintain** panelen på åtkomstpanelen ska logga in i tillämpningsprogrammet vxMaintain automatiskt.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Nästa steg

* [Lista över självstudier om att integrera SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

