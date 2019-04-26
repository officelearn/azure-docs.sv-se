---
title: Få åtkomst till Azure AD App Proxy-appar i Teams | Microsoft Docs
description: Använda Azure AD-programproxy för att komma åt ditt lokala program via Microsoft Teams.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 660d05146e82ab85136cac715b56cb1ee89b002f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292968"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Åtkomst till dina lokala program via Microsoft Teams

Azure Active Directory Application Proxy ger du enkel inloggning till lokala program oavsett var du befinner dig. Microsoft Teams förenklar din gemensamma ansträngningar på samma ställe. Integrera två tillsammans innebär att användarna kan vara produktiva med sina medarbetare i alla situationer. 

Användarna kan lägga till appar i molnet till sina Teams kanaler [med hjälp av flikarna](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), men vad händer om SharePoint-webbplatser eller planeringsverktyg som finns lokalt? Programproxyn är lösning. De kan lägga till appar som publicerats via programproxy till sina kanaler med hjälp av samma externa URL: erna använder de alltid få fjärråtkomst till sina appar. Och eftersom Application Proxy som autentiserar via Azure Active Directory, dina användare få en enkel inloggning.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Installera Application Proxy connector och publicera dina appar

Om du inte redan gjort [konfigurera programproxyn för din klient och installera connector](application-proxy-add-on-premises-application.md). Sedan [publicera dina lokala program](application-proxy-add-on-premises-application.md) för fjärråtkomst. När du publicerar appen, anteckna den externa URL: en eftersom den används för att lägga till appen i Teams.

Om du redan ha dina appar som publicerats men inte kommer ihåg sina externa URL: er, söker du efter dem den [Azure-portalen](https://portal.azure.com). Logga in och gå till **Azure Active Directory** > **företagsprogram** > **alla program** > Välj din app >  **Programproxy**.

## <a name="add-your-app-to-teams"></a>Lägga till din app i Teams

När du publicerar appen via programproxy kan låta dina användare veta att de kan lägga till den som en flik direkt i sina Teams kanaler och sedan appen är tillgänglig för alla i teamet att använda. Ha dem följande tre steg:

1. Gå till Teams-kanal där du vill lägga till den här appen och markera **+** att lägga till en flik.

   ![Välj Lägg till en flik](./media/application-proxy-integrate-with-teams/add-tab.png)

2. Välj **webbplats** från Flikalternativ.

   ![Lägga till en webbplats](./media/application-proxy-integrate-with-teams/website.png)

3. Namnge fliken och ange URL: en till Application Proxy-externa URL: en. 

   ![Konfigurera fliknamn och Webbadress](./media/application-proxy-integrate-with-teams/tab-name-url.png)

När en medlem i ett team lägger du till fliken, visas den för alla i kanalen. Alla användare som har åtkomst till appen få enkel inloggning med de autentiseringsuppgifter de använder för Microsoft Teams. Alla användare som inte har åtkomst till appen ser fliken i Teams, men blockeras tills du ge dem behörighet till den lokala appen och Azure portal publicerade versionen av appen. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [publicera lokala SharePoint-webbplatser](application-proxy-integrate-with-sharepoint-server.md) med programproxy.
- Konfigurera dina appar kan använda [anpassade domäner](application-proxy-configure-custom-domain.md) för sina externa URL: en. 
