---
title: 'Självstudier: Azure Active Directory-integrering med MOVEit överföring - integrering med Azure AD | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MOVEit överföringen - integrering med Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: f9bfd3ad884ad36026d56af514391c5f75a97e51
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347830"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Självstudier: Azure Active Directory-integrering med MOVEit överföring - Azure AD-integrering

I kursen får lära du att integrera MOVEit överföring - Azure AD-integrering med Azure Active Directory (AD Azure).

Integrera MOVEit överföring - Azure AD-integrering med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till MOVEit överföring - integrering med Azure AD.
- Du kan aktivera användarna att automatiskt hämta inloggade MOVEit - överföring av Azure AD-integrering (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MOVEit överföring - Azure AD-integrering, behöver du följande:

- En Azure AD-prenumeration
- En MOVEit överföring - Azure AD-integrering enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till MOVEit överföring - integrering med Azure AD från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Lägga till MOVEit överföring - integrering med Azure AD från galleriet
Du måste lägga till MOVEit överföring - Azure AD-integrering från galleriet i din lista över hanterade SaaS-appar för att konfigurera MOVEit överföring - integrering med Azure AD till Azure AD-integrering.

**Utför följande steg för att lägga till MOVEit överföring - integrering med Azure AD från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **MOVEit överföring - integrering med Azure AD**väljer **MOVEit överföring - integrering med Azure AD** resultatet-panelen klickar **Lägg till** för att lägga till den programmet.

    ![MOVEit överföring - Azure AD-integrering i resultat-listan](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med MOVEit överföring - integrering med Azure AD utifrån en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i MOVEit överföring - integrering med Azure AD motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i MOVEit överföring - Azure AD-integrering måste upprättas.

I MOVEit överföring - Azure AD-integrering, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med MOVEit överföring - Azure AD-integrering, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en MOVEit överföring - Azure AD-integrering testanvändare](#create-a-moveit-transfer---azure-ad-integration-test-user)**  – har en motsvarighet för Britta Simon MOVEit överföring - integrering med Azure AD som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din MOVEit överföring - program för Azure AD-integrering.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med MOVEit överföring - Azure AD-integrering:**

1. I Azure-portalen på den **MOVEit överföring - integrering med Azure AD** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. På den **MOVEit överföring - URL: er och integrering med Azure AD Domain** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://contoso.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://contoso.com/<tenatid>`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Du kan se dessa värden senare i **URL för tjänstmetadata providern** avsnitt eller kontakta [MOVEit överföring - supportteamet för Azure AD-integrering klienten](https://community.ipswitch.com/s/support) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Logga in på din MOVEit överföring klient som administratör.

7. I det vänstra navigeringsfönstret klickar du på **inställningar**.

    ![Inställningar för avsnittet på App-sida](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Klicka på **enkel inloggning** länk som är under **principer -> användaren Auth**.

    ![Säkerhet principer på App-sida](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Klicka på länken Metadata-URL för att hämta Metadatadokumentet.

    ![URL för tjänstmetadata Provider](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Kontrollera **ID för entiteterna** matchar **identifierare** i den **MOVEit överföring - URL: er och integrering med Azure AD Domain** avsnitt.
    * Kontrollera **AssertionConsumerService** -URL: en matchar **REPLY URL** i den **MOVEit överföring - URL: er och integrering med Azure AD Domain** avsnitt.
    
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Klicka på **lägga till identitetsleverantör** för att lägga till en ny Provider för federerad identitet.

    ![Lägg till identitetsleverantören.](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Klicka på **Bläddra...**  för att välja metadatafilen som du laddade ned från Azure-portalen, klicka på **lägga till identitetsleverantör** att ladda upp den hämta filen.

    ![SAML-identitetsprovider](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Välj ”**Ja**” som **aktiverat** i den **redigera federerad identitet providerinställningar...**  och klickar på **spara**.

    ![Federerade identiteter providerinställningar](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. I den **redigera federerad identitet providern användarinställningar** utför följande åtgärder:
    
    ![Redigera inställningar för federerad identitet-Provider](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Välj **SAML NameID** som **inloggningsnamnet**.
    
    b. Välj **andra** som **fullständiga namn** och i den **attributnamn** textruta placera värdet: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Välj **andra** som **e-post** och i den **attributnamn** textruta placera värdet: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Välj **Ja** som **skapa automatiskt konto på inloggning**.
    
    e. Klicka på **spara** knappen.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Skapa en MOVEit överföring - testanvändare i Azure AD-integrering

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i MOVEit överföring - integrering med Azure AD. MOVEit överföring - integrering med Azure AD stöder just-in-time-allokering som du har aktiverat. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt MOVEit överföring - Azure AD-integrering om den inte finns.

>[!NOTE]
>Om du behöver skapa en användare manuellt, måste du kontakta den [MOVEit överföring - supportteamet för Azure AD-integrering klienten](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MOVEit överföring - integrering med Azure AD.

![Tilldela rollen][200] 

**Utför följande steg om du vill tilldela Britta Simon MOVEit - överföring av Azure AD-integrering:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **MOVEit överföring - integrering med Azure AD**.

    ![MOVEit överföringen - Azure AD-integrering länken i listan med program](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på MOVEit överföringen - panelen Azure AD-integrering i panelen åtkomst du ska hämta automatiskt loggat in på ditt MOVEit överföring - program för Azure AD-integrering. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

