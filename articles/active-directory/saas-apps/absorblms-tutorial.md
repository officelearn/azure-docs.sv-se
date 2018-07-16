---
title: 'Självstudier: Azure Active Directory-integration med absorbera LMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och absorbera LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 066ae92056e4b80b6627b371d6ebeb3235b2781d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043786"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Självstudier: Azure Active Directory-integration med absorbera LMS

Lär dig hur du integrerar absorbera LMS med Azure Active Directory (AD Azure) i den här självstudien.

Integrera absorbera LMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till absorbera LMS.
- Du kan aktivera användarna att logga in automatiskt till absorbera LMS (via enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Om du vill veta mer om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med absorbera LMS, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett LMS-absorbera enkel inloggning aktiverat prenumeration

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för den här självstudien.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

* Att lägga till absorbera LMS från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-absorb-lms-from-the-gallery"></a>Lägg till absorbera LMS från galleriet
Lägg till absorbera LMS från galleriet i din lista över hanterade SaaS-appar för att konfigurera integrering av absorbera LMS i Azure AD.

Om du vill lägga till absorbera LMS från galleriet, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Fönstret för Enterprise-program][2]
    
3. Om du vill lägga till ett program, Välj den **nytt program** knappen.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **absorbera LMS**väljer **absorbera LMS** i resultera panelen och välj sedan den **Lägg till** knappen.

    ![Absorbera LMS i resultatlistan](./media/absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med absorbera LMS-baserat på en testanvändare kallas Britta Simon.

För enkel inloggning att fungera, behöver Azure AD du veta vad absorbera LMS motsvarighet användaren är i Azure AD. Med andra ord måste du upprätta en länk förhållandet mellan en användare i Azure AD och motsvarande användaren i absorbera LMS.

Du skapar den här länken relation genom att tilldela den *användarnamn* värdet i Azure AD som den *användarnamn* värdet i absorbera LMS.

Om du vill konfigurera och testa Azure AD enkel inloggning med absorbera LMS, slutför du byggblocken i nästa fem avsnitt.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt absorbera LMS-program.

Om du vill konfigurera Azure AD enkel inloggning med absorbera LMS, gör du följande:

1. I Azure-portalen på den **absorbera LMS** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. I den **enkel inloggning** i dialogrutan den **läge** väljer **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. I den **absorbera LMS-domän och URL: er** avsnittet, gör du följande:

    ![Absorbera LMS-domän och URL: er enkel inloggning för information](./media/absorblms-tutorial/tutorial_absorblms_url.png)

    a. I den **identifierare** skriver en URL som använder följande syntax: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. I den **svars-URL** skriver en URL som använder följande syntax: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Dessa URL: er är inte det verkliga värdet. Uppdatera dem med de faktiska identifierare och svars-URL. För att få dessa värden kan kontakta den [absorbera LMS klienten supportteamet](https://www.absorblms.com/support). 

4. I den **SAML-signeringscertifikat** avsnittet i den **hämta** kolumnen, markerar **XML-Metadata för**, och spara sedan metadatafilen till datorn.

    ![Certifikat för tokensignering länken](./media/absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Välj **Spara**.

    ![Konfigurera enkel inloggning spara-knapp](./media/absorblms-tutorial/tutorial_general_400.png)
    
6. I den **absorbera LMS Configuration** väljer **konfigurera absorbera LMS** att öppna **konfigurera inloggning** fönstret och sedan kopiera den **URL för utloggning** i den **Snabbreferens avsnittet.**

    ![Fönstret absorbera LMS-konfiguration](./media/absorblms-tutorial/tutorial_absorblms_configure.png) 

7. I ett nytt webbläsarfönster, loggar du in företagets platsen absorbera LMS som administratör.

8. Välj den **konto** knappen längst upp till höger. 

    ![Knappen konto](./media/absorblms-tutorial/1.png)

9. I rutan väljer **portalinställningar**.

    ![Länken inställningar](./media/absorblms-tutorial/2.png)
    
10. Välj fliken **Användare**.

    ![Fliken användare](./media/absorblms-tutorial/3.png)

11. Gör följande på sidan konfiguration för enkel inloggning:

    ![Sidan konfiguration för enkel inloggning](./media/absorblms-tutorial/4.png)

    a. I den **läge** väljer **identitet providern initierade**.

    b. I anteckningar, öppnar du det certifikat som du laddade ned från Azure-portalen. Ta bort den **---BEGIN CERTIFICATE---** och **---END CERTIFICATE---** taggar. I den **nyckel** rutan, klistra in det återstående innehållet.
    
    c. I den **Id-egenskapen** väljer du det attribut som du har konfigurerat som användar-ID i Azure AD. Till exempel om *userPrincipalName* väljs i Azure AD, Välj **användarnamn**.

    d. I den **inloggnings-URL** rutan, klistra in den **URL för användaråtkomst** från programmets **egenskaper** i Azure-portalen.

    e. I den **URL för utloggning**, klistra in den **URL: en för utloggning** värde som du kopierade från den **konfigurera inloggning** fönstret i Azure-portalen.

12. Visa/dölj **endast tillåta SSO-inloggning** till **på**.

    ![Endast tillåta SSO-inloggning växlingsknappen](./media/absorblms-tutorial/5.png)

13. Välj **spara.**

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du har lagt till appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och komma åt den inbäddade dokumentation genom den **Configuration** avsnittet längst ned. Mer information finns i [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen.

![Skapa en Azure AD-testanvändare][100]

Om du vill skapa en testanvändare i Azure AD, gör du följande:

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/absorblms-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, Välj **användare och grupper** > **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/absorblms-tutorial/create_aaduser_02.png) 

3. Längst ned i dialogrutan Välj **Lägg till**.
 
    ![Knappen Lägg till](./media/absorblms-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan Gör följande:
 
    ![Dialogrutan användare](./media/absorblms-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver du e-postadressen för Britta Simon.

    c. Välj den **visa lösenord** kryssrutan och anteckna värdet i den **lösenord** box.

    d. Välj **Skapa**.

### <a name="create-an-absorb-lms-test-user"></a>Skapa ett LMS-absorbera testanvändare

För Azure AD-användare att logga in på absorbera LMS, måste de konfigureras i absorbera LMS.  

För absorbera LMS-installationen är en manuell aktivitet.

Om du vill konfigurera ett användarkonto, gör du följande:

1. Logga in på platsen absorbera LMS företagets som administratör.

2. I den vänstra rutan väljer **användare**.

    ![Länken absorbera LMS-användare](./media/absorblms-tutorial/absorblms_users.png)

3. I den **användare** väljer **användare**.

    ![Länken användare](./media/absorblms-tutorial/absorblms_userssub.png)

4. I den **Lägg till ny** listrutan, väljer **användaren**.

    ![Lägg till ny nedrullningsbar listruta](./media/absorblms-tutorial/absorblms_createuser.png)

5. På den **Lägg till användare** gör följande:

    ![Sidan Lägg till användare](./media/absorblms-tutorial/user.png)

    a. I den **Förnamn** skriver förnamn, till exempel **Britta**.

    b. I den **efternamn** skriver efternamn, till exempel **Simon**.
    
    c. I den **användarnamn** skriver ett fullständigt namn, till exempel **Britta Simon**.

    d. I den **lösenord** skriver Britta Simon lösenord.

    e. I den **Bekräfta lösenord** rutan, ange lösenordet på nytt.
    
    f. Ange den **är aktiv** växla till **Active**.  

6. Välj **spara.**
 
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du användaren Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till absorbera LMS.

![Tilldela rollen][200]

Om du vill tilldela användaren Britta Simon absorbera LMS, gör du följande:

1. Öppna vyn program i Azure-portalen, gå till vyn directory och därefter **företagsprogram** > **alla program**.

    ![Länken ”alla program”][201] 

2. I den **program** väljer **absorbera LMS**.

    ![Absorbera LMS-länk i listan med program](./media/absorblms-tutorial/tutorial_absorblms_app.png) 

3. I den vänstra rutan väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Välj **Lägg till** och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**.

6. I den **användare och grupper** dialogrutan den **Välj** knappen.

7. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

I åtkomstpanelen, att välja den **absorbera LMS** panel loggar du in automatiskt till ditt absorbera LMS-program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/absorblms-tutorial/tutorial_general_01.png
[2]: ./media/absorblms-tutorial/tutorial_general_02.png
[3]: ./media/absorblms-tutorial/tutorial_general_03.png
[4]: ./media/absorblms-tutorial/tutorial_general_04.png

[100]: ./media/absorblms-tutorial/tutorial_general_100.png

[200]: ./media/absorblms-tutorial/tutorial_general_200.png
[201]: ./media/absorblms-tutorial/tutorial_general_201.png
[202]: ./media/absorblms-tutorial/tutorial_general_202.png
[203]: ./media/absorblms-tutorial/tutorial_general_203.png
