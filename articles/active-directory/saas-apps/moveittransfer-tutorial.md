---
title: 'Självstudier: Azure Active Directory-integrering med MOVEit - överföring i Azure AD-integrering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MOVEit överföringen – Azure AD-integrering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: e73ca95c27e7c9ef0799107dadc58c17aea5a9ca
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435925"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Självstudier: Azure Active Directory-integrering med MOVEit - överföring i Azure AD-integrering

Lär dig hur du integrerar MOVEit - överföring i Azure AD-integrering med Azure Active Directory (AD Azure) i den här självstudien.

Integrera MOVEit - överföring i Azure AD-integrering med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till MOVEit - överföring i Azure AD-integrering.
- Du kan aktivera användarna att automatiskt få loggat in på MOVEit - överföring i Azure AD-integrering (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MOVEit - överföring i Azure AD-integrering behöver du följande objekt:

- En Azure AD-prenumeration
- En MOVEit överföring – Azure AD-integrering enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till MOVEit - överföring i Azure AD-integrering från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Att lägga till MOVEit - överföring i Azure AD-integrering från galleriet
Om du vill konfigurera integreringen av MOVEit - överföring i Azure AD-integrering i Azure AD som du behöver lägga till MOVEit - överföring i Azure AD-integrering från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till MOVEit - överföring i Azure AD-integrering från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **MOVEit - överföring i Azure AD-integrering**väljer **MOVEit - överföring i Azure AD-integrering** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![MOVEit - överföring i Azure AD-integrering i resultatlistan](./media/moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med MOVEit överföring - baserat på en testanvändare som kallas ”Britta Simon” Azure AD-integrering.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i MOVEit överförings - Azure AD-integrering är till en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i MOVEit överförings - Azure AD-integrering måste upprättas.

I MOVEit överförings - Azure AD-integrering, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med MOVEit överföring – Azure AD-integrering, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en MOVEit överföring – Azure AD-integrering testanvändare](#create-a-moveit-transfer---azure-ad-integration-test-user)**  – du har en motsvarighet för Britta Simon i MOVEit överförings - Azure AD-integrering som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din MOVEit överföring – Azure AD-integrering-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med MOVEit - överföring i Azure AD-integrering:**

1. I Azure-portalen på den **MOVEit - överföring i Azure AD-integrering** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

1. På den **MOVEit överföring – Azure AD-integrering domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://contoso.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://contoso.com/<tenatid>`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Ser du de här värdena senare i **URL för tjänstmetadata providern** avsnittet eller kontakta [MOVEit överföring - supportteamet för Azure AD-integrering klienten](https://community.ipswitch.com/s/support) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/moveittransfer-tutorial/tutorial_general_400.png)
    
1. Logga in på din MOVEit överföring-klient som administratör.

1. I det vänstra navigeringsfönstret klickar du på **inställningar**.

    ![Inställningar för avsnittet på App-sida](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

1. Klicka på **enkel inloggning** länken, som finns under **säkerhetsprinciper -> användarautentisering**.

    ![Security principerna på appen på klientsidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

1. Klicka på länken Metadata-URL för att hämta för Metadatadokumentet.

    ![URL för tjänstmetadata Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Kontrollera **entityID** matchar **identifierare** i den **MOVEit överföring – Azure AD-integrering domän och URL: er** avsnittet.
    * Kontrollera **AssertionConsumerService** plats-URL: en matchar **SVARS-URL** i den **MOVEit överföring – Azure AD-integrering domän och URL: er** avsnittet.
    
    ![Konfigurera enkel inloggning på App-sida](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

1. Klicka på **Lägg till identitetsprovider** för att lägga till en ny Provider för federerad identitet.

    ![Lägg till identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

1. Klicka på **Bläddra...**  för att välja metadatafilen som du laddade ned från Azure-portalen, klicka sedan på **Lägg till identitetsprovider** att ladda upp den hämta filen.

    ![SAML-identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

1. Välj ”**Ja**” som **aktiverat** i den **redigera federerad identitet providerinställningar...**  och klicka på **spara**.

    ![Federerad identitet providerinställningar](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

1. I den **redigera federerad identitet providern användarinställningar** utför följande åtgärder:
    
    ![Redigera inställningar för federerad identitet Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Välj **SAML NameID** som **inloggningsnamn**.
    
    b. Välj **andra** som **fullständigt namn** och i den **attributnamnet** textrutan anger du värdet: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Välj **andra** som **e-post** och i den **attributnamnet** textrutan anger du värdet: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Välj **Ja** som **skapa automatiskt konto på inloggning**.
    
    e. Klicka på **spara** knappen.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/moveittransfer-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/moveittransfer-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/moveittransfer-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/moveittransfer-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Skapa en MOVEit överföring - testanvändare i Azure AD-integrering

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i MOVEit överförings - Azure AD-integrering. MOVEit - överföring i Azure AD-integrering har stöd för just-in-time-etablering, som du har aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt MOVEit - överföring i Azure AD-integrering om det inte finns ännu.

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [MOVEit överföring - supportteamet för Azure AD-integrering klienten](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MOVEit - överföring i Azure AD-integrering.

![Tilldela rollen][200] 

**Utför följande steg för att tilldela Britta Simon MOVEit - överföring i Azure AD-integrering:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **MOVEit - överföring i Azure AD-integrering**.

    ![MOVEit överföringen – Azure AD-integrering länk i listan med program](./media/moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på MOVEit överföringen - panelen Azure AD-integrering i åtkomstpanelen, du bör få automatiskt loggat in på din MOVEit överföring – Azure AD-integrering-program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/moveittransfer-tutorial/tutorial_general_203.png

