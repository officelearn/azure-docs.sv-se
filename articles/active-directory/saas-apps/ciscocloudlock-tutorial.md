---
title: 'Självstudier: Azure Active Directory-integrering med den säkerhet Molninfrastrukturresurs | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och det Molninfrastrukturresurs säkerhet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 5ec729c6f82cec503cae2fa057f5842849004ac7
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318253"
---
# <a name="tutorial-azure-active-directory-integration-with-the-cloud-security-fabric"></a>Självstudier: Azure Active Directory-integrering med den säkerhet Molninfrastrukturresurs

I kursen får lära du att integrera den Molninfrastrukturresurs säkerhet med Azure Active Directory (AD Azure).

Integrering av Molninfrastrukturresurs säkerhet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till den Molninfrastrukturresurs säkerhet.
- Du kan aktivera användarna att automatiskt hämta loggat in på den säkerhet Molninfrastrukturresurs (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med den Molninfrastrukturresurs säkerhet, behöver du följande:

- En Azure AD-prenumeration
- En av Molninfrastrukturresurs säkerhet enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till det Molninfrastrukturresurs säkerhet från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Att lägga till det Molninfrastrukturresurs säkerhet från galleriet
Du måste lägga till det Molninfrastrukturresurs säkerhet från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av den säkerhet Molninfrastrukturresurs i Azure AD.

**Utför följande steg för att lägga till det Molninfrastrukturresurs säkerhet från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **av Molninfrastrukturresurs säkerhet**väljer **av Molninfrastrukturresurs säkerhet** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Molninfrastrukturresurs för säkerhet i resultatlistan](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med den säkerhet Molninfrastrukturresurs baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i det Molninfrastrukturresurs säkerhet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i det Molninfrastrukturresurs säkerhet upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med det Molninfrastrukturresurs säkerhet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i Security Molninfrastrukturresurs](#create-a-the-cloud-security-fabric-test-user)**  – du har en motsvarighet för Britta Simon i den säkerhet Molninfrastrukturresurs som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program i molnet säkerhet Fabric.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med det Molninfrastrukturresurs säkerhet:**

1. I Azure-portalen på den **av Molninfrastrukturresurs säkerhet** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

1. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_samlbase.png)

3. På den **URL: er och i molnet Fabric säkerhetsdomän** avsnittet, utför följande steg:

    ![URL: er och molnet Fabric säkerhetsdomän enkel inloggning information](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_url.png)

    a. I den **inloggnings-URL** textruta, ange ett URL-Adressen:
    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > ID-värdet är inte verkliga. Uppdatera värdet med den faktiska identifieraren. Kontakta [Cloud Security Fabric klienten supportteamet](mailto:support@cloudlock.com) värdet hämtas. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/ciscocloudlock-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **av Molninfrastrukturresurs säkerhet** sida, måste du skicka den hämtade **XML-Metadata för** till [av Molninfrastrukturresurs säkerhet supportteamet](mailto:support@cloudlock.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ciscocloudlock-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/ciscocloudlock-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ciscocloudlock-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ciscocloudlock-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-the-cloud-security-fabric-test-user"></a>Skapa en testanvändare i Molninfrastrukturresurs säkerhet

I det här avsnittet kan du skapa en användare som kallas Britta Simon i det Molninfrastrukturresurs säkerhet. Arbeta med [av Molninfrastrukturresurs säkerhet supportteamet](mailto:support@cloudlock.com) att lägga till användare i det moln säkerhet Fabric-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till den Molninfrastrukturresurs säkerhet.

![Tilldela rollen][200]

**Om du vill tilldela det Molninfrastrukturresurs säkerhet Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **av Molninfrastrukturresurs säkerhet**.

    ![Länken i Molninfrastrukturresurs säkerhet i listan med program](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen av Molninfrastrukturresurs säkerhet på åtkomstpanelen du bör få automatiskt loggat in på ditt program i molnet säkerhet Fabric.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ciscocloudlock-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloudlock-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloudlock-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloudlock-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloudlock-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloudlock-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloudlock-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloudlock-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloudlock-tutorial/tutorial_general_203.png
