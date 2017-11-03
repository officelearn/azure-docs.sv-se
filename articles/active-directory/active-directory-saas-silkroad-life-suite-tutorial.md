---
title: "Självstudier: Azure Active Directory-integrering med SilkRoad livslängd Suite | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SilkRoad livslängd Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Självstudier: Azure Active Directory-integrering med SilkRoad livslängd Suite
Syftet med den här kursen är att visa dig hur du integrerar SilkRoad livslängd Suite med Azure Active Directory (AD Azure). 

Integrera SilkRoad livslängd Suite med Azure AD ger dig följande fördelar: 

* Du kan styra i Azure AD som har åtkomst till SilkRoad livslängd Suite 
* Du kan aktivera användarna att automatiskt hämta loggat in på SilkRoad livslängd Suite enkel inloggning (SSO) med sina Azure AD-konton

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav
För att konfigurera Azure AD-integrering med SilkRoad livslängd Suite, behöver du följande:

* En Azure AD-prenumeration
* En prenumeration SilkRoad livslängd Suite SSO aktiverad

>[!NOTE]
>Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö. 
> 

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

* Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
* Om du inte har en utvärderingsversion Azure AD-miljö kan du få en [utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Scenario-beskrivning
Syftet med den här kursen är att du ska testa Azure AD SSO i en testmiljö.

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SilkRoad livslängd Suite från galleriet 
2. Konfigurera och testa Azure AD-SSO

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Lägg till SilkRoad livslängd Suite från galleriet
Du måste lägga till SilkRoad livslängd Suite från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SilkRoad livslängd Suite i Azure AD.

**Utför följande steg för att lägga till SilkRoad livslängd Suite från galleriet:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**. 
   
    ![Active Directory][1]

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program][2]

4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Program][3]

5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Program][4]

6. I sökrutan skriver **SilkRoad livslängd Suite**.
   
    ![Program][5]

7. I resultatfönstret, Välj **SilkRoad livslängd Suite**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![Program][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Syftet med det här avsnittet är att visa dig hur du konfigurerar och testa Azure AD SSO med SilkRoad livslängd Suite baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD motsvarighet användaren i SilkRoad livslängd Suite till en användare i Azure AD är okänt. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SilkRoad livslängd Suite upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i SilkRoad livslängd Suite.

Om du vill konfigurera och testa Azure AD enkel inloggning med SilkRoad livslängd Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SilkRoad livslängd Suite](#creating-a-silkroad-life-suite-test-user)**  – har en motsvarighet för Britta Simon SilkRoad livslängd Suite som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning
Syftet med det här avsnittet är att aktivera Azure AD SSO i den klassiska Azure-portalen och konfigurera enkel inloggning i SilkRoad livslängd Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SilkRoad livslängd Suite:**

1. Inloggning på webbplatsen SilkRoad företag som administratör. 

  >[!NOTE] 
  > För att få åtkomst till programmet SilkRoad livslängd Suite autentisering för att konfigurera federation med Microsoft Azure AD kan kontakta SilkRoad Support eller genom ditt ombud SilkRoad tjänster.
  > 

2. Gå till **tjänstleverantör**, och klicka sedan på **Federation information**. 
   
    ![Azure AD-Single Sign-On][10] 

3. Klicka på **hämta Federationsmetadata**, och spara sedan metadatafilen på datorn.
   
    ![Azure AD-Single Sign-On][11] 

4. I den klassiska Azure-portalen på den **SilkRoad livslängd Suite** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning][6] 

5. På den **hur vill du att användarna kan logga in på SilkRoad livslängd Suite** väljer **Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Azure AD-Single Sign-On][7] 

6. På den **konfigurera Appinställningar** dialogrutan utför följande steg:
   
    ![Azure AD-Single Sign-On][8]   
 1. I den **logga URL** textruta, Skriv URL: en som används av dina användare logga in på webbplatsen SilkRoad livslängd Suite (t.ex.: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).  
 2. Öppna den hämtade **Silkroad** metadatafil. 
 3. Leta upp den **AssertionConsumerService** tagg och sedan kopiera den **plats** attribut.         
   
    ![Azure AD-Single Sign-On][21] 
 4. Klistra in värdet till den **Reply URL** textruta.  
 5. Klicka på **Nästa**.

