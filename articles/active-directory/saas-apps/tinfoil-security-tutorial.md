---
title: 'Självstudier: Azure Active Directory-integration med TINFOIL SECURITY | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 1ad18bd1aea36c5f185f7a8e3062b1c2103017c5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049797"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Självstudier: Azure Active Directory-integration med TINFOIL SECURITY

I den här självstudien får du lära dig hur du integrerar TINFOIL SECURITY med Azure Active Directory (AD Azure).

Integrera TINFOIL SECURITY med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TINFOIL SECURITY
- Du kan aktivera användarna att automatiskt få loggat in på TINFOIL SECURITY (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TINFOIL SECURITY, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett TINFOIL SECURITY enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till TINFOIL SECURITY från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-tinfoil-security-from-the-gallery"></a>Lägg till TINFOIL SECURITY från galleriet
För att konfigurera integrering av TINFOIL SECURITY i Azure AD, som du behöver lägga till TINFOIL SECURITY från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TINFOIL SECURITY från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TINFOIL SECURITY**väljer **TINFOIL SECURITY** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![TINFOIL SECURITY från galleriet](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TINFOIL SECURITY baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TINFOIL SECURITY är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i TINFOIL SECURITY upprättas.

TINFOIL Security, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med TINFOIL SECURITY, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  – du har en motsvarighet för Britta Simon i TINFOIL SECURITY som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TINFOIL SECURITY-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TINFOIL SECURITY:**

1. I Azure-portalen på den **TINFOIL SECURITY** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. På den **TINFOIL SECURITY domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Konfigurera enkel inloggning](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värde.

    ![Avsnittet för SAML-signeringscertifikat](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Om du vill lägga till de nödvändiga attributmappningarna, utför du följande steg:
    
    ![Attribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "attribut")
    
    | Attributnamn    |   Attributvärde |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Klicka på **lägga till användarattribut**.
    
    ![Lägg till attribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "attribut")
    
    ![Lägg till attribut](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "attribut")
    
    b. I den **attributnamnet** textrutan typ **accountid**.
    
    c. I den **attributvärdet** textrutan klistra in konto-ID-värde som du får senare i självstudien.
    
    d. Klicka på **OK**.    

6. Klicka på **spara** knappen.

    ![Knappen Spara](./media/tinfoil-security-tutorial/tutorial_general_400.png)

7. På den **TINFOIL SECURITY Configuration** klickar du på **konfigurera TINFOIL SECURITY** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![TINFOIL SECURITY-konfiguration](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. Logga in på webbplatsen för företagets TINFOIL SECURITY som en administratör i ett annat webbläsarfönster.

9. I verktygsfältet högst upp, klickar du på **mitt konto**.
   
    ![Instrumentpanelen](./media/tinfoil-security-tutorial/ic798971.png "instrumentpanel")

10. Klicka på **Security**.
   
    ![Security](./media/tinfoil-security-tutorial/ic798972.png "säkerhet")

11. På den **enkel inloggning** configuration utför följande steg:
   
    ![Enkel inloggning](./media/tinfoil-security-tutorial/ic798973.png "enkel inloggning")
   
    a. Välj **aktivera SAML**.
   
    b. Klicka på **manuell konfiguration**.
   
    c. I **SAML post-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen
   
    d. I **SAML-certifikatet fingeravtryck** textrutan klistra in värdet för **tumavtryck** som du har kopierat från **SAML-signeringscertifikat** avsnittet.
  
    e. Kopiera **ditt konto-ID** och klistra in värdet i **attributvärdet** textrutan under **lägga till attributet** avsnitt i Azure-portalen.
   
    f. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Användare och grupper -> alla användare ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Användare](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Skapa en testanvändare TINFOIL SECURITY

För att aktivera Azure AD-användare att logga in på TINFOIL SECURITY, måste de etableras i TINFOIL SECURITY. När det gäller TINFOIL SECURITY är etablering en manuell aktivitet.

**Utför följande steg för att få en användare som har etablerats:**

1. Om användaren är en del av ett Enterprise-konto, måste du [kontakta supporten TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) att hämta det användarkonto som har skapats.

2. Om användaren är en vanlig TINFOIL SECURITY SaaS-användare, kan du lägga till medarbetare till någon av användarens platser. Detta utlöser en process för att skicka en inbjudan till den angivna e-posten för att skapa ett nytt användarkonto TINFOIL SECURITY.

> [!NOTE]
> Du kan använda andra verktyg för TINFOIL SECURITY användare konto att skapa eller API: er som tillhandahålls av TINFOIL SECURITY för att etablera användarkonton i Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TINFOIL SECURITY.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon TINFOIL SECURITY, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **TINFOIL SECURITY**.

    ![Välj TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TINFOIL SECURITY i åtkomstpanelen du bör få automatiskt loggat in på ditt TINFOIL SECURITY-program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

