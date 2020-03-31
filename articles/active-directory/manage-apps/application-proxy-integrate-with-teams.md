---
title: Få tillgång till Azure AD App Proxy-appar i Teams | Microsoft-dokument
description: Använd Azure AD Application Proxy för att komma åt ditt lokala program via Microsoft Teams.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807765"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Få tillgång till lokala program via Microsoft Teams

Azure Active Directory Application Proxy ger dig enkel inloggning till lokala program oavsett var du befinner dig. Microsoft Teams effektiviserar samarbetet på ett och samma ställe. Att integrera de två tillsammans innebär att användarna kan vara produktiva med sina lagkamrater i alla situationer.

Användarna kan lägga till molnappar i sina Teams-kanaler [med hjälp av flikar,](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)men hur är det med SharePoint-webbplatserna eller planeringsverktyget som finns lokalt? Application Proxy är lösningen. De kan lägga till appar som publiceras via Programproxy i sina kanaler med samma externa webbadresser som de alltid använder för att komma åt sina appar på distans. Och eftersom Application Proxy autentiserar via Azure Active Directory får användarna en enda inloggningsupplevelse.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Installera application proxy-anslutningsappen och publicera appen

Om du inte redan har gjort [det konfigurerar du Programproxy för klienten och installerar kopplingen](application-proxy-add-on-premises-application.md). Publicera sedan [ditt lokala program](application-proxy-add-on-premises-application.md) för fjärråtkomst. När du publicerar appen bör du notera den externa webbadressen eftersom den används för att lägga till appen i Teams.

Om du redan har publicerat dina appar men inte kommer ihåg deras externa webbadresser letar du upp dem i [Azure-portalen](https://portal.azure.com). Logga in och navigera sedan till **Azure Active Directory** > **Enterprise-program** > **Alla program** > välja din app > **Programproxy**.

## <a name="add-your-app-to-teams"></a>Lägga till din app i Teams

När du har publicerat appen via Programproxy meddelar du användarna att de kan lägga till den som en flik direkt i sina Teams-kanaler och att appen är tillgänglig för alla i teamet att använda. Låt dem följa dessa tre steg:

1. Navigera till teams-kanalen där du vill **+** lägga till den här appen och välj att lägga till en flik.

   ![Välj + om du vill lägga till en flik i Teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Välj **Webbplats** på flikalternativen.

   ![Välj webbplats på skärmen Lägg till en flik](./media/application-proxy-integrate-with-teams/website.png)

1. Ge fliken ett namn och ange URL:en till den externa url:en för programproxy.

   ![Namnge fliken och lägg till den externa URL:en](./media/application-proxy-integrate-with-teams/tab-name-url.png)

När en medlem i ett team lägger till fliken visas den för alla i kanalen. Alla användare som har åtkomst till appen får enkel inloggning med de autentiseringsuppgifter de använder för Microsoft Teams. Alla användare som inte har åtkomst till appen kan se fliken i Teams, men blockeras tills du ger dem behörighet till den lokala appen och Azure-portalens publicerade version av appen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [publicerar lokala SharePoint-webbplatser](application-proxy-integrate-with-sharepoint-server.md) med Programproxy.
- Konfigurera dina appar så att de använder [anpassade domäner](application-proxy-configure-custom-domain.md) för deras externa WEBBADRESS.
