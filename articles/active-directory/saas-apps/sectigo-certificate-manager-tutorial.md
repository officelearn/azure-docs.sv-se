---
title: 'Självstudiekurs: Azure Active Directory-integrering med Sectigo-certifikathanteraren | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67588236"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Självstudiekurs: Azure Active Directory-integrering med Sectigo-certifikathanteraren

I den här självstudien får du lära dig hur du integrerar Sectigo-certifikathanteraren med Azure Active Directory (Azure AD).

Genom att integrera Sectigo Certificate Manager med Azure AD får du följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till Sectigo Certificate Manager.
* Användare kan automatiskt loggas in på Sectigo Certificate Manager med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns [i Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Sectigo Certificate Manager behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Sectigo Certificate Manager-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du azure AD-enkel inloggning i en testmiljö och integrerar Sectigo-certifikathanteraren med Azure AD.

Sectigo Certificate Manager stöder följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Lägga till Sectigo-certifikathanteraren i Azure-portalen

Om du vill integrera Sectigo-certifikathanteraren med Azure AD måste du lägga till Sectigo-certifikathanteraren i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj Azure Active **Directory**på den vänstra menyn .

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program väljer du **Nytt program**.

    ![Alternativet Nytt program](common/add-new-app.png)

1. I sökrutan anger du **Sectigo Certificate Manager**. I sökresultaten väljer du **Sectigo Certificate Manager**och väljer sedan **Lägg till**.

    ![Sectigo Certifikathanterare i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Sectigo Certificate Manager baserat på en testanvändare som heter **Britta Simon**. För enkel inloggning till jobbet måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i Sectigo Certificate Manager.

Om du vill konfigurera och testa azure AD-enkel inloggning med Sectigo Certificate Manager måste du slutföra följande byggblock:

| Aktivitet | Beskrivning |
| --- | --- |
| **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera enkel inloggning för Sectigo-certifikathanteraren](#configure-sectigo-certificate-manager-single-sign-on)** | Konfigurerar de enskilda inloggningsinställningarna i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Testar Azure AD enkel inloggning för en användare som heter Britta Simon. |
| **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda azure AD enkel inloggning. |
| **[Skapa en Sectigo Certificate Manager-testanvändare](#create-a-sectigo-certificate-manager-test-user)** | Skapar en motsvarighet till Britta Simon i Sectigo Certificate Manager som är länkad till Azure AD-representationen för användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Verifierar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet konfigurerar du Azure AD enkel inloggning med Sectigo Certificate Manager i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**i programintegreringsfönstret **sectigocertifikathanteraren** .

    ![Konfigurera alternativet enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML-** eller **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret Konfigurera enkel inloggning med SAML väljer du **Redigera** (pennikonen) för att öppna fönstret **Grundläggande SAML-konfiguration.** **Set up Single Sign-On with SAML**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **Grundläggande SAML-konfiguration,** för att konfigurera *IDP-initierat läge,* utför du följande steg:

    1. Ange en av följande webbadresser i rutan **Identifierare:**
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Ange en av följande webbadresser i rutan **Svara URL:**
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Välj **Ange ytterligare webbadresser**.

    1. Ange en av följande webbadresser i rutan **Relätillstånd:**
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Certificate Manager domän och webbadresser enkel inloggningsinformation](common/idp-relay.png)

1.  Så här konfigurerar du programmet i *SP-initierat läge:*

    * Ange en av följande webbadresser i rutan **Logga in på WEBBADRESS:**
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Certificate Manager domän och webbadresser enkel inloggningsinformation](common/both-signonurl.png)

1. Välj **Hämta** bredvid Certifikat (Base64) i avsnittet **SAML-signeringscertifikat** i fönstret **Konfigurera enkel inloggning med SAML-signeringscertifikat** . **Certificate (Base64)** Välj ett nedladdningsalternativ baserat på dina behov. Spara certifikatet på datorn.

    ![Hämtat certifikat (Base64)](common/certificatebase64.png)

1. Kopiera följande webbadresser i avsnittet **Konfigurera Sectigo-certifikathanterare** baserat på dina krav:

    * Inloggnings-URL
    * Azure AD-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurera enkel inloggning för Sectigo-certifikathanteraren

Om du vill konfigurera enkel inloggning på Sectigo-certifikathanteraren skickar du den nedladdade certifikatfilen (Base64) och relevanta URL:er som du kopierade från Azure-portalen till [Sectigo Certificate Manager-supportteamet](https://sectigo.com/support). Sectigo Certificate Manager-supportteamet använder den information du skickar dem för att säkerställa att SAML-anslutningen för enkel inloggning är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

    ![Alternativen Användare och alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet Ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. Ange **User name** **\@\<brittasimon-din-företagsdomän> i rutan Användarnamn.\< förlängning\>**. Till **exempel,\@brittasimon contoso.com**.

    1. Markera kryssrutan **Visa lösenord.** Skriv ned värdet som visas i rutan **Lösenord.**

    1. Välj **Skapa**.

    ![Användarfönstret](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ger du Britta Simon åtkomst till Sectigo Certificate Manager så att hon kan använda Azure single sign-on.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** > **Sectigo Certificate Manager**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **Sectigo Certificate Manager i**programlistan .

    ![Sectigo Certifikathanterare i programlistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **Användare och grupper** väljer du **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du den relevanta rollen för användaren i listan i fönstret **Välj roll.** Välj **Välj**.

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Skapa en Sectigo Certificate Manager-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Sectigo Certificate Manager. Arbeta med [Sectigo Certificate Manager-supportteamet](https://sectigo.com/support) för att lägga till användaren i Sectigo Certificate Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du har konfigurerat enkel inloggning loggas du in på **Sectigo Certificate Manager** i portalen Mina appar automatiskt i Sectigo Certificate Manager. Mer information om portalen Mina appar finns [i Komma åt och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill veta mer:

- [Lista över självstudier för integrering av SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


