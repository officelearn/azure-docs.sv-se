---
title: 'Självstudier: Azure Active Directory-integrering med Bomgar fjärrsupport | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bomgar fjärrsupport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: 2d0a4df20ef513b2a6524ba92656a7f861da8458
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116182"
---
# <a name="tutorial-azure-active-directory-integration-with-bomgar-remote-support"></a>Självstudier: Azure Active Directory-integrering med Bomgar fjärrsupport

I kursen får lära du att integrera Bomgar Fjärrsupport med Azure Active Directory (AD Azure).

Integrera Bomgar Fjärrsupport med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bomgar fjärrsupport.
- Du kan aktivera användarna att automatiskt hämta loggat in på Bomgar fjärrsupport (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Bomgar fjärrsupport behöver du följande:

- En Azure AD-prenumeration
- En Bomgar fjärrsupport enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Bomgar fjärrsupport från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bomgar-remote-support-from-the-gallery"></a>Att lägga till Bomgar fjärrsupport från galleriet
Du måste lägga till Bomgar fjärrsupport från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Bomgar fjärrsupport i Azure AD.

**Utför följande steg för att lägga till Bomgar fjärrsupport från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Bomgar fjärrsupport**väljer **Bomgar fjärrsupport** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Bomgar fjärrsupport i resultatlistan](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Bomgar fjärrsupport baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Bomgar fjärrsupport motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Bomgar fjärrsupport upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bomgar fjärrsupport, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Bomgar fjärrsupport](#create-a-bomgar-remote-support-test-user)**  – har en motsvarighet för Britta Simon Bomgar fjärrsupport som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Bomgar fjärrsupport program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bomgar fjärrsupport:**

1. I Azure-portalen på den **Bomgar fjärrsupport** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_samlbase.png)

3. På den **Bomgar fjärranslutna stöd för domänen och URL: er** avsnittet, utför följande steg:

    ![URL: er och Bomgar fjärranslutna stöd domänen med enkel inloggning information](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<SUBDOMAIN>.trafficmanager.net/saml`

    b. I den **identifierare (enhets-ID)** textruta Skriv en URL med följande mönster: `https://<SUBDOMAIN>.trafficmanager.net`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare (enhets-ID). Kontakta [Bomgar stöd för fjärrklienter supportteamet](https://www.bomgar.com/docs/index.htm#support) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/bomgarremotesupport-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **Bomgar Remote stöder** sida, måste du skicka den hämtade **XML-Metadata för** till [Bomgar Remote stöder supportteamet](https://www.bomgar.com/docs/index.htm#support). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/bomgarremotesupport-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/bomgarremotesupport-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/bomgarremotesupport-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/bomgarremotesupport-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-bomgar-remote-support-test-user"></a>Skapa en testanvändare Bomgar fjärrsupport

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Bomgar fjärrsupport. Bomgar fjärrsupport stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Bomgar fjärrsupport om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [Bomgar Remote stöder supportteamet](https://www.bomgar.com/docs/index.htm#support).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bomgar fjärrsupport.

![Tilldela rollen][200] 

**Om du vill tilldela Bomgar fjärrsupport Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Bomgar fjärrsupport**.

    ![Länken Bomgar fjärrsupport i listan med program](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Bomgar fjärrsupport på åtkomstpanelen du bör få automatiskt loggat in på ditt Bomgar fjärrsupport program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bomgarremotesupport-tutorial/tutorial_general_01.png
[2]: ./media/bomgarremotesupport-tutorial/tutorial_general_02.png
[3]: ./media/bomgarremotesupport-tutorial/tutorial_general_03.png
[4]: ./media/bomgarremotesupport-tutorial/tutorial_general_04.png

[100]: ./media/bomgarremotesupport-tutorial/tutorial_general_100.png

[200]: ./media/bomgarremotesupport-tutorial/tutorial_general_200.png
[201]: ./media/bomgarremotesupport-tutorial/tutorial_general_201.png
[202]: ./media/bomgarremotesupport-tutorial/tutorial_general_202.png
[203]: ./media/bomgarremotesupport-tutorial/tutorial_general_203.png

