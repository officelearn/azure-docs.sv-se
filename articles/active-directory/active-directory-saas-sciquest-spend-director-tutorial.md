---
title: "Självstudier: Azure Active Directory-integrering med SciQuest tillbringar Director | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SciQuest tillbringar Director."
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
ms.openlocfilehash: 2fe4d6a5fdaeebac142e28a72eced9556ef86795
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Självstudier: Azure Active Directory-integrering med SciQuest tillbringar Director
Syftet med den här kursen är att visa dig hur du integrerar SciQuest tillbringar Director med Azure Active Directory (AD Azure).  
Integrera SciQuest tillbringar Director med Azure AD ger dig följande fördelar: 

* Du kan styra i Azure AD som har åtkomst till SciQuest tillbringar Director 
* Du kan aktivera användarna att automatiskt hämta loggat in på SciQuest tillbringar Director (Single Sign-On) med sina Azure AD-konton
* Du kan hantera dina konton i en central plats – den klassiska Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav
För att konfigurera Azure AD-integrering med SciQuest tillbringar Director, behöver du följande:

* En Azure AD-prenumeration
* En SciQuest tillbringar Director enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.
> 
> 

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

* Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
* Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Scenario-beskrivning
Syftet med den här kursen är att du ska testa Azure AD enkel inloggning i en testmiljö.  
Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SciQuest tillbringar Director från galleriet 
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Att lägga till SciQuest tillbringar Director från galleriet
Du måste lägga till SciQuest tillbringar Director från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SciQuest tillbringar Director i Azure AD.

**Utför följande steg för att lägga till SciQuest tillbringar Director från galleriet:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**. 
   
    ![Active Directory][1]

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program][2]

4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Program][3]

5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Program][4]

6. I sökrutan skriver **sciQuest tillbringar director**.
   
    ![Program][5]

7. I resultatfönstret, Välj **SciQuest tillbringar Director**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![Program][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Syftet med det här avsnittet är att visa dig hur du konfigurerar och testa Azure AD enkel inloggning med SciQuest tillbringar Director baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD motsvarighet användaren i SciQuest tillbringar Director till en användare i Azure AD är okänt. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i SciQuest tillbringar Director upprättas.  
Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i SciQuest tillbringar Director.

Om du vill konfigurera och testa Azure AD enkel inloggning med SciQuest tillbringar Director, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enda enkel inloggning](#configuring-azure-ad-single-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SciQuest tillbringar Director](#creating-a-halogen-software-test-user)**  – du har en motsvarighet för Britta Simon i SciQuest tillbringar Director som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Konfigurera Azure AD-en enkel inloggning
Syftet med det här avsnittet är att aktivera Azure AD enkel inloggning i den klassiska Azure-portalen och konfigurera enkel inloggning i ditt SciQuest tillbringar Director-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SciQuest tillbringar Director:**

1. I den klassiska Azure-portalen på den **SciQuest tillbringar Director** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning**  dialogrutan.
   
    ![Konfigurera enkel inloggning][8]

2. På den **hur vill du att användarna kan logga in på SciQuest tillbringar Director** väljer **Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Azure AD-Single Sign-On][9]

3. På den **konfigurera Appinställningar** dialogrutan utför följande steg: 
   
    ![Konfigurera Appinställningar för][10]
   
     a. I den **logga URL** textruta, Skriv URL: en används av dina användare logga in i tillämpningsprogrammet SciQuest tillbringar chef med hjälp av följande mönster: *https://.* SciQuest.com/.**
   
     b. I den **Reply URL** textruta skriva in samma värde som du har skrivit i den **logga URL** textruta. 
   
     c. Klicka på **Nästa**.

4. På den **Konfigurera enkel inloggning på SciQuest tillbringar Director** klickar du på **hämtar metadata**, och sedan spara metadatafilen lokalt på datorn.
   
    ![Vad är Azure AD Connect?][11]

5. Kontakta SciQuest stöd för att aktivera den här autentiseringsmetoden använder ovan hämtade metadata.

6. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan. 
   
    ![Vad är Azure AD Connect?][15]

7. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.  

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i den klassiska Azure-portalen kallas Britta Simon.

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.
   
    ![Vad är Azure AD Connect?][100] 

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att visa en lista över användare, på menyn upp **användare**.
   
    ![Vad är Azure AD Connect?][101] 

4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**. 
   
    ![Vad är Azure AD Connect?][102] 

5. På den **berätta om den här användaren** dialogrutan utför följande steg:
   
    ![Vad är Azure AD Connect?][103] 
   
    a. Som **typ av användare**väljer **ny användare i din organisation**.
   
    b. I användarnamnet **textruta**, typen **BrittaSimon**.
   
    c. Klicka på **Nästa**.

6. På den **användarprofil** dialogrutan utför följande steg: 
   
    ![Vad är Azure AD Connect?][104] 
   
    a. I den **Förnamn** textruta typen **Britta**.  
   
    b. I den **efternamn** txtbox typ, **Simon**.
   
    c. I den **visningsnamn** textruta typen **Britta Simon**.
   
    d. I den **rollen** väljer **användaren**.
   
    e. Klicka på **Nästa**.

7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.
   
    ![Vad är Azure AD Connect?][105]  

8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:
   
    ![Vad är Azure AD Connect?][106]   
   
    a. Anteckna värdet för den **nytt lösenord**.
   
    b. Klicka på **Complete** (Slutför).   

### <a name="creating-a-sciquest-spend-director-test-user"></a>Skapa en testanvändare SciQuest tillbringar Director
Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i SciQuest tillbringar Director.

Du måste Kontakta supportteamet SciQuest tillbringar Director och ange dem med information om kontot test för att hämta den skapade.

Du kan också användas just-in-time etablering, en enkel inloggning funktion som stöds av SciQuest tillbringar Director.  
Om just-in-time-etablering aktiveras kan skapas användare automatiskt av SciQuest tillbringar Director under ett försök att enkel inloggning om de inte redan finns. Den här funktionen eliminerar behovet av att manuellt skapa användare motsvarighet för enkel inloggning.

För att få just-in-time-etablering aktiverat, måste du kontakta din supportteamet SciQuest tillbringar Director.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
Syftet med det här avsnittet är att aktivera Britta Simon att använda Azure enkel inloggning med sin åtkomst beviljas till SciQuest tillbringar Director.

![Vad är Azure AD Connect?][200]

**Om du vill tilldela SciQuest tillbringar Director Britta Simon utför du följande steg:**

1. På den klassiska Azure-portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.
   
    ![Vad är Azure AD Connect?][201]

2. Välj i listan med program **SciQuest tillbringar Director**.
   
    ![Vad är Azure AD Connect?][202]

3. Klicka på menyn högst upp **användare**.
   
    ![Vad är Azure AD Connect?][203]

4. Välj i listan användare **Britta Simon**.
   
    ![Vad är Azure AD Connect?][204]

5. Klicka på i verktygsfältet längst ned i **tilldela**.
   
    ![Vad är Azure AD Connect?][205]

### <a name="testing-single-sign-on"></a>Testa enkel inloggning
Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.  
När du klickar på panelen SciQuest tillbringar Director på åtkomstpanelen du bör få automatiskt loggat in på ditt SciQuest tillbringar Director-program.

## <a name="additional-resources"></a>Ytterligare resurser
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_20.png

