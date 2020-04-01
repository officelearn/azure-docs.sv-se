---
title: SSO-konfiguration (Single-Click, Single Sign-on) för ditt Azure Marketplace-program | Microsoft-dokument
description: Steg för konfiguration med ett klick för SSO för ditt program från Azure Marketplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67872432"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Appkonfiguration med ett klick för enkel inloggning

 I den här självstudien får du lära dig hur du utför en klick, enkel inloggning (SSO) konfiguration för SAML-stöd, Azure Active Directory (Azure AD) program från Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introduktion till SSO med ett klick

SSO-funktionen med ett klick är utformad för att konfigurera enkel inloggning för Azure Marketplace-appar som stöder SAML-protokoll. På konfigurationssidan för Azure AD SSO kan du med det här alternativet automatiskt konfigurera Azure AD-metadata på programsidan. På så sätt kan du snabbt ställa in SSO med minimal manuell ansträngning.

## <a name="advantages-of-one-click-sso"></a>Fördelar med ett klick SSO

- Snabb SSO-konfiguration av Azure Marketplace-program som kräver manuell installation på programsidan.
- Effektivare och mer exakt SSO-konfiguration.
- Ingen partnerkommunikation eller support behövs för installationen. Programmet tillhandahåller användargränssnittet för SAML-konfiguration.

## <a name="prerequisites"></a>Krav

- En aktiv prenumeration på programmet för att konfigurera med SSO. Du behöver också administratörsautentiseringsuppgifter.
- Tillägget **Säker inloggning för Mina appar** från Microsoft som är installerat i webbläsaren. Mer information finns i [Komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Steg med SSO-konfiguration med ett klick

1. Lägg till programmet från Azure Marketplace.

2. Välj **Enkel inloggning**.

3. Välj **Aktivera enkel inloggning**.

4. Fyll i de obligatoriska konfigurationsvärdena i avsnittet **Grundläggande SAML-konfiguration.**

    > [!NOTE]
    > Om programmet har anpassade anspråk som du behöver konfigurera kan du hantera dem innan du utför SSO med ett klick.

5. Om SSO-funktionen med ett klick är tillgänglig för ditt Azure Marketplace-program visas följande skärm. Du kanske måste installera **webbläsartillägget För säker** inloggning för mina appar genom att välja **Installera tillägget**.

   ![Installera webbläsartillägg för säker inloggning för mina appar](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. När du har lagt till tillägget i webbläsaren väljer du ** \<Ange programnamn\>**. När du omdirigeras till programadministratörsportalen loggar du in som administratör.

   ![Namn på installationsprogram](./media/one-click-sso-tutorial/setup-sso.png)

7. Webbläsartillägget konfigurerar automatiskt SSO i programmet. Bekräfta genom att välja **Ja**.

   ![Spara data som fylls i automatiskt](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Om SSO-konfigurationen för ditt program kräver ytterligare steg följer du anvisningarna för att utföra stegen.

8. När konfigurationen är klar väljer du **OK** för att spara ändringarna.

   ![Spara data som fylls i automatiskt](./media/one-click-sso-tutorial/save-data.png)

9. Ett bekräftelsefönster visas så att du vet att SSO-inställningarna har konfigurerats.

   ![SSO konfigurerad](./media/one-click-sso-tutorial/sso-configured.png)

10. När konfigurationen har slutförts loggas du ut från programmet och returneras till Azure-portalen.

11. Du kan välja **Testa** för att testa enkel inloggning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Vad är webbläsartillägget För säker inloggning för mina appar?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