6. På den **Konfigurera enkel inloggning på SilkRoad livslängd Suite** utför följande steg:
   
    ![Azure AD-Single Sign-On][9]  
 1. Klicka på hämta certifikatet och spara filen på datorn.  
 2. Klicka på **Nästa**.

7. I din **SilkRoad** programmet, klickar du på **autentisering källor**.
   
    ![Azure AD-Single Sign-On][12] 

8. Klicka på **Lägg till autentiseringskälla**. 
   
    ![Azure AD-Single Sign-On][13] 

9. I den **Lägg till autentiseringskälla** avsnittet, utför följande steg: 
   
    ![Azure AD-Single Sign-On][14]  
 1. Under **alternativ 2 - metadatafil**, klickar du på **Bläddra** att överföra metadatafilen hämtade.  
 2. Klicka på **skapa identitetsleverantör med hjälp av fildata**.

10. I den **autentisering källor** klickar du på **redigera**. 
    
     ![Azure AD-Single Sign-On][15] 

11. På den **redigera autentiseringskälla** dialogrutan, utför följande steg: 
    
     ![Azure AD-Single Sign-On][16] 
 1. Som **aktiverad**väljer **Ja**.   
 2. I den **IdP beskrivning** textruta skriver du en beskrivning för konfigurationen (t.ex.: *Azure AD SSO*).  
 3. I den **IdP namnet** textruta, ange ett namn som är specifik för din konfiguration (t.ex.: *Azure SP*).  
 4. Klicka på **Spara**.

12. Inaktivera alla autentisering källor. 
    
     ![Azure AD-Single Sign-On][17]

13. I den klassiska Azure-portalen på den **enkel inloggning bekräftelse** klickar du på **nästa**.  
    
     ![Azure AD-Single Sign-On][18]

14. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.
    
     ![Azure AD-Single Sign-On][19]

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i den klassiska Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][20]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att visa en lista över användare, på menyn upp **användare**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**. 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. På den **berätta om den här användaren** dialogrutan utför följande steg: 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. Välj ny användare i din organisation som typ av användare.  
 2. I användarnamnet **textruta**, typen **BrittaSimon**. 
 3. Klicka på **Nästa**.

6. På den **användarprofil** dialogrutan utför följande steg: 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. I den **Förnamn** textruta typen **Britta**.    
 2. I den **efternamn** textruta typ, **Simon**. 
 3. I den **visningsnamn** textruta typen **Britta Simon**. 
 4. I den **rollen** väljer **användaren**.
 5. Klicka på **Nästa**.

7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. Anteckna värdet för den **nytt lösenord**. 
 2. Klicka på **Complete** (Slutför).   

### <a name="create-a-silkroad-life-suite-test-user"></a>Skapa en testanvändare SilkRoad livslängd Suite
Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i SilkRoad livslängd Suite. Brittas måste ha ett ID för enkel inloggning (kallas ibland ett *AuthParam*) som matchar Britta's **e-postadress** i Azure AD.

**Utför följande steg för att skapa en användare som kallas Britta Simon i SilkRoad livslängd Suite:**

- Be din SilkRoad livslängd Suite supportteamet för att skapa en användare som har som **SSO ID** attributet samma värde som den **e-postadress** av Britta Simon i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
Syftet med det här avsnittet är att aktivera Britta Simon att använda Azure SSO med sin åtkomst beviljas till SilkRoad livslängd Suite.

![Tilldela användare][200] 

**Om du vill tilldela SilkRoad livslängd Suite Britta Simon utför du följande steg:**

1. På den klassiska Azure-portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.
   
    ![Tilldela användare][201] 

2. Välj i listan med program **SilkRoad livslängd Suite**.
   
    ![Tilldela användare][202] 

3. Klicka på menyn högst upp **användare**.
   
    ![Tilldela användare][203] 

4. Välj i listan användare **Britta Simon**.

5. Klicka på i verktygsfältet längst ned i **tilldela**.
   
    ![Tilldela användare][205]

### <a name="test-single-sign-on"></a>Testa enkel inloggning
Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen SilkRoad livslängd Suite på åtkomstpanelen du bör få automatiskt loggat in på ditt SilkRoad livslängd Suite-program.

## <a name="additional-resources"></a>Ytterligare resurser
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png





