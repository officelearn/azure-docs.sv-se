---
title: 'Självstudier: Azure Active Directory-integration med Palo Alto Networks - GlobalProtect | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 6f395897687235f0956928fd0a5dccf00d4c7d12
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041049"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Självstudier: Azure Active Directory-integration med Palo Alto Networks - GlobalProtect

I den här självstudien får du lära dig hur du integrerar Palo Alto Networks - GlobalProtect med Azure Active Directory (AD Azure).

Integrera Palo Alto Networks - GlobalProtect med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Palo Alto Networks - GlobalProtect.
- Du kan aktivera användarna att automatiskt få loggat in på Palo Alto Networks - GlobalProtect (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo Alto Networks - GlobalProtect, behöver du följande objekt:

- En Azure AD-prenumeration
- En Palo Alto Networks - GlobalProtect enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Palo Alto Networks - GlobalProtect från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Att lägga till Palo Alto Networks - GlobalProtect från galleriet
Om du vill konfigurera integreringen av Palo Alto Networks - GlobalProtect till Azure AD som du behöver lägga till Palo Alto Networks - GlobalProtect från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Palo Alto Networks - GlobalProtect från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Palo Alto Networks - GlobalProtect**väljer **Palo Alto Networks - GlobalProtect** resultatet panelen klickar **Lägg till** för att lägga till programmet .

    ![Palo Alto Networks - GlobalProtect i resultatlistan](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - GlobalProtect baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Palo Alto Networks - GlobalProtect är till en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i Palo Alto Networks - GlobalProtect måste upprättas.

I Palo Alto Networks - GlobalProtect, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - GlobalProtect, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Palo Alto Networks - GlobalProtect testanvändare](#create-a-palo-alto-networks---globalprotect-test-user)**  – du har en motsvarighet för Britta Simon i Palo Alto Networks - GlobalProtect som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Palo Alto Networks - GlobalProtect program.

**Om du vill konfigurera Azure AD utför enkel inloggning med Palo Alto Networks - GlobalProtect, du följande steg:**

1. I Azure-portalen på den **Palo Alto Networks - GlobalProtect** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

3. På den **Palo Alto Networks - GlobalProtect domän och URL: er** avsnittet, utför följande steg:

    ![Palo Alto Networks - GlobalProtect domän och URL: er enkel inloggning för information](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<Customer Firewall URL>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Palo Alto Networks - GlobalProtect klienten supportteamet](https://support.paloaltonetworks.com/support) att hämta dessa värden. 
 
4. Palo Alto Networks - GlobalProtect program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
5. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | --- | --- |    
    | användarnamn | User.userPrincipalName |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden. Vi har mappat värdet med user.userprincipalname som exempel men du kan mappa med din lämpligt värde. 
    
    d. Klicka på **Ok**


6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/paloaltoglobalprotect-tutorial/tutorial_general_400.png)

8. Öppna Admin-Gränssnittet Palo Alto Networks-brandväggen som en administratör i ett annat webbläsarfönster.

9. Klicka på **enhet**.

    ![Konfigurera Palo Alto enkel inloggning](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Välj **SAML-identitetsprovider** i det vänstra navigeringsfönstret och klickar på ”Importera” om du vill importera metadatafilen.

    ![Konfigurera Palo Alto enkel inloggning](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Utför följande åtgärder i fönstret Import

    ![Konfigurera Palo Alto enkel inloggning](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. I den **profilnamn** textrutan, ange ett namn t.ex Azure AD GlobalProtect.
    
    b. I **identitet providern Metadata**, klickar du på **Bläddra** och välj metadata.xml-fil som du har hämtat från Azure-portalen
    
    c. Klicka på **OK**

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/paloaltoglobalprotect-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/paloaltoglobalprotect-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/paloaltoglobalprotect-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>Skapa en Palo Alto Networks - GlobalProtect testanvändare

Palo Alto Networks - GlobalProtect som stöder Just-in-time användaretablering så att en användare skapas automatiskt i systemet efter en lyckad autentisering om den inte redan finns. Du behöver inte göra någonting här. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto Networks - GlobalProtect.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Palo Alto Networks - GlobalProtect, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Palo Alto Networks - GlobalProtect**.

    ![Palo Alto nätverk - GlobalProtect länk i listan med program](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Palo Alto Networks - GlobalProtect panel i åtkomstpanelen, du bör få automatiskt loggat in på ditt Palo Alto Networks - GlobalProtect program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_203.png

