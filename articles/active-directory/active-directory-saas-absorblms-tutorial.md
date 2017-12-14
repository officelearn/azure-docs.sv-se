---
title: "Självstudier: Azure Active Directory-integrering med upptar LMS | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och upptar LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: df39b957e70be38331b3711a36ce847348049591
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Självstudier: Azure Active Directory-integrering med upptar LMS

I kursen får lära du att integrera upptar LMS med Azure Active Directory (AD Azure).

Integrera upptar LMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till upptar LMS.
- Du kan aktivera användarna att logga in automatiskt upptar LMS (via enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats, Azure-portalen.

Om du vill veta mer om programvara som en tjänst (SaaS) appintegrering med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med upptar LMS behöver du följande:

- En Azure AD-prenumeration
- En upptar LMS enkel inloggning aktiverad prenumeration

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för den här kursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

* Att lägga till upptar LMS från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-absorb-lms-from-the-gallery"></a>Lägg till upptar LMS från galleriet
För att konfigurera integrering av upptar LMS i Azure AD, lägga till upptar LMS från galleriet i listan över hanterade SaaS-appar.

Lägg till upptar LMS från galleriet genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Fönstret Enterprise program][2]
    
3. Om du vill lägga till ett program, Välj den **nytt program** knappen.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **upptar LMS**väljer **upptar LMS** i leda panelen och välj sedan den **Lägg till** knappen.

    ![Upptar LMS i resultatlistan](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med upptar LMS baserat på en testanvändare kallas Britta Simon.

Azure AD måste du känna till användaren skulle kunna ta emot LMS motsvarighet i Azure AD för enkel inloggning ska fungera. Med andra ord måste du upprätta en länk förhållandet mellan en användare i Azure AD och motsvarande användaren i upptar LMS.

Du skapar den här länken relation genom att tilldela den *användarnamn* värdet i Azure AD som den *användarnamn* värdet i upptar LMS.

Slutför byggblock i följande fem avsnitt för att konfigurera och testa Azure AD enkel inloggning med upptar LMS.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program skulle kunna ta emot LMS.

För att konfigurera Azure AD enkel inloggning med upptar LMS, gör du följande:

1. I Azure-portalen på den **upptar LMS** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. I den **enkel inloggning** i dialogrutan den **läge** väljer **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. I den **upptar LMS domän och URL: er** avsnittet, gör du följande:

    ![Upptar LMS domän URL: er och enkel inloggning information](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. I den **identifierare** skriver en URL som använder följande syntax: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. I den **Reply URL** skriver en URL som använder följande syntax: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Dessa URL: er är inte de verkliga värden. Uppdatera dem med de faktiska identifierare och svars-URL: er. För att få dessa värden kan kontakta den [upptar LMS klienten supportteamet](https://www.absorblms.com/support). 

4. I den **SAML-signeringscertifikat** avsnitt i den **hämta** väljer **XML-Metadata för**, och spara sedan metadatafilen till datorn.

    ![Länk för hämtning av signering certifikat](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Välj **Spara**.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. I den **upptar LMS Configuration** väljer **konfigurera upptar LMS** att öppna **konfigurera inloggning** fönster och kopiera sedan de **Sign-Out URL** i den **Snabbreferens avsnitt.**

    ![Fönstret upptar LMS konfiguration](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. I ett nytt webbläsarfönster loggar du in på ditt upptar LMS företagets webbplats som administratör.

8. Välj den **konto** längst upp till höger. 

    ![Knappen konto](./media/active-directory-saas-absorblms-tutorial/1.png)

9. Välj i rutan konto **portalinställningar**.

    ![Länken inställningar](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Välj fliken **Användare**.

    ![Fliken användare](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Gör följande på konfigurationssidan för enkel inloggning:

    ![Konfigurationssidan för enkel inloggning](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. I den **läge** väljer **identitet providern initierade**.

    b. Öppna det certifikat du hämtade från Azure-portalen i anteckningar. Ta bort den **---BEGIN CERTIFICATE---** och **---END CERTIFICATE---** taggar. I den **nyckeln** klistra in det återstående innehållet.
    
    c. I den **Id-egenskapen** väljer du det attribut som du har konfigurerat som användar-ID i Azure AD. Till exempel om *userPrincipalName* väljs i Azure AD, Välj **användarnamn**.

    d. I den **inloggnings-URL** klistra in den **användaren åtkomst-URL** från programmets **egenskaper** sidan i Azure-portalen.

    e. I den **logga ut URL**, klistra in den **Sign-Out URL** värde som du kopierade från den **konfigurera inloggning** fönstret i Azure-portalen.

12. Växla **Tillåt endast SSO-inloggning** till **på**.

    ![Endast tillåta SSO-inloggning växla](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Välj **spara.**

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du har lagt till appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Mer information finns i [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen.

![Skapa en testanvändare i Azure AD][100]

Om du vill skapa en testanvändare i Azure AD, gör du följande:

1. I Azure-portalen i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, Välj **användare och grupper** > **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. Överst i dialogrutan Välj **Lägg till**.
 
    ![Knappen Lägg till](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan Gör följande:
 
    ![Dialogrutan användare](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** textruta skriver Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och anteckna värdet i den **lösenord** rutan.

    d. Välj **Skapa**.

### <a name="create-an-absorb-lms-test-user"></a>Skapa en testanvändare upptar LMS

För Azure AD-användare att logga in på upptar LMS måste de ställas in i upptar LMS.  

För upptar LMS är installationsprogrammet en manuell aktivitet.

Om du vill konfigurera ett användarkonto, gör du följande:

1. Logga in på webbplatsen upptar LMS företag som administratör.

2. I den vänstra rutan, Välj **användare**.

    ![Länken upptar LMS användare](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. I den **användare** väljer **användare**.

    ![Länken användare](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. I den **Lägg till ny** listrutan, Välj **användaren**.

    ![Lägg till ny listrutan](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. På den **Lägg till användare** gör följande:

    ![Sidan Lägg till användare](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. I den **Förnamn** Ange förnamn, t.ex **Britta**.

    b. I den **efternamn** skriver efternamn, t.ex **Simon**.
    
    c. I den **användarnamn** Skriv ett fullständigt namn som **Britta Simon**.

    d. I den **lösenord** skriver Britta Simon lösenord.

    e. I den **Bekräfta lösenord** rutan, ange lösenordet igen.
    
    f. Ange den **är aktiv** växla till **Active**.  

6. Välj **spara.**
 
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du låta användare Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till upptar LMS.

![Tilldela rollen][200]

Om du vill tilldela användaren Britta Simon upptar LMS, gör du följande:

1. Öppna program i Azure-portalen, gå till vyn directory och välj sedan **företagsprogram** > **alla program**.

    ![Länken ”alla program”][201] 

2. I den **program** väljer **upptar LMS**.

    ![Länken upptar LMS i listan med program](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. I den vänstra rutan, Välj **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Välj **Lägg till** och sedan, i den **Lägg uppdrag** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**.

6. I den **användare och grupper** dialogrutan markerar du den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan markerar du den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

I panelen åtkomst att välja den **upptar LMS** panelen automatiskt loggar du in i tillämpningsprogrammet upptar LMS. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

