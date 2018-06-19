---
title: 'Självstudier: Azure Active Directory-integrering med TargetProcess | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 1f2cea7f68e991401f5efa1773e6c67685efde49
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968222"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Självstudier: Azure Active Directory-integrering med TargetProcess

I kursen får lära du att integrera TargetProcess med Azure Active Directory (AD Azure).

Integrera TargetProcess med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TargetProcess
- Du kan aktivera användarna att automatiskt hämta loggat in på TargetProcess (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med TargetProcess, behöver du följande:

- En Azure AD-prenumeration
- En TargetProcess enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till TargetProcess från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-targetprocess-from-the-gallery"></a>Lägg till TargetProcess från galleriet
Du måste lägga till TargetProcess från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av TargetProcess i Azure AD.

**Utför följande steg för att lägga till TargetProcess från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TargetProcess**väljer **TargetProcess** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Lägg till TargetProcess från galleriet](./media/target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med TargetProcess baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i TargetProcess motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TargetProcess upprättas.

I TargetProcess, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TargetProcess, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TargetProcess](#create-a-targetprocess-test-user)**  – du har en motsvarighet för Britta Simon i TargetProcess som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TargetProcess program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TargetProcess:**

1. I Azure-portalen på den **TargetProcess** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/target-process-tutorial/tutorial_target-process_samlbase.png)

3. På den **TargetProcess domän och URL: er** avsnittet, utför följande steg:

    ![Avsnittet TargetProcess domän och URL: er](./media/target-process-tutorial/tutorial_target-process_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.tpondemand.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.tpondemand.com/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [TargetProcess klienten supportteamet](mailto:support@targetprocess.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Signeringscertifikat för SAML-avsnitt](./media/target-process-tutorial/tutorial_target-process_certificate.png) 

5. Klicka på **spara** knappen.

    ![Knappen Spara](./media/target-process-tutorial/tutorial_general_400.png)

6. På den **TargetProcess Configuration** klickar du på **konfigurera TargetProcess** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![TargetProcess konfigurationsavsnitt](./media/target-process-tutorial/tutorial_target-process_configure.png) 

7. Inloggning till TargetProcess programmet som administratör.

8. Klicka på menyn högst upp **installationsprogrammet**.
   
    ![Konfiguration](./media/target-process-tutorial/tutorial_target_process_05.png)

9. Klicka på **inställningar**.
   
    ![Inställningar](./media/target-process-tutorial/tutorial_target_process_06.png) 

10. Klicka på **enkel inloggning**.
   
    ![Klicka på enkel inloggning](./media/target-process-tutorial/tutorial_target_process_07.png) 

11. Utför följande steg på enkel inloggning dialogrutan Inställningar för:
   
    ![Konfigurera enkel inloggning](./media/target-process-tutorial/tutorial_target_process_08.png)
    
    a. Klicka på **aktivera enkel inloggning**.
    
    b. I **inloggnings-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Öppna din hämtat certifikat i anteckningar, kopiera innehållet och klistrar in det i den **certifikat** textruta.
    
    d. Klicka på **aktivera JIT etablering**.

    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/target-process-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Visa en lista över användare](./media/target-process-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/target-process-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Avsnittet för användare](./media/target-process-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-targetprocess-test-user"></a>Skapa en testanvändare TargetProcess

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i TargetProcess.

TargetProcess stöder just-in-time-etablering. Du har redan aktiverats i [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

Det finns ingen åtgärd objekt i det här avsnittet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TargetProcess.

![Tilldela användare][200] 

**Om du vill tilldela TargetProcess Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TargetProcess**.

    ![TargetProcess i listan över appar](./media/target-process-tutorial/tutorial_target-process_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen TargetProcess på åtkomstpanelen du bör få automatiskt loggat in på ditt TargetProcess program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/target-process-tutorial/tutorial_general_01.png
[2]: ./media/target-process-tutorial/tutorial_general_02.png
[3]: ./media/target-process-tutorial/tutorial_general_03.png
[4]: ./media/target-process-tutorial/tutorial_general_04.png

[100]: ./media/target-process-tutorial/tutorial_general_100.png

[200]: ./media/target-process-tutorial/tutorial_general_200.png
[201]: ./media/target-process-tutorial/tutorial_general_201.png
[202]: ./media/target-process-tutorial/tutorial_general_202.png
[203]: ./media/target-process-tutorial/tutorial_general_203.png

