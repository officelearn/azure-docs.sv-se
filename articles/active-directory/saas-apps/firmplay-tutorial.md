---
title: 'Självstudier: Azure Active Directory-integrering med FirmPlay - medarbetare befrämjande för rekrytering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FirmPlay - medarbetare befrämjande för rekrytering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 2a95b6108bb52dc5a5b28bf51ebf431a00a9faa8
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963794"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Självstudier: Azure Active Directory-integrering med FirmPlay - medarbetare befrämjande för rekrytering

I kursen får lära du att integrera FirmPlay - medarbetare befrämjande för rekrytering med Azure Active Directory (AD Azure).

Integrera FirmPlay - medarbetare befrämjande för rekrytering med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FirmPlay - medarbetare befrämjande för rekrytering
- Du kan aktivera användarna att automatiskt hämta loggat in på FirmPlay - medarbetare befrämjande för rekrytering (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FirmPlay - medarbetare befrämjande för rekrytering, behöver du följande:

- En Azure AD-prenumeration
- En FirmPlay - medarbetare befrämjande för rekrytering enkel inloggning aktiverad prenumeration


> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till FirmPlay - medarbetare befrämjande för rekrytering från galleriet
2. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Lägger till FirmPlay - medarbetare befrämjande för rekrytering från galleriet
Du måste lägga till FirmPlay - medarbetare befrämjande för rekrytering från galleriet i listan över hanterade SaaS-appar för att konfigurera FirmPlay - medarbetare befrämjande för rekrytering till Azure AD-integrering.

**Utför följande steg för att lägga till FirmPlay - medarbetare befrämjande för rekrytering från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **FirmPlay - medarbetare befrämjande för rekrytering**.

    ![Skapa en testanvändare i Azure AD](./media/firmplay-tutorial/tutorial_firmplay_001.png)

5. Välj i resultatpanelen **FirmPlay - medarbetare befrämjande för rekrytering**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med FirmPlay - medarbetare befrämjande för rekrytering baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste veta vilka motsvarande användaren i FirmPlay - medarbetare befrämjande för rekrytering är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i FirmPlay - medarbetare befrämjande för rekrytering upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i FirmPlay - medarbetare befrämjande för rekrytering.

Om du vill konfigurera och testa Azure AD enkel inloggning med FirmPlay - medarbetare befrämjande för rekrytering, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en FirmPlay - medarbetare befrämjande för rekrytering testanvändare](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  – har en motsvarighet för Britta Simon FirmPlay: medarbetare befrämjande för rekrytering som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i din FirmPlay - medarbetare befrämjande för rekrytering program.

**Om du vill konfigurera Azure AD enkel inloggning med FirmPlay - medarbetare befrämjande för rekrytering, utför du följande steg:**

1. I Azure-hanteringsportalen på den **FirmPlay - medarbetare befrämjande för rekrytering** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_01.png)

3. På den **FirmPlay - medarbetare befrämjande rekrytering domän och URL: er** avsnitt i den **logga URL** textruta Skriv en URL med följande mönster: `https://<your-subdomain>.firmplay.com/`

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera det här värdet med faktiska logga på URL: en. Kontakta [FirmPlay - medarbetare befrämjande för rekrytering supportteamet](mailto:engineering@firmplay.com) att hämta det här värdet. 

4. På den **SAML-signeringscertifikat** klickar du på **Skapa nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_03.png)     

5. På den **skapa nya certifikat** dialogrutan, klicka på kalenderikonen och välj en **förfallodatum**. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_general_300.png)

6. På den **SAML-signeringscertifikat** väljer **aktivera nya certifikatet** och på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_04.png)

7. På popup-fönstret **förnyelsecertifikat** -fönstret klickar du på **OK**.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_general_400.png)

8. På den **SAML-signeringscertifikat** klickar du på **certifikat (base64)** och spara certifikatfilen på datorn. 

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_05.png) 

9. På den **FirmPlay - medarbetare befrämjande för rekrytering konfigurationen** klickar du på **konfigurera FirmPlay - medarbetare befrämjande för rekrytering** att öppna **konfigurera inloggning** dialogrutan.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_07.png)

10. För att få SSO konfigurerats för ditt program, kontakta [FirmPlay - medarbetare befrämjande för rekrytering supportteamet](mailto:engineering@firmplay.com) och ge dem med följande: 

    • Den hämtade **certifikatfilen**

    • Den **URL för SAML-tjänst för enkel inloggning**

    • Den **SAML enhets-ID**

    • Den **URL för utloggning**
  

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/firmplay-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/firmplay-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/firmplay-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/firmplay-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Skapa en FirmPlay - medarbetare befrämjande för rekrytering testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i FirmPlay - medarbetare befrämjande för rekrytering. Se tillsammans med [FirmPlay - medarbetare befrämjande för rekrytering supportteamet](mailto:engineering@firmplay.com) att lägga till användare i FirmPlay - medarbetare befrämjande för rekrytering plattform.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till FirmPlay - medarbetare befrämjande för rekrytering.

![Tilldela användare][200] 

**Om du vill tilldela FirmPlay - Britta Simon utför medarbetare befrämjande för rekrytering, följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **FirmPlay - medarbetare befrämjande för rekrytering**.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_50.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på FirmPlay - medarbetare befrämjande för rekrytering panelen på panelen åtkomst du ska hämta automatiskt loggat in på ditt FirmPlay - medarbetare befrämjande för rekrytering program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/firmplay-tutorial/tutorial_general_01.png
[2]: ./media/firmplay-tutorial/tutorial_general_02.png
[3]: ./media/firmplay-tutorial/tutorial_general_03.png
[4]: ./media/firmplay-tutorial/tutorial_general_04.png

[100]: ./media/firmplay-tutorial/tutorial_general_100.png

[200]: ./media/firmplay-tutorial/tutorial_general_200.png
[201]: ./media/firmplay-tutorial/tutorial_general_201.png
[202]: ./media/firmplay-tutorial/tutorial_general_202.png
[203]: ./media/firmplay-tutorial/tutorial_general_203.png