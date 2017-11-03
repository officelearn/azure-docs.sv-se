---
title: "Självstudier: Azure Active Directory-integrering med Wizergos produktivitetsprogram | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wizergos produktivitetsprogram."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
ms.openlocfilehash: 73b3bc05aeb337c12acb7e47c0dbebe6d0196530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Självstudier: Azure Active Directory-integrering med Wizergos produktivitetsprogram
Syftet med den här kursen är att visa dig hur du integrerar Wizergos produktivitetsprogram med Azure Active Directory (AD Azure).

Integrera Wizergos produktivitetsprogram med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Wizergos produktivitetsprogram
* Du kan aktivera användarna att automatiskt hämta loggat in på Wizergos produktivitetsprogram enkel inloggning (SSO) med sina Azure AD-konton
* Du kan hantera dina konton i en central plats – den klassiska Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav
För att konfigurera Azure AD-integrering med Wizergos produktivitetsprogram, behöver du följande:

* En Azure AD-prenumeration
* En prenumeration Wizergos produktivitet programvara SSO aktiverad

>[!NOTE]
>Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö. 
> 

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

* Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
* Om du inte har en utvärderingsversion Azure AD-miljö kan du få en [utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
Syftet med den här kursen är att du ska testa Azure AD SSO i en testmiljö.

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Wizergos produktivitetsprogram från galleriet
2. Konfigurera och testa Azure AD-SSO

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Att lägga till Wizergos produktivitetsprogram från galleriet
Du måste lägga till Wizergos produktivitetsprogram från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Wizergos produktivitetsprogram i Azure AD.

**Utför följande steg för att lägga till Wizergos produktivitetsprogram från galleriet:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**. 
   
    ![Active Directory][1]
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program][2]
4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Program][3]
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Program][4]
6. I sökrutan skriver **Wizergos produktivitetsprogram**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)
7. Välj i resultatpanelen **Wizergos produktivitetsprogram**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![Markera appen i galleriet](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD-SSO
Syftet med det här avsnittet är att visa dig hur du konfigurerar och testa Azure AD SSO med Wizergos produktivitetsprogram baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD motsvarighet användaren i Wizergos produktivitetsprogram till en användare i Azure AD är okänt. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Wizergos produktivitetsprogram upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Wizergos produktivitetsprogram.

Om du vill konfigurera och testa Azure AD enkel inloggning med BynWizergos produktivitet Softwareder, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Wizergos produktivitetsprogram](#creating-a-wizergos-productivity-software-test-user)**  – har en motsvarighet för Britta Simon Wizergos produktivitetsprogram som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-sso"></a>Konfigurera Azure AD för enkel inloggning
I det här avsnittet Aktivera Azure AD enkel inloggning i den klassiska portalen och konfigurera enkel inloggning i tillämpningsprogrammet Wizergos produktivitetsprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Wizergos produktivitetsprogram:**

1. I den klassiska portalen på den **Wizergos produktivitetsprogram** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning**  dialogrutan.
   
    ![Konfigurera enkel inloggning][6] 
2. På den **hur vill du att användarna kan logga in på Wizergos produktivitetsprogram** väljer **Azure AD enkel inloggning**, och klicka sedan på **nästa**:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)
3. På den **konfigurera Appinställningar** dialogrutan sidan, klickar du på **nästa**:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)
4. På den **Konfigurera enkel inloggning på Wizergos produktivitetsprogram** klickar du på **hämta certifikat**, och sedan spara filen på datorn:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)
5. I en annan webbläsarfönster inloggning till Wizergos produktivitetsprogram-klient som administratör.
6. Välj menyn hamburger **Admin**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)
7. Markera i Administrationssida på vänstra menyn **AUTENTISERING** och klicka på **Azure AD**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)
8. Utför följande steg på **AUTENTISERING** avsnitt.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
  1. Klicka på **överför** för att ladda upp det hämta certifikatet från Azure AD. 
  2. I den **utfärdar-URL** textruta ange värdet för **utfärdar-URL** från guiden Konfigurera program för Azure AD.
  3. I den **URL för enkel inloggning** textruta ange värdet för **inloggning tjänst-URL för enkel** från guiden Konfigurera program för Azure AD.
  4. I den **Sign-Out-URL för enkel** textruta ange värdet för **tjänst-URL för enkel Sign-out** från guiden Konfigurera program för Azure AD.
  5. Klicka på **spara** knappen.
9. I den klassiska portalen markerar du konfigurationen för enkel inloggning bekräftelsen och klicka sedan på **nästa**.
   
    ![Azure AD-Single Sign-On][10]
10. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.  
    
    ![Azure AD-Single Sign-On][11]

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i den klassiska portalen kallas Britta Simon.

![Skapa Azure AD-användare][20]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att visa en lista över användare, på menyn upp **användare**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)
4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)
5. På den **berätta om den här användaren** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png) 
  1. Välj ny användare i din organisation som typ av användare.
  2. I användarnamnet **textruta**, typen **BrittaSimon**.
  3. Klicka på **Nästa**.
6. På den **användarprofil** dialogrutan utför följande steg:
   
   ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)
  1. I den **Förnamn** textruta typen **Britta**.  
  2. I den **efternamn** textruta typ, **Simon**.
  3. I den **visningsnamn** textruta typen **Britta Simon**.
  4. I den **rollen** väljer **användaren**.
  5. Klicka på **Nästa**.
7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)
8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)
  1. Anteckna värdet för den **nytt lösenord**.
  2. Klicka på **Complete** (Slutför).   

### <a name="create-a-wizergos-productivity-software-test-user"></a>Skapa en testanvändare Wizergos produktivitetsprogram
I det här avsnittet skapar du en användare som kallas Britta Simon i Wizergos produktivitetsprogram. Se tillsammans med Wizergos produktivitetsprogram supportteamet via [ support@wizergos.com ](emailTo:support@wizergos.com) att lägga till användare i Wizergos produktivitetsprogram-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare
Syftet med det här avsnittet är att aktivera Britta Simon att använda Azure SSO genom att bevilja sin åtkomst till Wizergos produktivitetsprogram.

  ![Tilldela användare][200]

**Om du vill tilldela Wizergos produktivitetsprogram Britta Simon utför du följande steg:**

1. På den klassiska portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.
   
    ![Tilldela användare][201]
2. Välj i listan med program **Wizergos produktivitetsprogram**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)
3. Klicka på menyn högst upp **användare**.
   
    ![Tilldela användare][203]
4. Välj i listan användare **Britta Simon**.
5. Klicka på i verktygsfältet längst ned i **tilldela**.
   
    ![Tilldela användare][205]

### <a name="test-single-sign-on"></a>Testa enkel inloggning
Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Wizergos produktivitetsprogram på åtkomstpanelen du bör få automatiskt loggat in på ditt Wizergos produktivitetsprogram program.

## <a name="additional-resources"></a>Ytterligare resurser
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png
