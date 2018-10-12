---
title: 'Självstudier: Azure Active Directory-integration med FreshDesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 3f640d729d002e89c4968442e8d8ab761f78d090
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115172"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Självstudier: Azure Active Directory-integration med FreshDesk

I den här självstudien får du lära dig hur du integrerar FreshDesk med Azure Active Directory (AD Azure).

Integrera FreshDesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FreshDesk
- Du kan aktivera användarna att automatiskt få loggat in på FreshDesk (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FreshDesk, behöver du följande objekt:

- En Azure AD-prenumeration
- En FreshDesk enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till FreshDesk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-freshdesk-from-the-gallery"></a>Att lägga till FreshDesk från galleriet

För att konfigurera integrering av FreshDesk i Azure AD, som du behöver lägga till FreshDesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FreshDesk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **FreshDesk**. Välj **FreshDesk** i resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med FreshDesk baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i FreshDesk är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i FreshDesk upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **e-postadress** i FreshDesk.

Om du vill konfigurera och testa Azure AD enkel inloggning med FreshDesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FreshDesk](#creating-a-freshdesk-test-user)**  – du har en motsvarighet för Britta Simon i FreshDesk som är länkad till en Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt FreshDesk-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FreshDesk:**

1. I Azure-portalen på den **FreshDesk** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. På den **FreshDesk domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.freshdesk.com` eller ett annat värde som har föreslagits Freshdesk.

    > [!NOTE]
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [FreshDesk klienten supportteamet](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) att hämta det här värdet.

4. Ditt program förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. Standardvärdet för **användaridentifierare** är **user.userprincipalname** men **FreshDesk** förväntar sig detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller Använd rätt attribut-värde baserat på konfigurationen för din organisation.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_attribute.png)

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Om du har problem, finns det [länk](https://support.freshdesk.com/support/discussions/topics/317543).

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_general_400.png)

7. Installera **OpenSSL** i systemet om du inte har installerat i systemet.

8. Öppna **kommandotolk** och kör följande kommandon:

    a. Ange `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt` värdet i Kommandotolken.

    > [!NOTE]
    > Här **FreshDesk.cer** är det certifikat som du har hämtat från Azure-portalen.

    b. Ange `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt` värdet i Kommandotolken. 
    
    > [!NOTE]
    > Här **certificate.crt** är utdata-certifikat som har genererats i föregående steg.

    c. Kopiera den **tumavtryck** värde och klistra in den i anteckningar. Ta bort kolon från tumavtryck och hämta det slutliga värdet för tumavtryck.

9. På den **FreshDesk Configuration** klickar du på **konfigurera FreshDesk** öppna Konfigurera inloggnings-fönstret. Kopiera SAML enkel inloggning för tjänstens URL och URL: en för utloggning från den **Snabbreferens** avsnittet.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

10. Logga in på webbplatsen Freshdesk företag som en administratör i ett annat webbläsarfönster.

11. Välj den **inställningsikonen** och i den **Security** avsnittet, utför följande steg:

    ![Enkel inloggning](./media/freshdesk-tutorial/IC776770.png "enkel inloggning")
  
    a. För **enkel inloggning (SSO)** väljer **på**.

    b. Välj **SAML SSO**.

    c. I den **inloggnings-URL för SAML** textrutan klistra in **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen.

    d. I den **URL för utloggning** textrutan klistra in **URL: en för utloggning** värde, som du har kopierat från Azure-portalen.

    e. I den **Security certifikat fingeravtryck** textrutan klistra in **tumavtryck** värde som du har fått tidigare när du tar bort kolon.
  
    f. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-freshdesk-test-user"></a>Skapa en FreshDesk testanvändare

För att aktivera Azure AD-användare att logga in på FreshDesk, måste de etableras i FreshDesk.  
När det gäller FreshDesk är etablering en manuell aktivitet.

**Utför följande steg för att tillhandahålla en användarkonton:**

1. Logga in på din **Freshdesk** klient.

2. Klicka på menyn längst upp **Admin**.

   ![Administratören](./media/freshdesk-tutorial/IC776772.png "Admin")

3. I den **allmänna inställningar** fliken **agenter**.
  
   ![Agenter](./media/freshdesk-tutorial/IC776773.png "agenter")

4. Klicka på **ny Agent**.

    ![Ny Agent](./media/freshdesk-tutorial/IC776774.png "ny Agent")

5. I dialogrutan agentinformation utför du följande steg:

   ![Agentinformation](./media/freshdesk-tutorial/IC776775.png "agentinformation")

   a. I den **e-post** textrutan Ange Azure AD-e-postadress för Azure AD-konto som du vill etablera.

   b. I den **fullständigt namn** textrutan skriver du namnet på Azure AD-konto som du vill etablera.

   c. I den **rubrik** textrutan skriver du namnet på Azure AD-konto som du vill etablera.

   d. Klicka på **Spara**.

    >[!NOTE]
    >Azure AD-kontoinnehavare får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det aktiveras.
    >
    >[!NOTE]
    >Du kan använda alla andra Freshdesk användare konto verktyg för att skapa eller API: er som tillhandahålls av Freshdesk att etablera AAD-användarkonton till FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst till Box.

![Tilldela användare][200]

**Om du vill tilldela Britta Simon FreshDesk, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **FreshDesk**.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen FreshDesk i åtkomstpanelen, bör du få inloggningssida för att hämta loggat in på ditt FreshDesk-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png