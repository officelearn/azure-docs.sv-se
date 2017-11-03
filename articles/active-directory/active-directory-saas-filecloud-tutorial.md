---
title: "Självstudier: Azure Active Directory-integrering med FileCloud | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FileCloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f39f0ddd-b504-4562-971f-77b88d1e75fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: ad03516f684acc59912ffc57f6e0712828bd03f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Självstudier: Azure Active Directory-integrering med FileCloud

I kursen får lära du att integrera FileCloud med Azure Active Directory (AD Azure).

Integrera FileCloud med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FileCloud.
- Du kan aktivera användarna att automatiskt hämta loggat in på FileCloud (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med FileCloud, behöver du följande:

- En Azure AD-prenumeration
- En FileCloud enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till FileCloud från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-filecloud-from-the-gallery"></a>Att lägga till FileCloud från galleriet
Du måste lägga till FileCloud från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av FileCloud i Azure AD.

**Utför följande steg för att lägga till FileCloud från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **FileCloud**väljer **FileCloud** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![FileCloud i resultatlistan](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med FileCloud baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i FileCloud motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i FileCloud upprättas.

I FileCloud, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med FileCloud, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FileCloud](#create-a-filecloud-test-user)**  – du har en motsvarighet för Britta Simon i FileCloud som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt FileCloud program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FileCloud:**

1. I Azure-portalen på den **FileCloud** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_samlbase.png)

3. På den **FileCloud domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och FileCloud domän med enkel inloggning information](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<subdomain>.filecloudhosted.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [FileCloud klienten supportteamet](mailto:support@codelathe.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-filecloud-tutorial/tutorial_general_400.png)

6. På den **FileCloud Configuration** klickar du på **konfigurera FileCloud** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![FileCloud konfiguration](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_configure.png) 

7. I en annan webbläsarfönster inloggning till FileCloud-klient som administratör.

8. I det vänstra navigeringsfönstret klickar du på **inställningar**. 
   
    ![Inställningar för avsnittet på App-sida](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

9. Klicka på **SSO** för inställningar. 
   
    ![Enkel inloggning fliken på App-sida](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

10. Välj **SAML** som **standard SSO typen** på **inställningar för enkel inloggning (SSO)** panelen.
   
    ![Enkel inloggning panelen i appen inställningar på klientsidan](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

11. Klistra in **SAML enhets-ID**, som du har kopierat från Azure-portalen i den **IdP slutpunkt URL** textruta.

    ![IDP slutet punkt URL: en textruta](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

12. Öppna hämtade metadatafilen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **IdP metadata** textruta på **SAML inställningar** panelen.

    ![IDP Meta dataavsnittet på App-sida](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

13. Klicka på **spara** knappen.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-filecloud-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-filecloud-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-filecloud-test-user"></a>Skapa en testanvändare FileCloud

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i FileCloud. FileCloud stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt FileCloud om den inte finns.

>[!NOTE]
>Om du behöver skapa en användare manuellt, måste du kontakta den [FileCloud klienten supportteamet](mailto:support@codelathe.com). 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till FileCloud.

![Tilldela rollen][200] 

**Om du vill tilldela FileCloud Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **FileCloud**.

    ![Länken FileCloud i listan med program](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen FileCloud på åtkomstpanelen du bör få automatiskt loggat in på ditt FileCloud program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png

