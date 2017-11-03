---
title: "Självstudier: Azure Active Directory-integrering med Halosys | Microsoft Docs"
description: "Lär dig hur du använder Halosys med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 42a0eb7c-5cb7-44a9-b00b-b0e7df4b63e8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 18c5cd8eb4ca211f8ae2b8dd994c0e8c48625a2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-halosys"></a>Självstudier: Azure Active Directory-integrering med Halosys

I kursen får lära du att integrera Halosys med Azure Active Directory (AD Azure).

Integrera Halosys med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Halosys
- Du kan aktivera användarna att automatiskt hämta loggat in på Halosys (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats – den klassiska Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Halosys, behöver du följande:

- En Azure AD-prenumeration
- En Halosys enkel inloggning på aktiverade prenumeration


> [!NOTE] 
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö.

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Halosys från galleriet
2. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-halosys-from-the-gallery"></a>Att lägga till Halosys från galleriet
Du måste lägga till Halosys från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Halosys i Azure AD.

**Utför följande steg för att lägga till Halosys från galleriet:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.

    ![Active Directory][1]
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.

    ![Program][2]

4. Klicka på **Lägg till** längst ned på sidan.

    ![Program][3]

5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.

    ![Program][4]

6. I sökrutan skriver **Halosys**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_01.png)
    
7. I resultatfönstret, Välj **Halosys**, och klicka sedan på **Slutför** lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_011.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Halosys baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Halosys motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Halosys upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Halosys.

Om du vill konfigurera och testa Azure AD enkel inloggning med Halosys, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Halosys](#creating-a-halosys-test-user)**  – du har en motsvarighet för Britta Simon i Halosys som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i den klassiska portalen och konfigurera enkel inloggning i ditt Halosys program.


**Utför följande steg för att konfigurera Azure AD enkel inloggning med Halosys:**

1. I den klassiska portalen på den **Halosys** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
     
    ![Konfigurera enkel inloggning][6] 

2. På den **hur vill du att användarna kan logga in på Halosys** väljer **Azure AD enkel inloggning**, och klicka sedan på **nästa**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_03.png) 

3. På den **konfigurera Appinställningar** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_04.png) 

    a. I den **logga URL** textruta, Skriv URL: en som används av dina användare logga in i tillämpningsprogrammet Halosys med hjälp av följande mönster: `https://<company-name>.Halosys.com/client-api/api`.

    b.In den **Identifierare** textruta ange Webbadressen i följande mönster: `https://<company-name>.Halosys.com`.   
         
4. På den **Konfigurera enkel inloggning på Halosys** klickar du på **hämtar metadata**, och sedan spara filen på datorn:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_05.png)
   
5. För att få SSO konfigurerats för ditt program, Kontakta supportteamet för Halosys och ge dem med följande:

    • Den hämtade **metadatafil**
    
    • Den **SAML SSO-URL**
    

6. I den klassiska portalen markerar du konfigurationen för enkel inloggning bekräftelsen och klicka sedan på **nästa**.
    
    ![Azure AD-Single Sign-On][10]

7. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.  
 
    ![Azure AD-Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
I det här avsnittet kan du skapa en testanvändare i den klassiska portalen kallas Britta Simon.


![Skapa Azure AD-användare][20]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_09.png) 

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att visa en lista över användare, på menyn upp **användare**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_03.png) 

4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_04.png) 

5. På den **berätta om den här användaren** dialogrutan utför följande steg: ![att skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_05.png) 

    a. Välj ny användare i din organisation som typ av användare.

    b. I användarnamnet **textruta**, typen **BrittaSimon**.

    c. Klicka på **Nästa**.

6.  På den **användarprofil** dialogrutan utför följande steg: ![att skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_06.png) 

    a. I den **Förnamn** textruta typen **Britta**.  

    b. I den **efternamn** textruta typ, **Simon**.

    c. I den **visningsnamn** textruta typen **Britta Simon**.

    d. I den **rollen** väljer **användaren**.

    e. Klicka på **Nästa**.

7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_07.png) 

8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_08.png) 

    a. Anteckna värdet för den **nytt lösenord**.

    b. Klicka på **Complete** (Slutför).   



### <a name="creating-a-halosys-test-user"></a>Skapa en testanvändare Halosys

I det här avsnittet skapar du en användare som kallas Britta Simon i Halosys. Se tillsammans med Halosys supportteamet att lägga till användare i Halosys-plattformen.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till Halosys.

![Tilldela användare][200] 

**Om du vill tilldela Halosys Britta Simon utför du följande steg:**

1. På den klassiska portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.

    ![Tilldela användare][201] 

2. Välj i listan med program **Halosys**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_50.png) 

3. Klicka på menyn högst upp **användare**.

    ![Tilldela användare][203]

4. Välj i listan användare **Britta Simon**.

5. Klicka på i verktygsfältet längst ned i **tilldela**.

    ![Tilldela användare][205]


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Halosys på åtkomstpanelen du bör få automatiskt loggat in på ditt Halosys program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_205.png
