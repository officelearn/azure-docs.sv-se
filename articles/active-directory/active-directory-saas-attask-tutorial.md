---
title: "Självstudier: Azure Active Directory-integrering med @Task| Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och @Task."
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 2443c7e2e57bd9c36af292658ff85a973d5ec224
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>Självstudier: Azure Active Directory-integrering med@Task
Syftet med den här kursen är att visa dig hur du integrerar @Task med Azure Active Directory (AD Azure).  
Integrera @Task med Azure AD ger följande fördelar: 

* Du kan styra i Azure AD som har åtkomst till@Task
* Du kan ge användarna automatiskt får loggat in på @Task (Single Sign-On) med sina Azure AD-konton
* Du kan hantera dina konton i en central plats – den klassiska Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav
Konfigurera Azure AD-integrering med @Task, behöver du följande:

* En Azure AD-prenumeration
* En @Task enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.
> 
> 

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

* Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
* Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Scenario-beskrivning
Syftet med den här kursen är att du ska testa Azure AD enkel inloggning i en testmiljö.  
Det scenario som beskrivs i den här kursen består av tre huvudsakliga byggblock:

1. Lägga till @Task från galleriet 
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-task-from-the-gallery"></a>Lägga till @Task från galleriet
Så här konfigurerar du integrering av @Task till Azure AD, måste du lägga till @Task från galleriet i listan över hanterade SaaS-appar.

**Att lägga till @Task från galleriet utför du följande steg:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**. 
   
    ![Active Directory][1] 
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program][2] 
4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Program][3] 
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Program][4] 
6. I sökrutan skriver  **@Task** .
   
    ![Program][5] 
7. I resultatfönstret, Välj  **@Task** , och klicka sedan på **Slutför** lägga till programmet.
   
    ![Program][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Syftet med det här avsnittet är att visa dig hur du konfigurerar och testa Azure AD enkel inloggning med @Task baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD måste veta vilka motsvarande användaren i @Task till en användare i Azure AD är. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i @Task måste upprättas.   
Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i @Task.

Konfigurera och testa Azure AD enkel inloggning med @Task, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en @Tasktest användare](#creating-a-halogen-software-test-user)**  – du har en motsvarighet för Britta Simon i @Taskthat är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD-Single Sign-On
Syftet med det här avsnittet är att aktivera Azure AD enkel inloggning i den klassiska Azure-portalen och konfigurera enkel inloggning i din @Task program.

**Konfigurera Azure AD enkel inloggning med @Task, utför följande steg:**

1. I den klassiska Azure-portalen på den  **@Task**  integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning][6] 
2. På den **hur vill du användarna att logga in på @Task**  väljer **Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Azure AD-Single Sign-On][7] 
3. På den **konfigurera Appinställningar** dialogrutan utför följande steg:
   
    ![Konfigurera Appinställningar för][8] 
   
     a. I den **logga URL** textruta, Skriv URL: en som används av dina användare logga in till din @Task program (t.ex.:*https://<Tenant name>.attask ondemand.com*).
   
     b. Klicka på **Nästa**.
4. På den **Konfigurera enkel inloggning vid @Task**  klickar du på **hämtar metadata**, spara metadatafilen lokalt på datorn och klicka sedan på **nästa**.
   
    ![Vad är Azure AD Connect?][9] 
5. Logga in på ditt @Task företagets webbplats som administratör.
6. Gå till **enkel inloggning på konfigurationen**.
7. På den **enkel inloggning** dialogrutan, utför följande steg
   
    ![Konfigurera enkel inloggning][23]
   
    a. Som **typen**väljer **SAML 2.0**.
   
    b. Välj **Service Provider-ID**.
   
    c. På den klassiska Azure-portalen, kopiera den **Remote inloggnings-URL**, och klistrar in det i den **Portal Inloggningswebbadressen** textruta.
   
    d. På den klassiska Azure-portalen, kopiera den **tjänst-URL för enkel Sign-Out**, och klistrar in det i den **Sign-Out URL** textruta.
   
    e. På den klassiska Azure-portalen, kopiera den **ändra lösenord URL**, och klistrar in det i den **ändra lösenord URL** textruta.
   
    f. Klicka på **Spara**.
8. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **nästa**. 
   
    ![Vad är Azure AD Connect?][10]
9. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.  
   
    ![Vad är Azure AD Connect?][11]

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i den klassiska Azure-portalen kallas Britta Simon.  

![Skapa Azure AD-användare][20]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att visa en lista över användare, på menyn upp **användare**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**. 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. På den **berätta om den här användaren** dialogrutan utför följande steg: 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. Välj ny användare i din organisation som typ av användare.
   
    b. I användarnamnet **textruta**, typen **BrittaSimon**.
   
    c. Klicka på **Nästa**.
6. På den **användarprofil** dialogrutan utför följande steg: 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. I den **Förnamn** textruta typen **Britta**.  
   
    b. I den **efternamn** textruta typ, **Simon**.
   
    c. I den **visningsnamn** textruta typen **Britta Simon**.
   
    d. I den **rollen** väljer **användaren**.

    e. Klicka på **Nästa**.

7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. Anteckna värdet för den **nytt lösenord**.
   
    b. Klicka på **Complete** (Slutför).   

### <a name="creating-an-task-test-user"></a>Skapa en @Task testanvändare
Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i @Task.

**Så här skapar du en användare som kallas Britta Simon i @Task, utför följande steg:**

1. Logga in på ditt @Task företagets webbplats som administratör.
2. Klicka på menyn högst upp **personer**.
3. Klicka på **ny Person**. 
4. I dialogrutan Ny Person att utföra följande steg:
   
    ![Skapa en @Task testanvändare][21] 
   
    a. I den **Förnamn** textruta skriver ”Britta”.
   
    b. I den **efternamn** textruta skriver ”Simon”.
   
    c. I den **e-postadress** textruta Skriv Britta Simon e-postadress i Azure Active Directory.
   
    d. Klicka på **lägga till personen**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
Syftet med det här avsnittet är att aktivera Britta Simon att använda Azure enkel inloggning med sin åtkomst beviljas till @Task.

![Tilldela användare][200] 

**Tilldela Britta Simon till @Task, utför följande steg:**

1. På den klassiska Azure-portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.
   
    ![Tilldela användare][201] 
2. Välj i listan med program  **@Task** .
   
    ![Tilldela användare][202] 
3. Klicka på menyn högst upp **användare**.
   
    ![Tilldela användare][203] 
4. Välj i listan användare **Britta Simon**.
5. Klicka på i verktygsfältet längst ned i **tilldela**.
   
    ![Tilldela användare][205]

### <a name="testing-single-sign-on"></a>Testa enkel inloggning
Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.  
När du klickar på den @Task panelen på panelen åtkomst som du bör få automatiskt loggat in på ditt @Task program.

## <a name="additional-resources"></a>Ytterligare resurser
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






