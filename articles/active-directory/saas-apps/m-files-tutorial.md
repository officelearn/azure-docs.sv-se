---
title: 'Självstudier: Azure Active Directory-integrering med M-filer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och M-filer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 26aabe7b3bfa328787b40e81471e235b2bf73765
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228082"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Självstudier: Azure Active Directory-integrering med M-filer

I kursen får lära du att integrera M-filer med Azure Active Directory (AD Azure).

Integrera M-filer med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till M-filer
- Du kan aktivera användarna att automatiskt hämta loggat in på M-filer (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med M-filer, behöver du följande:

- En Azure AD-prenumeration
- En M-filer enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till M-filer från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-m-files-from-the-gallery"></a>Lägga till M-filer från galleriet
Du måste lägga till M-filer från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av M-filer i Azure AD.

**Utför följande steg för att lägga till M-filer från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **M-filer**.

    ![Skapa en testanvändare i Azure AD](./media/m-files-tutorial/tutorial_m-files_search.png)

5. Välj i resultatpanelen **M filer**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med M-filer baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i M-filer till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i M-filer upprättas.

I M-filer, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med M-filer, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare M filer](#creating-a-m-files-test-user)**  – du har en motsvarighet för Britta Simon i M-filer som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet M-filer.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med M-filer:**

1. I Azure-portalen på den **M filer** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_samlbase.png)

3. På den **M filer domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [M-filer som klienten supportteamet](mailto:support@m-files.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_general_400.png)

6. För att få SSO konfigurerats för ditt program, kontakta [M filer supportteam](mailto:support@m-files.com) och ge dem hämtade Metadata.
   
    >[!NOTE]
    >Följ stegen nedan om du vill konfigurera enkel inloggning för dig miljon filen skrivbordsprogram. Inga ytterligare åtgärder krävs om du vill konfigurera enkel inloggning för webbversionen M-filer.  

7. Följ stegen nedan om du vill konfigurera skrivbordsprogram M-filen för att aktivera enkel inloggning med Azure AD. Om du vill hämta M-filer, gå till [M filer hämta](https://www.m-files.com/en/download-latest-version) sidan.

8. Öppna den **M filer Desktop inställningar** fönster. Klicka på **Lägg till**.
   
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m_files_10.png)

9. På den **dokumentet valvet anslutningsegenskaper** fönster, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m_files_11.png)  

    Under servern avsnittet typ, värdena på följande sätt:  

    a. För **namn**, typen `<tenant-name>.cloudvault.m-files.com`. 
 
    b. För **portnummer**, typen **4466**. 

    c. För **protokollet**väljer **HTTPS**. 

    d. I den **autentisering** väljer **specifika Windows-användare**. Du uppmanas sedan med en signering sida. Infoga Azure AD-autentiseringsuppgifterna. 

    e. För den **valvet på servern**, välj sedan motsvarande valv på servern.
 
    f. Klicka på **OK**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/m-files-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/m-files-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/m-files-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/m-files-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-m-files-test-user"></a>Skapa en testanvändare M-filer

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i M-filer. Arbeta med [M filer supportteam](mailto:support@m-files.com) att lägga till användare i M-filer.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till M-filer.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon M-filer, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **M-filer**.

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen M-filer på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet M-filer.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/m-files-tutorial/tutorial_general_01.png
[2]: ./media/m-files-tutorial/tutorial_general_02.png
[3]: ./media/m-files-tutorial/tutorial_general_03.png
[4]: ./media/m-files-tutorial/tutorial_general_04.png

[100]: ./media/m-files-tutorial/tutorial_general_100.png

[200]: ./media/m-files-tutorial/tutorial_general_200.png
[201]: ./media/m-files-tutorial/tutorial_general_201.png
[202]: ./media/m-files-tutorial/tutorial_general_202.png
[203]: ./media/m-files-tutorial/tutorial_general_203.png

