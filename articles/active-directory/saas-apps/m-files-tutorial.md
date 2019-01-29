---
title: 'Självstudier: Azure Active Directory-integrering med M-Files | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och M-Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 41c31a987e766551682b2c90c00473ea44215dd5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150181"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Självstudier: Azure Active Directory-integrering med M-Files

I den här självstudien får du lära dig hur du integrerar M-Files med Azure Active Directory (AD Azure).

Integrera M-Files med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till M-Files
- Du kan aktivera användarna att automatiskt få loggat in på M-Files (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med M-Files, behöver du följande objekt:

- En Azure AD-prenumeration
- En M-Files enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till M-Files från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-m-files-from-the-gallery"></a>Att lägga till M-Files från galleriet
Om du vill konfigurera integreringen av M-Files till Azure AD, som du behöver lägga till M-Files från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till M-Files från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **M-Files**.

    ![Skapa en Azure AD-användare för testning](./media/m-files-tutorial/tutorial_m-files_search.png)

1. I resultatpanelen väljer **M-Files**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med M-Files baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i M-Files är för en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i M-Files upprättas.

I M-Files, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med M-Files, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare i M-Files](#creating-a-m-files-test-user)**  – du har en motsvarighet för Britta Simon i M-filer som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för M-Files.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med M-Files:**

1. I Azure-portalen på den **M-Files** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_samlbase.png)

1. På den **M-Files domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [M-Files klienten supportteamet](mailto:support@m-files.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_general_400.png)

1. För att få SSO konfigurerats för ditt program kan kontakta [M-Files supportteam](mailto:support@m-files.com) och ge dem hämtade Metadata.
   
    >[!NOTE]
    >Följ stegen nedan om du vill konfigurera enkel inloggning för du M-File skrivbordsprogram. Inga ytterligare åtgärder krävs om du vill konfigurera enkel inloggning för M-Files webbversionen.  

1. Följ stegen nedan om du vill konfigurera skrivbordsprogram M-filen för att aktivera enkel inloggning med Azure AD. För att hämta M-Files, gå till [M-Files hämta](https://www.m-files.com/en/download-latest-version) sidan.

1. Öppna den **M-Files Desktop inställningar** fönster. Klicka sedan på **Lägg till**.
   
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m_files_10.png)

1. På den **dokumentet Vault anslutningsegenskaper** och utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m_files_11.png)  

    Under servern avsnittet typ, värdena på följande sätt:  

    a. För **namn**, typen `<tenant-name>.cloudvault.m-files.com`. 
 
    b. För **portnummer**, typ **4466**. 

    c. För **protokollet**väljer **HTTPS**. 

    d. I den **autentisering** väljer **specifika Windows användaren**. Du uppmanas sedan med en signering sida. Infoga dina autentiseringsuppgifter för Azure AD. 

    e. För den **valvet på servern**, väljer du motsvarande valvet på servern.
 
    f. Klicka på **OK**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/m-files-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/m-files-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/m-files-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/m-files-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-m-files-test-user"></a>Skapa en testanvändare i M-Files

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i M-Files. Arbeta med [M-Files supportteam](mailto:support@m-files.com) att lägga till användare i M-Files.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till M-Files.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon M-Files, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **M-Files**.

    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m-files_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen M-Files i åtkomstpanelen du bör få automatiskt loggat in på ditt program för M-Files.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

