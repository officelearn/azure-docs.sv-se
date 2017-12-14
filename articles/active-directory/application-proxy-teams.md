---
title: "Åtkomst till Azure AD App Proxy appar i team | Microsoft Docs"
description: "Använda Azure AD Application Proxy åtkomst till lokala program via Microsoft-Teams."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 57ae4c38a528251dee89d56f6fdb7cf1d1dc922d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Åtkomst till lokala program via Microsoft-Teams

Azure Active Directory Application Proxy ger enkel inloggning till lokala program oavsett var du befinner dig. Microsoft-Teams förenklar samverkande arbetet på ett ställe. Integrering av två innebär att användarna kan vara produktiva med sina medarbetare i en situation. 

Användarna kan lägga till molnappar till deras team kanaler [med hjälp av flikarna](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), men vad händer om SharePoint-webbplatser eller planering verktyg som finns lokalt? Application Proxy är lösningen. De kan lägga till appar som publiceras via Application Proxy till deras kanaler som använder samma externa URL: er använder de alltid åtkomst till sina program via fjärranslutning. Och eftersom Application Proxy som autentiserar via Azure Active Directory, får användarna en enkel inloggning.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Installera Application Proxy connector och publicera en app

Om du inte redan har gjort [konfigurera programproxyn för din klient och installera connector](active-directory-application-proxy-enable.md). Sedan [publicera programmet lokalt](application-proxy-publish-azure-portal.md) för fjärråtkomst. När du publicerar appen, anteckna den externa URL eftersom den används för att lägga till appen i team.

Om du redan har dina appar som publiceras men inte kommer ihåg sina externa URL: er, söker du efter dem den [Azure-portalen](https://portal.azure.com). Logga in och sedan gå till **Azure Active Directory** > **företagsprogram** > **alla program** > Välj appen > **programproxy**.

## <a name="add-your-app-to-teams"></a>Lägg till din app i team

När du publicerar appen via Application Proxy kan du låta dina användare veta att de kan lägga till den som en flik direkt i deras team kanaler och sedan appen är tillgänglig för alla i gruppen kan använda. Ha dem följande tre steg:

1. Navigera till team kanalen där du vill lägga till den här appen och markera  **+**  att lägga till en flik.

   ![Välj Lägg till en flik](./media/application-proxy-teams/add-tab.png)

2. Välj **webbplats** från Flikalternativ.

   ![Lägga till en webbplats](./media/application-proxy-teams/website.png)

3. Namnge fliken och ange URL-Adressen till Application Proxy externa URL: en. 

   ![Konfigurera fliknamn och en URL](./media/application-proxy-teams/tab-name-url.png)

När en medlem i ett team lägger du till fliken, visas den för alla i kanalen. Alla användare som har åtkomst till appen hämta enkel inloggning med de autentiseringsuppgifter de använder för Microsoft Teams. Alla användare som inte har åtkomst till appen visas på fliken i team, men är spärrat tills du ge dem behörighet till appen lokalt och Azure portal publicerade versionen av appen. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [publicera lokala SharePoint-webbplatser](application-proxy-enable-remote-access-sharepoint.md) med Application Proxy.
- Konfigurera dina appar att använda [anpassade domäner](active-directory-application-proxy-custom-domains.md) för sina externa URL: en. 
