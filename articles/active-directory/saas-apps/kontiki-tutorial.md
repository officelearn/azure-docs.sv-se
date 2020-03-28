---
title: 'Självstudiekurs: Azure Active Directory-integrering med Kontiki | Microsoft-dokument'
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
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098494"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Självstudiekurs: Azure Active Directory-integrering med Kontiki

I den här självstudien får du lära dig hur du integrerar Kontiki med Azure Active Directory (Azure AD).

Genom att integrera Kontiki med Azure AD får du följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till Kontiki.
* Användare kan automatiskt loggas in på Kontiki med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns [i Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Kontiki behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En Kontiki-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du azure AD-enkel inloggning i en testmiljö och integrerar Kontiki med Azure AD.

Kontiki stöder följande funktioner:

* **SP-initierad enkel inloggning**
* **just-in-time-användaretablering**

## <a name="add-kontiki-in-the-azure-portal"></a>Lägg till Kontiki i Azure-portalen

Om du vill integrera Kontiki med Azure AD måste du lägga till Kontiki i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj Azure Active **Directory**på den vänstra menyn .

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program väljer du **Nytt program**.

    ![Alternativet Nytt program](common/add-new-app.png)

1. I sökrutan anger du **Kontiki**. I sökresultaten väljer du **Kontiki**och väljer sedan **Lägg till**.

    ![Kontiki i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Kontiki baserat på en testanvändare som heter **Britta Simon**. För enkel inloggning till jobbet måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i Kontiki.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Kontiki måste du slutföra följande byggblock:

| Aktivitet | Beskrivning |
| --- | --- |
| **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera Kontiki enkel inloggning](#configure-kontiki-single-sign-on)** | Konfigurerar de enskilda inloggningsinställningarna i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Testar Azure AD enkel inloggning för en användare som heter Britta Simon. |
| **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda azure AD enkel inloggning. |
| **[Skapa en Kontiki-testanvändare](#create-a-kontiki-test-user)** | Skapar en motsvarighet till Britta Simon i Kontiki som är länkad till Azure AD-representationen av användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Verifierar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet konfigurerar du azure AD enkel inloggning med Kontiki i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning i fönstret **Kontiki-programintegration** . **Single sign-on**

    ![Konfigurera alternativet enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML-** eller **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret Konfigurera enkel inloggning med SAML väljer du **Redigera** (pennikonen) för att öppna fönstret **Grundläggande SAML-konfiguration.** **Set up Single Sign-On with SAML**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange en URL med följande mönster i textrutan **Sign on URL** i fönstret Grundläggande **SAML-konfiguration:**`https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki-domän och webbadresser med enkel inloggning](common/sp-signonurl.png)

    > [!NOTE]
    > Kontakta [Kontiki Client supportteam](https://customersupport.kontiki.com/enterprise/contactsupport.html) för att få rätt värde att använda. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Välj **Hämta bredvid** XML för **federationsmetadata**i fönstret Konfigurera enkel inloggning med SAML med SAML-signeringscertifikat . **Set up Single Sign-On with SAML** **SAML Signing Certificate** Välj ett nedladdningsalternativ baserat på dina behov. Spara certifikatet på datorn.

    ![Alternativet Hämtning av XML-certifikat för federationsmetadata](common/metadataxml.png)

1. Kopiera följande webbadresser i avsnittet **Konfigurera Kontiki** baserat på dina krav:

    * Inloggnings-URL
    * Azure AD-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurera Kontiki enkel inloggning

Om du vill konfigurera enkel inloggning på Kontiki-sidan skickar du den nedladdade XML-filen för federationsmetadata och relevanta URL:er som du kopierade från Azure-portalen till [Kontiki-supportteamet](https://customersupport.kontiki.com/enterprise/contactsupport.html). Kontiki-supportteamet använder den information du skickar dem för att säkerställa att SAML-anslutningen för enkel inloggning är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

    ![Alternativen Användare och alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet Ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. Ange **User name** **\@\<brittasimon-din-företagsdomän> i rutan Användarnamn.\< förlängning>**. Till **exempel,\@brittasimon contoso.com**.

    1. Markera kryssrutan **Visa lösenord.** Skriv ned värdet som visas i rutan **Lösenord.**

    1. Välj **Skapa**.

    ![Användarfönstret](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ger du Britta Simon åtkomst till Kontiki så att hon kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** > **Kontiki**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **Kontiki**i programlistan .

    ![Kontiki i ansökningslistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **Användare och grupper** väljer du **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du den relevanta rollen för användaren i listan i fönstret **Välj roll.** Välj **Välj**.

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-a-kontiki-test-user"></a>Skapa en Kontiki-testanvändare

Det finns inget åtgärdsobjekt för dig att konfigurera användaretablering i Kontiki. När en tilldelad användare försöker logga in på Kontiki med hjälp av portalen Mina appar kontrollerar Kontiki om användaren finns. Om inget användarkonto hittas skapar Kontiki automatiskt användarkontot.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du har konfigurerat enkel inloggning loggas du in på **Kontiki** i portalen Mina appar automatiskt i Kontiki. Mer information om portalen Mina appar finns [i Komma åt och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill veta mer:

- [Lista över självstudier för integrering av SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
