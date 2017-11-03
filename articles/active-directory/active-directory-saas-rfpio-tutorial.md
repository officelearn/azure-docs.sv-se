---
title: "Självstudier: Azure Active Directory-integrering med RFPIO | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RFPIO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 26a8bb17dad5a01b401ce7f9b484f09822825cbf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Självstudier: Azure Active Directory-integrering med RFPIO

I kursen får lära du att integrera RFPIO med Azure Active Directory (AD Azure).

Integrera RFPIO med Azure AD ger dig följande fördelar:

- Du kan styra vem i Azure AD som har åtkomst till RFPIO.
- Du kan aktivera användarna att automatiskt hämta loggat in på RFPIO (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med RFPIO, behöver du följande:

- En Azure AD-prenumeration.
- En RFPIO enkel inloggning på aktiverat prenumeration.

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här kursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du få en [utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till RFPIO från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-rfpio-from-the-gallery"></a>Lägg till RFPIO från galleriet
Du måste lägga till RFPIO från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av RFPIO i Azure AD.

### <a name="to-add-rfpio-from-the-gallery"></a>Lägga till RFPIO från galleriet

1. I den  **[Azure-portalen](https://portal.azure.com)**, på det vänstra navigeringsfönstret väljer du den **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **RFPIO**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. Välj i resultatpanelen **RFPIO**, och välj sedan den **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med RFPIO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till relationen mellan motsvarighet användare i RFPIO och en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i RFPIO upprättas.

I RFPIO, tilldela värdet för **användarnamn** i Azure AD som värde för **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med RFPIO, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**--att användarna ska kunna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**--att testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare RFPIO](#creating-a-rfpio-test-user)**  --har en motsvarighet för Britta Simon RFPIO som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**--att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  --att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt RFPIO program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med RFPIO:**

1. I Azure-portalen på den **RFPIO** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. På den **RFPIO domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. I den **identifierare** textruta anger du URL:`https://www.rfpio.com`

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Kontrollera **visa avancerade inställningar för URL: en**.

    c. I den **Relay tillstånd** textrutan anger du ett strängvärde. Kontakta [RFPIO supportteam](https://www.rfpio.com/contact/) att hämta det här värdet. 

4. Kontrollera **visa avancerade inställningar för URL: en**. Om du vill konfigurera programmet i **SP** initierade läge: 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    I den **inloggning URL** textruta anger du URL:`https://www.app.rfpio.com`

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. I en annan webbläsarfönstret, logga in på den **RFPIO** webbplats som administratör.

8. Klicka på listrutan längst ned till vänster.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. Klicka på den **organisationsinställningar**. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. Klicka på den **funktioner & INTEGRATION**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. I den **SAML SSO Configuration** klickar du på **redigera**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. Utför följande åtgärder i det här avsnittet:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. Kopiera innehållet i den **hämtas XML-Metadata för** och klistrar in det i den **identitet configuration** fältet.

    > [!NOTE]
    >Att kopiera innehållet i nedladdade **XML-Metadata för** Använd **anteckningar ++** eller rätt **XML-redigerare**. 

    b. Klicka på **Validera**.

    c. När du klickar på **Validera**, vänd **SAML(Enabled)** till on.

    d. Klicka på **skicka**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-rfpio-test-user"></a>Skapa en testanvändare RFPIO

Om du vill aktivera Azure AD-användare kan logga in på RFPIO etableras de i RFPIO.  
När det gäller RFPIO är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen RFPIO företag som administratör.

2. Klicka på listrutan längst ned till vänster.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. Klicka på den **organisationsinställningar**. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. Klicka på **GRUPPMEDLEMMAR**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. Klicka på **Lägg till MEDLEMMAR**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. I den **lägga till nya medlemmar** avsnitt. Utför följande åtgärder:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. Ange **e-postadress** i den **ange en e-postadress per rad** fältet.

    b. Välj Plese **rollen** enligt dina krav.

    c. Klicka på **Lägg till MEDLEMMAR**.
        
    > [!NOTE]
    > Azure Active Directory kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RFPIO.

![Tilldela användare][200] 

**Om du vill tilldela RFPIO Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **RFPIO**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen RFPIO på åtkomstpanelen du bör få automatiskt loggat in på ditt RFPIO program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png

