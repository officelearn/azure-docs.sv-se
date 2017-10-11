---
title: "Självstudier: Azure Active Directory-integrering med Splunk Enterprise och Splunk molntjänster | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Splunk Enterprise och Splunk moln."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/09/2017
ms.author: jeedes
ms.openlocfilehash: b78e9b7161207a74880e912241d5e965b353d1c5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Självstudier: Azure Active Directory-integrering med Splunk Enterprise och Splunk moln

I kursen får lära du att integrera Splunk Enterprise och Splunk moln med Azure Active Directory (AD Azure).

Integrera Splunk Enterprise och Splunk moln med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Splunk Enterprise och Splunk moln
- Du kan aktivera användarna att automatiskt hämta loggat in på Splunk Enterprise och Splunk moln enkel inloggning (SSO) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats – den klassiska Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Splunk Enterprise och Splunk molnet, behöver du följande:

- En Azure AD-prenumeration
- En Splunk Enterprise eller Splunk moln SSO aktiverad prenumeration


>[!NOTE]
>Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.
>

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du få en [utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö.

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Splunk Enterprise och Splunk moln från galleriet
2. Konfigurera och testa Azure AD-SSO


## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Lägg till Splunk Enterprise och Splunk moln från galleriet
Du måste lägga till Splunk Enterprise och Splunk moln från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Splunk Enterprise och Splunk moln i Azure AD.

**Utför följande steg för att lägga till Splunk Enterprise och Splunk moln från galleriet:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.

    ![Active Directory][1]

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.

    ![Program][2]

4. Klicka på **Lägg till** längst ned på sidan.

    ![Program][3]

5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.

    ![Program][4]

6. I sökrutan skriver **Splunk Enterprise eller Splunk moln**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_01.png)

7. I resultatfönstret, Välj **Splunk Enterprise och Splunk moln**, och klicka sedan på **Slutför** lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_02.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Splunk Enterprise och Splunk molnet baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Splunk Enterprise och Splunk molnet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Splunk Enterprise och Splunk molnet upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Splunk Enterprise och Splunk moln.

Om du vill konfigurera och testa Azure AD enkel inloggning med Splunk Enterprise och Splunk moln, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Splunk Enterprise och Splunk moln](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Splunk företag och Splunk moln som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i den klassiska portalen och konfigurera enkel inloggning i tillämpningsprogrammet Splunk Enterprise och Splunk moln.


**Utför följande steg för att konfigurera Azure AD enkel inloggning med Splunk Enterprise och Splunk molnet:**

1. I den klassiska portalen på den **Splunk Enterprise och Splunk moln** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
     
    ![Konfigurera enkel inloggning][6] 

2. På den **hur vill du att användarna kan logga in på Splunk Enterprise och Splunk moln** väljer **Azure AD enkel inloggning**, och klicka sedan på **nästa**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_03.png) 

3. På den **konfigurera Appinställningar** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_04.png) 
  1. I den **logga URL** textruta, Skriv URL: en som används av dina användare logga in i tillämpningsprogrammet Splunk Enterprise och Splunk moln med hjälp av följande mönster:`https://<splunkserverUrl>/en-US/app/launcher/home`
  2. I den **identifierare** textruta ange Webbadressen till Splunk-Server.
  3. I den **Reply URL** textruta anger du URL med följande mönster:`https://<splunkserver>/saml/acs`
  4. Klicka på **Nästa**.
 
4. På den **Konfigurera enkel inloggning på Splunk Enterprise och Splunk moln** utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_05.png)
  1. Klicka på **hämtar metadata**, och sedan spara filen på datorn.
  2. Klicka på **Nästa**.

5. Kontakta Splunk Enterprise och Splunk moln supportteamet för att få SSO konfigurerats för ditt program, och ge dem med följande:

    * Den hämtade **federaton metadata**
6. I den klassiska portalen markerar du konfigurationen för enkel inloggning bekräftelsen och klicka sedan på **nästa**.
    
    ![Azure AD-Single Sign-On][10]

7. På den **enkel inloggning bekräftelse** klickar du på **Slutför**.  
 
    ![Azure AD-Single Sign-On][11]

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
I det här avsnittet kan du skapa en testanvändare i den klassiska portalen kallas Britta Simon.

![Skapa Azure AD-användare][20]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **klassiska Azure-portalen**, klicka på det vänstra navigeringsfönstret **Active Directory**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_09.png) 

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att visa en lista över användare, på menyn upp **användare**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_03.png) 

4. Öppna den **Lägg till användare** i verktygsfältet längst ned i dialogrutan klickar du på **Lägg till användare**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_04.png) 

5. På den **berätta om den här användaren** dialogrutan utför följande steg:

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_05.png) 
  1. Välj ny användare i din organisation som typ av användare.
  2. I användarnamnet **textruta**, typen **BrittaSimon**.
  3. Klicka på **Nästa**.

6.  På den **användarprofil** dialogrutan utför följande steg:
  
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_06.png) 
  1. I den **Förnamn** textruta typen **Britta**.  
  2. I den **efternamn** textruta typ, **Simon**.
  3. I den **visningsnamn** textruta typen **Britta Simon**.
  4. I den **rollen** väljer **användaren**.
  5. Klicka på **Nästa**.

7. På den **skaffa tillfälligt lösenord** dialogrutan sidan, klickar du på **skapa**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_07.png) 

8. På den **skaffa tillfälligt lösenord** dialogrutan utför följande steg:

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_08.png) 
  1. Anteckna värdet för den **nytt lösenord**.
  2. Klicka på **Complete** (Slutför).   

### <a name="create-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Skapa en Splunk Enterprise och Splunk moln testanvändare

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Splunk företag och Splunk moln. Se tillsammans med Splunk Enterprise och Splunk moln supportteamet för att lägga till användare i Splunk Enterprise och Splunk Cloud platform.


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure SSOy bevilja sin åtkomst till Splunk Enterprise och Splunk moln.

![Tilldela användare][200] 

**Utför följande steg om du vill tilldela Britta Simon Splunk företag och Splunk molnet:**

1. På den klassiska portalen för att öppna vyn program i katalogen vyn klickar du på **program** på huvudmenyn.

    ![Tilldela användare][201] 

2. Välj i listan med program **Splunk Enterprise och Splunk moln**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_50.png) 

3. Klicka på menyn högst upp **användare**.

    ![Tilldela användare][203]

4. Välj i listan användare **Britta Simon**.

5. Klicka på i verktygsfältet längst ned i **tilldela**.

    ![Tilldela användare][205]

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa din Azure AD-SSOonfiguration med åtkomstpanelen.

När du klickar på företaget Splunk Splunk moln panelen i åtkomstpanelen, du ska hämta automatiskt loggat in i tillämpningsprogrammet Splunk Enterprise och Splunk moln.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_205.png
