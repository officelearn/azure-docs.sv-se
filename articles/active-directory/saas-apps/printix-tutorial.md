---
title: 'Självstudiekurs: Azure Active Directory-integrering med Printix | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "62111467"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Självstudiekurs: Azure Active Directory-integrering med Printix

I den här självstudien får du lära dig hur du integrerar Printix med Azure Active Directory (Azure AD).

Genom att integrera Printix med Azure AD får du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Printix
- Du kan göra det möjligt för användarna att automatiskt logga in på Printix (Enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-portalen

Om du vill veta mer om Integrering av SaaS-appar med Azure AD läser du [vad som är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Printix behöver du följande objekt:

- En Azure AD-prenumeration
- En Printix-prenumeration med enkel inloggning

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en utvärderingsmiljö för Azure AD kan du få en utvärderingsversion på en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien testar du azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två huvudbyggstenar:

1. Lägga till Printix från galleriet
1. Konfigurera och testa enkel inloggning i Azure AD

## <a name="adding-printix-from-the-gallery"></a>Lägga till Printix från galleriet
Om du vill konfigurera integreringen av Printix i Azure AD måste du lägga till Printix från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Printix från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen. 

    ![Active Directory][1]

1. Navigera till **Enterprise-program**. Gå sedan till **Alla program**.

    ![Program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Program][3]

1. Skriv **Printix**i sökrutan .

    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/tutorial_printix_search.png)

1. Välj **Printix**på resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning i Azure AD
I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Printix baserat på en testanvändare som heter "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD veta vad motpartsanvändaren i Printix är för en användare i Azure AD. Med andra ord måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Printix upprättas.

I Printix tilldelar du värdet för **användarnamnet** i Azure AD som värdet för **användarnamnet** för att upprätta länkrelationen.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Printix måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med Britta Simon.
1. **[Skapa en Printix-testanvändare](#creating-a-printix-test-user)** - att ha en motsvarighet till Britta Simon i Printix som är länkad till Azure AD-representationen av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assigning-the-azure-ad-test-user)** – så att Britta Simon kan använda azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)** - för att kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning i Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen och konfigurerar enkel inloggning i ditt Printix-program.

**Så här konfigurerar du en enkel Azure AD-inloggning med Printix:**

1. Klicka på Enkel inloggning på sidan Printix-programintegrering **på Azure-portalen.** **Printix**

    ![Konfigurera enkel inloggning][4]

1. I dialogrutan **Enkel inloggning** väljer du **Läge** som **SAML-baserad inloggning** för att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Gör följande i avsnittet **Printix-domän och URL:er:**

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_url.png)

    Skriv en URL i textrutan **Sign-on URL** med följande mönster:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta Supportteamet för [Printix-klienten](mailto:support@printix.net) för att få värdet. 
 
1. Klicka på **Metadata XML** i avsnittet **SAML-signeringscertifikat** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_general_400.png)

1. Logga in på din Printix-klient som administratör.

1. Klicka på ikonen längst upp till höger i menyn högst upp och välj "**Autentisering**".
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_06.png)

1. Välj **Aktivera Azure/Office 365-autentisering** på fliken **Installation**
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_07.png)

1. På fliken **Azure,** indatafederation metadata URL till textrutan för "**Federation metadata dokument**". 

    Bifoga metadata xml-filen som du hämtade från Azure AD till [Printix supportteam](mailto:support@printix.net). Sedan laddar de upp xml-filen och tillhandahåller en url för federationsmetadata.
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klicka på knappen "**Testa**" och klicka på "**OK**" om testet lyckades.
   
     Azure active directory-sidan visas när du har klickat på **testknappen.** "Testet lyckades" här innebär att efter att ha angett autentiseringsuppgifterna för ditt Azure-testkonto kommer det att dyka upp ett meddelande "Inställningar testade OK". Klicka sedan **OK** på OK-knappen.
   
    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_09.png)

1. Klicka på sidan **Spara** på sidan "**Autentisering**".


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om den inbäddade dokumentationsfunktionen här: [Azure AD inbäddad dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare för Azure AD
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa Azure AD-användare][100]

**Så här skapar du en testanvändare i Azure AD:**

1. Klicka på Azure Active Directory i det vänstra navigeringsfönstret i **Azure-portalen**. **Azure Active Directory**

    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_01.png) 

1. Om du vill visa listan över användare går du till **Användare och grupper** och klickar på Alla **användare**.
    
    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_02.png) 

1. Om du vill öppna dialogrutan **Användare** klickar du på **Lägg till** högst upp i dialogrutan.
 
    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_03.png) 

1. Gör följande på dialogrutan **Användare:**
 
    ![Skapa en testanvändare för Azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. Skriv **BrittaSimon**i textrutan **Namn** .

    b. Skriv **e-postadressen** till BrittaSimon i textrutan **Användarnamn.**

    c. Välj **Visa lösenord** och skriv ned värdet för **lösenordet**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-printix-test-user"></a>Skapa en Printix-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i Printix. Printix stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas under ett försök att komma åt Printix om det inte finns ännu. 

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Printix supportteam](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Printix.

![Tilldela användare][200] 

**Så här tilldelar du Britta Simon till Printix:**

1. Öppna programvyn i Azure-portalen och navigera sedan till katalogvyn och gå till **Enterprise-program** och klicka sedan på **Alla program**.

    ![Tilldela användare][201] 

1. Välj **Printix**i programlistan .

    ![Konfigurera enkel inloggning](./media/printix-tutorial/tutorial_printix_app.png) 

1. Klicka på Användare och **grupper**på menyn till vänster .

    ![Tilldela användare][202] 

1. Klicka på knappen **Lägg till**. Välj sedan **Användare och grupper** i dialogrutan Lägg till **tilldelning.**

    ![Tilldela användare][203]

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare.

1. Klicka på **Knappen Välj** i dialogrutan Användare **och grupper.**

1. Klicka på **Knappen Tilldela** i dialogrutan Lägg **till tilldelning.**
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Printix på Åtkomstpanelen bör du automatiskt logga in på printix-programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

