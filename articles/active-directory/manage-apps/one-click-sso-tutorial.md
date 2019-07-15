---
title: Ett klick, enkel inloggning (SSO) konfigurationen av ditt Azure Marketplace-program | Microsoft Docs
description: Stegen för en enda klickning konfigurationen för enkel inloggning för ditt program från Azure Marketplace.
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
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872432"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfiguration av ett klick för enkel inloggning

 I den här självstudien får du lära dig hur du utför ett klick, enkel inloggning (SSO) konfiguration för SAML-stöd, Azure Active Directory (Azure AD)-program från Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introduktion till en enkel inloggning

Ett klick SSO-funktionen har utformats för att konfigurera enkel inloggning för Azure Marketplace-appar som stöder SAML-protokoll. På konfigurationssidan för enkel inloggning för Azure AD kan det här alternativet du automatiskt konfigurera Azure AD-metadata på programsidan. På så vis kan du snabbt konfigurera enkel inloggning med minimal manuellt arbete.

## <a name="advantages-of-one-click-sso"></a>Fördelarna med ett klick SSO

- Snabb SSO-konfiguration av Azure Marketplace-program som kräver manuell installation på programsidan.
- Mer effektiv och korrekt konfiguration för enkel inloggning.
- Ingen partner kommunikation eller stöd för installationsprogrammet. Programmet tillhandahåller Användargränssnittet för SAML-konfiguration.

## <a name="prerequisites"></a>Förutsättningar

- En aktiv prenumeration av programmet för att konfigurera med enkel inloggning. Du måste också administratörsautentiseringsuppgifter.
- Den **Mina appar skyddat inloggningstillägg** från Microsoft som installerats i webbläsaren. Mer information finns i [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Konfigurationsstegen för en enkel inloggning

1. Lägg till programmet från Azure Marketplace.

2. Välj **enkel inloggning**.

3. Välj **aktivera enkel inloggning**.

4. Fyll i obligatoriska konfigurationsvärdena i den **SAML grundkonfiguration** avsnittet.

    > [!NOTE]
    > Om programmet har anpassade anspråk som du måste konfigurera kan du hantera dem innan du utför en enkel inloggning.

5. Om ett klick SSO-funktionen är tillgänglig för ditt Azure Marketplace-program, se följande skärm. Du kan behöva installera det **Mina appar skyddat inloggning webbläsartillägget** genom att välja **installera tillägget**.

   ![Installera Mina appar skyddat inloggning webbläsartillägg](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. När du lägger till tillägget till webbläsaren, väljer **installationsprogrammet \<programnamn\>** . När du är omdirigeras till administrationsportalen för programmet, kan du logga in som administratör.

   ![Konfigurera programnamn](./media/one-click-sso-tutorial/setup-sso.png)

7. Webbläsartillägget konfigurerar automatiskt SSO på programmet. Bekräfta genom att välja **Ja**.

   ![Sparar data fylls](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Om konfigurationen för enkel inloggning för ditt program kräver ytterligare steg, följa anvisningarna för att utföra stegen.

8. När konfigurationen är klar kan du välja **OK** att spara ändringarna.

   ![Spara data fylls](./media/one-click-sso-tutorial/save-data.png)

9. Det visas ett bekräftelsefönster så att du vet att SSO-inställningar har angetts.

   ![Enkel inloggning konfigurerad](./media/one-click-sso-tutorial/sso-configured.png)

10. När konfigurationen är klar kan du loggat ut från programmet och tillbaka till Azure-portalen.

11. Du kan välja **testa** att testa enkel inloggning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Vad är webbläsartillägget Mina appar skyddat logga in?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
