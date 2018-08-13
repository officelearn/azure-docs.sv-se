---
title: 'Självstudier: Azure Active Directory-integration med Cisco Webex | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005534"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Självstudier: Azure Active Directory-integration med Cisco Webex

Lär dig hur du integrerar Cisco Webex med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Cisco Webex med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cisco Webex.
- Du kan aktivera användarna att få loggas in automatiskt Cisco Webex med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cisco Webex, behöver du följande objekt:

- En Azure AD-prenumeration
- En Cisco-Webex enkel inloggning på aktiverad prenumeration

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här självstudien.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [skaffa en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Cisco Webex från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-cisco-webex-from-the-gallery"></a>Lägg till Cisco Webex från galleriet
För att konfigurera integrering av Cisco Webex i Azure AD, som du behöver lägga till Cisco Webex från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Cisco Webex från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Cisco Webex**. 

5. Välj **Cisco Webex** från panelen resultat. Välj sedan den **Lägg till** för att lägga till programmet.

    ![Cisco Webex i resultatlistan](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cisco Webex baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilken användaren motsvarighet i Cisco Webex är att en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och en tillhörande användare i Cisco Webex.

I Cisco Webex ger värdet **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna. 

Om du vill konfigurera och testa Azure AD enkel inloggning med Cisco Webex, utför du följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Cisco Webex](#create-a-cisco-webex-test-user) har en motsvarighet för Britta Simon i Cisco Webex som är länkad till en Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Cisco Webex-programmet.

**Om du vill konfigurera Azure AD enkel inloggning med Cisco Webex, gör du följande:**

1. I Azure-portalen på den **Cisco Webex** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. Att aktivera enkel inloggning, i den **enkel inloggning** i dialogrutan den **läge** listrutan, väljer **SAML-baserad inloggning**.
 
    ![Enkel inloggning för dialogrutan](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. I ett annat webbläsarfönster, loggar du in din Cisco Webex företagets webbplats som administratör.

4. Klicka på **inställningar** från vänstra menyn.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. På inställningssidan rulla ned under den **autentisering** klickar du på **ändra**.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Välj **integrera en identitetsprovider med 3 part. (Avancerat) ** och gå till nästa sida.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. På den **exportera katalog för Metadata** klickar du på **hämta metadatafil** att hämta för metadatafilen.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. I Azure-portalen under den **Cisco Webex domän och URL: er** avsnittet, ladda upp den hämtade **tjänstleverantör metadatafil** och konfigurera programmet genom att utföra följande steg:

    a. Klicka på **ladda upp metadatafilen**.

    ![Cisco Webex domän och URL: er med enkel inloggning för information](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    ![Cisco Webex domän och URL: er med enkel inloggning för information](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. När installationen har slutförts överför **tjänstleverantör metadatafil** den **identifierare** och **svars-URL** värden får automatiskt ifylld i **Cisco Webex Domän och URL: er** avsnittet textrutan som visas nedan:

    ![Cisco Webex domän och URL: er med enkel inloggning för information](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. I den **inloggnings-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.webex.com/`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska URL: en för inloggning. Kontakta [Cisco Webex klienten supportteamet](https://www.webex.co.in/support/support-overview.html) att hämta dessa värden.

9. Cisco Webex-programmet förväntar sig SAML-intyg innehålla specifika attribut. Konfigurera följande attribut för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    |  Attributnamn  | Attributvärde |
    | --------------- | -------------------- |    
    |   Förnamn    | User.givenName |
    |   Efternamn    | User.surname |
    |   UID    | User.Mail |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

11. På den **SAML-signeringscertifikat** väljer **XML-Metadata för**, och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. Välj **Spara**.

    ![Konfigurera den enkel inloggning spara knappen](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. På sidan Cisco Webex företagets webbplats administratör använder du webbläsaren filalternativet att leta upp och ladda upp metadatafilen Azure AD. Välj **kräver certifikat som signerats av en certifikatutfärdare i Metadata (säkrare)** och gå till nästa sida. 

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. Välj **SSO Testanslutningen**, och när en ny webbläsarflik öppnas autentisera med Azure AD genom att logga in.

15. Gå tillbaka till den **Cisco samarbete Molnhantering** flik i webbläsaren. Om testet lyckades, väljer **det här testet lyckades. Aktivera enkel inloggning för alternativet** och klicka på **spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör följande:

    ![Dialogrutan användare](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-cisco-webex-test-user"></a>Skapa en Cisco-Webex testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Cisco Webex. Cisco Webex stöder just-in-time-etablering och automatisk användaretablering, som är som standard aktiverat. Du hittar mer information om [här](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial) om hur du konfigurerar automatisk användaretablering.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska låta du användarna Britta Simon att använda Azure enkel inloggning genom att ge dem åtkomst till Cisco Webex.

![Tilldela rollen][200] 

**Om du vill tilldela Cisco Webex Britta Simon gör du följande:**

1. Öppna program i Azure-portalen. Nästa, gå till vyn directory och sedan till **företagsprogram**.  

2. Välj **alla program**.

    ![Tilldela användare][201] 

3. I listan med program väljer **Cisco Webex**.

    ![Cisco Webex-länk i listan med program](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. I menyn till vänster väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

7. Välj den **tilldela** knappen i den **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Cisco Webex i åtkomstpanelen kan hämta du loggas in automatiskt din Cisco Webex-programmet.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

