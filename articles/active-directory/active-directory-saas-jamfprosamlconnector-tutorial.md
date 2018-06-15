---
title: 'Självstudier: Azure Active Directory-integrering med Jamf Pro | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 687453b37e15f5d3dd1fa161f89b6d18f90ba279
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343427"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Självstudier: Azure Active Directory-integrering med Jamf Pro

I kursen får lära du att integrera Jamf Pro med Azure Active Directory (AD Azure).

Integrera Jamf Pro med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jamf Pro.
- Du kan aktivera användarna att automatiskt hämta inloggade Jamf Pro (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Jamf Pro, behöver du följande:

- En Azure AD-prenumeration
- En Jamf Pro enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Jamf Pro från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jamf-pro-from-the-gallery"></a>Att lägga till Jamf Pro från galleriet
Du måste lägga till Jamf Pro från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Jamf Pro i Azure AD.

**Utför följande steg för att lägga till Jamf Pro från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Jamf Pro**väljer **Jamf Pro** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Jamf Pro i resultatlistan](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med Jamf Pro baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Jamf Pro motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Jamf Pro upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jamf Pro, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Jamf Pro](#create-a-jamf-pro-test-user)**  – du har en motsvarighet för Britta Simon i Jamf Pro som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Jamf Pro.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jamf Pro:**

1. I Azure-portalen på den **Jamf Pro** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. På den **Jamf Pro domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Jamf Pro domän med enkel inloggning information](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. I den **identifierare (enhets-ID)** textruta Skriv en URL med följande mönster: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.jamfcloud.com/saml/SSO`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Jamf Pro domän med enkel inloggning information](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Du får det faktiska värdet för identifieraren från **enkel inloggning** avsnitt i Jamf Pro portal som beskrivs senare i självstudierna. Du kan extrahera den faktiska **underdomän** värde från ID-värdet och använda **underdomän** information i inloggnings-URL och Reply URL.

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar.

    ![Länken hämta certifikatet](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. Logga in på webbplatsen Jamf Pro företag som en administratör i en annan webbläsarfönster.

8. Klicka på den **inställningsikonen** från det övre högra hörnet på sidan.

    ![Jamf Pro-konfiguration](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

9. Klicka på **enkel inloggning**.

    ![Jamf Pro-konfiguration](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure2.png)

10. Bläddra nedåt upp till **IDENTITETSLEVERANTÖR** under den **enkel inloggning** avsnittet och utför följande steg:

    ![Jamf Pro-konfiguration](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure3.png)

    a. Välj **andra** som ett alternativ från den **IDENTITETSLEVERANTÖR** listrutan.

    b. I den **andra PROVIDERN** textruta ange **Azure AD**.

    c. Välj **URL för tjänstmetadata** som ett alternativ från den **identitet PROVIDERN METADATAKÄLLA** listrutan och klistra in i textrutan följande den **webbadress Federation Metadata** värdet som du har kopierat från Azure-portalen.

    d. Kopiera den **enhets-ID** värdet och klistrar in det i den **identifierare (enhets-ID)** TextBox-kontroll i **Jamf Pro domän och URL: er** avsnitt på Azure-portalen.

    >[!NOTE]
    > Här `aadsso` ingår underdomän, (vilket är för referens ändamål). Använd det här värdet för att slutföra inloggnings-URL och Reply URL i den **Jamf Pro domän och URL: er** avsnitt på Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-jamf-pro-test-user"></a>Skapa en testanvändare Jamf Pro

Om du vill aktivera Azure AD-användare kan logga in på Jamf Pro, måste de etableras i Jamf Pro. När det gäller Jamf Pro är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Jamf Pro företag som administratör.

2. Klicka på den **inställningsikonen** från det övre högra hörnet på sidan.

    ![Lägga till medarbetare](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

3. Klicka på **Jamf Pro användarkonton och grupper**.

    ![Lägga till medarbetare](./media/active-directory-saas-jamfprosamlconnector-tutorial/user1.png)

4. Klicka på **Ny**.

    ![Lägga till medarbetare](./media/active-directory-saas-jamfprosamlconnector-tutorial/user2.png)

5. Välj **skapa standardkonto**.

    ![Lägga till medarbetare](./media/active-directory-saas-jamfprosamlconnector-tutorial/user3.png)

6. På den **nytt konto** dailog, utför följande steg:

    ![Lägga till medarbetare](./media/active-directory-saas-jamfprosamlconnector-tutorial/user4.png)

    a. I den **användarnamn** textruta skriver du det fullständiga namnet på BrittaSimon.

    b. Välj lämpliga alternativ i din organisation för **ÅTKOMSTNIVÅ**, **PRIVILEGIET värdet**, och för **ÅTKOMSTSTATUS**.
    
    c. I den **FULLSTÄNDIGA namn** textruta skriver du det fullständiga namnet på Britta Simon.

    d. I den **e-postadress** textruta skriver Britta Simon konto e-postadress.

    e. I den **lösenord** textruta skriver du lösenordet för användaren.

    f. I den **BEKRÄFTA lösenord** textruta skriver du lösenordet för användaren.

    g. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jamf Pro.

![Tilldela rollen][200] 

**Om du vill tilldela Jamf Pro Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Jamf Pro**.

    ![Jamf Pro-länken i listan med program](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Jamf Pro på åtkomstpanelen du bör få automatiskt loggat in på ditt Jamf Pro-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_203.png

