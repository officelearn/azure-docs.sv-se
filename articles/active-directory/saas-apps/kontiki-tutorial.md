---
title: 'Självstudier: Azure Active Directory-integrering med Kontiki | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee7454942b9214eeb1253339446df370e20fe01
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785841"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Självstudier: Azure Active Directory-integrering med Kontiki

I den här självstudien får du lära dig hur du integrerar Kontiki med Azure Active Directory (AD Azure).

Integrera Kontiki med Azure AD ger dig följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Kontiki.
* Användare kan vara loggas in automatiskt till Kontiki med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Kontiki, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En Kontiki prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du och testa Azure AD enkel inloggning i en testmiljö och integrera Kontiki med Azure AD.

Kontiki har stöd för följande funktioner:

* **SP-initierad enkel inloggning**
* **just-in-time-användaretablering**

## <a name="add-kontiki-in-the-azure-portal"></a>Lägg till Kontiki i Azure portal

Om du vill integrera Kontiki med Azure AD, måste du lägga till Kontiki i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn väljer du **Azure Active Directory**.

    ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program, Välj **nytt program**.

    ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **Kontiki**. I sökresultaten väljer **Kontiki**, och välj sedan **Lägg till**.

    ![Kontiki i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Kontiki baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i Kontiki.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kontiki, måste du utföra följande byggblock:

| Uppgift | Beskrivning |
| --- | --- |
| **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera Kontiki enkel inloggning](#configure-kontiki-single-sign-on)** | Konfigurerar inställningar för enkel inloggning i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Med namnet Britta Simon tester Azure AD enkel inloggning för en användare. |
| **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda Azure AD enkel inloggning. |
| **[Skapa en Kontiki testanvändare](#create-a-kontiki-test-user)** | Skapar en motsvarighet för Britta Simon i Kontiki som är länkad till en Azure AD-representation av användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Kontrollerar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet konfigurerar du Azure AD enkel inloggning med Kontiki i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)i den **Kontiki** application integration väljer **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I den **väljer du en metod för enkel inloggning** fönstret Välj **SAML** eller **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I den **SAML grundkonfiguration** fönstret i den **inloggnings-URL** text, ange en URL som har följande mönster: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    > [!NOTE]
    > Kontakta den [Kontiki klienten supportteamet](https://customersupport.kontiki.com/enterprise/contactsupport.html) att få rätt värdet som ska användas. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I den **ange in enkel inloggning med SAML** fönstret i den **SAML-signeringscertifikat** väljer **hämta** bredvid **Federation Metadata XML**. Markera ett nedladdningsalternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtningsalternativ för Federation Metadata-XML-certifikat](common/metadataxml.png)

1. I den **konfigurera Kontiki** avsnittet, kopiera följande URL: er utifrån dina behov:

    * Inloggningswebbadress
    * Microsoft Azure Active Directory-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurera Kontiki enkel inloggning

Om du vill konfigurera enkel inloggning på Kontiki sida, skicka den hämta Federation Metadata-XML-filen och relevanta URL: er som du kopierade från Azure portal för att den [Kontiki supportteamet](https://customersupport.kontiki.com/enterprise/contactsupport.html). Supportteamet Kontiki använder informationen som du skickar dem till att kontrollera att SAML enkel inloggning för anslutningen är korrekt inställd på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användarna och alternativ för alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **brittasimon\@\<din företagsdomän >.\< tillägget >**. Till exempel **brittasimon\@contoso.com**.

    1. Välj den **Show lösenord** markerar du kryssrutan. Skriv ned värdet som visas i den **lösenord** box.

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska ge du Britta Simon åtkomst till Kontiki så att hon kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **Kontiki**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan med program väljer **Kontiki**.

    ![Kontiki i programlistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** fönstret Välj rollen relevant för användaren i listan. Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-a-kontiki-test-user"></a>Skapa en Kontiki testanvändare

Det finns inga uppgift att konfigurera användaretablering i Kontiki. När en tilldelad användare försöker logga in på Kontiki med hjälp av Mina appar-portalen kontrollerar Kontiki om användaren finns. Om inget konto hittas, skapas Kontiki automatiskt användarkontot.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av Mina appar-portalen.

När du har konfigurerat enkel inloggning, när du väljer **Kontiki** i portalen Mina appar loggas du automatiskt till Kontiki. Läs mer om portalen Mina appar [öppna och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer kan du granska dessa artiklar:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
