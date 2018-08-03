---
title: 'Självstudier: Azure Active Directory-integrering med | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: f5aa724e9848c9794eef093aef15b0aaed9cae97
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435768"
---
# <a name="integrate-azure-active-directory-with-box"></a>Integrera Azure Active Directory med

I de här självstudierna lär du dig hur du integrerar Azure Active Directory (Azure AD) med rutan.

Genom att integrera Azure AD med rutan kan få du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Box.
- Du kan aktivera dina användare få loggat in automatiskt till Box (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Läs mer om integrering av SaaS-app med Azure AD, i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med, behöver du följande objekt:

- En Azure AD-prenumeration
- En ruta SSO-aktiverad prenumeration

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du gör *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Box från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-box-from-the-gallery"></a>Lägg till Box från galleriet
Om du vill konfigurera integreringen av Azure AD med Lägg till Box från galleriet i din lista över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

1. Välj **företagsprogram** > **alla program**.

    ![Fönstret ”program”][2]
    
1. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![”Det nya programmet” knappen][3]

1. I sökrutan skriver **Box**väljer **Box** i resultatlistan och välj sedan **Lägg till**.

    ![Rutan i resultatlistan](./media/box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med rutan baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD att identifiera användaren Box och dess motsvarighet i Azure AD. Med andra ord etableras en länk förhållandet mellan en Azure AD-användare och samma användare i rutan.

För att upprätta länken relationen, tilldela som rutan *användarnamn* värdet för den *användarnamn* i Azure AD.

Slutför byggblocken i nästa fem avsnitt för att konfigurera och testa Azure AD enkel inloggning med.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Box-program genom att göra följande:

1. I Azure-portalen i den **Box** application integration fönstret **enkel inloggning**.

    ![Länken ”enkel inloggning”][4]

1. I den **enkel inloggning** fönstret i den **läge för enkel inloggning** väljer **SAML-baserad inloggning**.
 
    ![Fönstret ”enkel inloggning”](./media/box-tutorial/tutorial_box_samlbase.png)

1. Under **Box domän och URL: er**, gör du följande:

    ![”Rutan domän och URL: er” enkel inloggning för information](./media/box-tutorial/url3.png)

    a. I den **inloggnings-URL** skriver du en URL i formatet: *https://\<underdomän >. box.com*.

    b. I den **identifierare** textrutan typ **box.net**.
     
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med faktiska inloggnings-URL och identifierare. För att få värdena kan kontakta den [Box klienten supportteamet](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

1. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**, och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/box-tutorial/tutorial_box_certificate.png) 

1. Välj **Spara**.

    ![Konfigurera enkel inloggning spara-knapp](./media/box-tutorial/tutorial_general_400.png)
    
1. Om du vill konfigurera enkel inloggning för ditt program, följer du anvisningarna i [Konfigurera enkel inloggning på din egen](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Om du inte kan aktivera SSO-inställningar för Box-konto, kan du behöva kontakta den [Box klienten supportteamet](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) och ge den hämta XML-filen.

> [!TIP]
> När du konfigurerar appen, du kan läsa en kortare version av föregående anvisningar i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen i den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och öppna den Embedded-dokumentation i den **Configuration** avsnittet längst ned. Mer information om funktionen embedded-dokumentation finns i [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du testanvändare Britta Simon i Azure portal genom att göra följande:

![Skapa en Azure AD-testanvändare][100]

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**.

    ![Azure Active Directory-länk](./media/box-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över aktuella användare, Välj **användare och grupper** > **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/box-tutorial/create_aaduser_02.png)

1. Överst på den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/box-tutorial/create_aaduser_03.png)

    Den **användaren** öppnas.

1. I den **användaren** fönstret gör du följande:

    ![Fönstret användare](./media/box-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-box-test-user"></a>Skapa en testanvändare i Box

I det här avsnittet skapar du testanvändare Britta Simon i Box. Box har stöd för just-in-time-etablering, som är aktiverat som standard. Om en användare inte redan finns, skapas en ny när du försöker få åtkomst till Box. Ingen åtgärd krävs från dig att skapa användaren.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du användaren Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Box. Du gör detta genom att göra följande:

![Tilldela rollen][200]

1. I Azure-portalen öppnar du den **program** visa, gå till den **Directory** visa och välj sedan **företagsprogram** > **alla program**.

    ![”Program” och ”alla program”-länkar][201] 

1. I den **program** väljer **Box**.

    ![Länken](./media/box-tutorial/tutorial_box_app.png)  

1. I den vänstra rutan väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Välj **Lägg till** och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

1. I den **användare och grupper** fönstret i den **användare** väljer **Britta Simon**.

1. Välj den **Välj** knappen.

1. I den **Lägg till tilldelning** väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **Box** panelen i åtkomstpanelen, du öppnar inloggningssidan för att logga in i ditt Box-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om integrering av SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera användaretablering](box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/box-tutorial/tutorial_general_01.png
[2]: ./media/box-tutorial/tutorial_general_02.png
[3]: ./media/box-tutorial/tutorial_general_03.png
[4]: ./media/box-tutorial/tutorial_general_04.png

[100]: ./media/box-tutorial/tutorial_general_100.png

[200]: ./media/box-tutorial/tutorial_general_200.png
[201]: ./media/box-tutorial/tutorial_general_201.png
[202]: ./media/box-tutorial/tutorial_general_202.png
[203]: ./media/box-tutorial/tutorial_general_203.png

