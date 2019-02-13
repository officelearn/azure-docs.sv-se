---
title: 'Självstudier: Azure Active Directory-integrering med TigerText Secure Messenger | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TigerText skydda Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20bb003fbb8c6e7c1592618a8742f9b88882e358
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184150"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Självstudier: Azure Active Directory-integrering med TigerText Secure Messenger

Lär dig hur du integrerar TigerText skydda Messenger med Azure Active Directory (AD Azure) i den här självstudien.

Integrera TigerText skydda Messenger med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TigerText skydda Messenger
- Du kan aktivera användarna att automatiskt få loggat in på TigerText skydda Messenger (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TigerText Secure Messenger, behöver du följande objekt:

- En Azure AD-prenumeration
- En TigerText skydda Messenger enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till TigerText skydda Messenger från galleriet
1. Konfigurera och testa enkel inloggning med Azure AD

## <a name="add-tigertext-secure-messenger-from-the-gallery"></a>Lägg till TigerText skydda Messenger från galleriet
För att konfigurera integrering av TigerText skydda Messenger i Azure AD, som du behöver lägga till TigerText skydda Messenger från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TigerText skydda Messenger från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **TigerText skydda Messenger**väljer **TigerText skydda Messenger** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/tigertext-tutorial/tutorial_tigertext_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TigerText Secure Messenger baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TigerText skydda Messenger är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TigerText skydda Messenger upprättas.

I TigerText skydda Messenger, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med TigerText Secure Messenger, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare TigerText skydda Messenger](#create-a-tigertext-secure-messenger-test-user)**  – du har en motsvarighet för Britta Simon i TigerText skydda Messenger som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TigerText skydda Messenger-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TigerText Secure Messenger:**

1. I Azure-portalen på den **TigerText skydda Messenger** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/tigertext-tutorial/tutorial_tigertext_samlbase.png)

1. På den **TigerText skydda Messenger domän och URL: er** avsnittet, utför följande steg:

    ![TigerText skydda Messenger domän och URL: er](./media/tigertext-tutorial/tutorial_tigertext_url.png)

    a. I den **inloggnings-URL** textrutan typen URL: en som: `https://home.tigertext.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://saml-lb.tigertext.me/v1/organization/<instance Id>`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [TigerText skydda Messenger-klienten supportteamet](mailTo:prosupport@tigertext.com) att hämta det här värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Avsnittet för SAML-signeringscertifikat](./media/tigertext-tutorial/tutorial_tigertext_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Knappen Spara](./media/tigertext-tutorial/tutorial_general_400.png)

1. För att få enkel inloggning konfigurerad för ditt program kan kontakta [TigerText skydda Messenger supportteamet](mailTo:prosupport@tigertext.com) och ge dem den **hämtade metadata**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/tigertext-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Användare och grupper -> alla användare](./media/tigertext-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Lägg till knapp](./media/tigertext-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Användardialogrutan](./media/tigertext-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-tigertext-secure-messenger-test-user"></a>Skapa en testanvändare TigerText skydda Messenger

I det här avsnittet skapar du en användare som kallas Britta Simon i TigerText. Vänligen kontakta [TigerText skydda Messenger-klienten supportteamet](mailTo:prosupport@tigertext.com) att lägga till användare i TigerText-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TigerText skydda Messenger.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon TigerText skydda Messenger, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **TigerText skydda Messenger**.

    ![TigerText skydda Messenger i applistan](./media/tigertext-tutorial/tutorial_tigertext_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TigerText i åtkomstpanelen du bör få automatiskt loggat in på ditt TigerText program. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tigertext-tutorial/tutorial_general_01.png
[2]: ./media/tigertext-tutorial/tutorial_general_02.png
[3]: ./media/tigertext-tutorial/tutorial_general_03.png
[4]: ./media/tigertext-tutorial/tutorial_general_04.png

[100]: ./media/tigertext-tutorial/tutorial_general_100.png

[200]: ./media/tigertext-tutorial/tutorial_general_200.png
[201]: ./media/tigertext-tutorial/tutorial_general_201.png
[202]: ./media/tigertext-tutorial/tutorial_general_202.png
[203]: ./media/tigertext-tutorial/tutorial_general_203.png

