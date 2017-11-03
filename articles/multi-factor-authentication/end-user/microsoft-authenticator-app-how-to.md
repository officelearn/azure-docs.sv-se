---
title: "Microsoft Authenticator-appen för mobila enheter | Microsoft Docs"
description: "Lär dig hur du uppgraderar till den senaste versionen av Azure Authenticator."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: cae11f53df768daf5846ac719a45715709326d76
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Kom igång med Microsoft Authenticator-appen
Ger en extra nivå av säkerhet i arbets-eller skolkonto för Microsoft Authenticator-appen (till exempel bsimon@contoso.com) eller Microsoft-konto (till exempel bsimon@outlook.com).

Appen inte fungerar i ett av två sätt:

* **Meddelande**. Appen kan du förhindra obehörig åtkomst till konton och stoppa olagliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Bara visa meddelandet och om det är tillförlitligt, Välj **Kontrollera**. Välj annars **neka**.
* **Verifieringskoden**. Appen kan användas som en programvarutoken för att generera en OAuth Verifieringskod. När du har angett ditt användarnamn och lösenord koden du från appen till skärmen för inloggning. Verifieringskoden innehåller andra formen av autentisering.

Microsoft Authenticator-appen ersätter Azure Authenticator-appen. Azure Authenticator-appen fungerar fortfarande, men om du vill flytta till den nya Microsoft Authenticator-appen i den här artikeln kan hjälpa dig.  

## <a name="opt-in-for-two-step-verification"></a>Välja tvåstegsverifiering

Microsoft Authenticator-appen fungerar inte med sig själv. Konfigurera var och en av dina konton för att begära en andra verifieringsmetod när du loggar in med ditt användarnamn och lösenord.

För ett konto för arbetet eller skolan dig inte vanligtvis att välja den här funktionen för dig själv. I stället en administratör väljer i den för din räkning och meddelar dig att registrera verifieringsmetoderna för ditt konto. Om det här scenariot gäller för dig, Läs mer i [vad Azure Multi-Factor Authentication innebär för mig](multi-factor-authentication-end-user.md).

Du måste ställa in tvåstegsverifiering själv för ett personligt konto. Om du har ett Microsoft-konto kan de här stegen finns i [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Du kan också använda Microsoft Authenticator med icke-Microsoft konton. De kan anropa funktionen något annat än tvåstegsverifiering, men du bör kunna hitta under inloggning inställningar eller säkerhet.

## <a name="install-the-app"></a>Installera appen
Microsoft Authenticator-appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Lägga till konton till appen
Använd någon av följande procedurer för varje konto som du vill lägga till Microsoft Authenticator-appen:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Lägg till ett personligt microsoftkonto i appen

För ett personligt microsoftkonto (en som används för att logga in på Outlook.com, Xbox, Skype, etc.), är du behöver logga in på ditt konto i Microsoft Authenticator-appen.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Lägg till ett arbets- eller skolkonto konto i appen med skanner för QR-kod
1. Gå till skärmen för säkerhetsinställningar verifiering.  Mer information om hur du kommer till den här skärmen finns [ändra säkerhetsinställningarna](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Markera kryssrutan bredvid **autentiseringsapp** Välj **konfigurera**.

    ![Knappen Konfigurera på skärmen för säkerhetsinställningar verifiering](./media/authenticator-app-how-to/azureauthe.png)

    Nu visas en skärm med en QR-kod på den.

    ![Skärmen som ger QR-kod](./media/authenticator-app-how-to/barcode2.png)
3. Öppna Microsoft Authenticator-appen. På den **konton** väljer  **+** , och sedan ange att du vill lägga till ett konto för arbetet eller skolan.
4. Använd kameran för att skanna QR-koden och välj sedan **klar** att stänga skärmen QR-kod.

    Om kameran inte fungerar kan du [ange QR-koden och Webbadressen manuellt](#add-an-account-to-the-app-manually).

5. När appen visas namnet på ditt konto med en sexsiffrig kod under den är du klar.

    ![Konton skärmen](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Lägga till ett konto i appen manuellt
1. Gå till skärmen för säkerhetsinställningar verifiering.  Mer information om hur du kommer till den här skärmen finns [ändra säkerhetsinställningarna](multi-factor-authentication-end-user-manage-settings.md).
2. Välj **konfigurera**.

    ![Knappen Konfigurera på skärmen för säkerhetsinställningar verifiering](./media/authenticator-app-how-to/azureauthe.png)

    Nu visas en skärm med en QR-kod på den.  Observera att koden och Webbadressen.

    ![Skärmen som ger en QR-kod och URL](./media/authenticator-app-how-to/barcode2.png)
3. Öppna Microsoft Authenticator-appen. På den **konton** väljer  **+** , och sedan ange att du vill lägga till ett konto för arbetet eller skolan.

4. Välj i skannern **ange koden manuellt**.

    ![Skärmen för att skanna QR-kod](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Ange koden och URL-Adressen i motsvarande rutor i appen och välj sedan **Slutför**.

    ![Skärmen för att ange koden och Webbadressen](./media/authenticator-app-how-to/manual.png)

6. När appen visas namnet på ditt konto med en sexsiffrig kod under den är du klar.

    ![Konton skärmen](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Lägg till ett konto i appen med din enhet fingeravtryck eller ansiktsigenkänning funktioner
Din organisation kan kräva en PIN-kod att slutföra verifieringen utmaning. Microsoft Authenticator-appen kan använda din enhet fingeravtryck eller ansiktsigenkänning funktioner i stället för en PIN-kod. Om du vill konfigurera detta på första verifieringen i appen visas ett alternativ för att använda Touch ID (iOS) eller fingeravtryck identifiering i stället. 

Om du vill konfigurera Touch ID för Microsoft Authenticator som du behöver utföra en normal verifiering utmaning med en PIN-kod. Microsoft Authenticator in automatiskt den för enheter som stöder Touch ID. 

![Verifiering av installationsprogrammet för Touch ID](./media/authenticator-app-how-to/touchid1.png)

Peka framåt från som, när du måste verifiera din inloggning, väljer mottagna push-meddelanden och söka igenom ditt fingeravtryck istället för att ange din PIN-kod.

![Push-meddelanden](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Använda appen när du loggar in

När ditt konto har lagts till i appen, uppmanas du att göra en test-verifiering för att kontrollera att allt är korrekt konfigurerad. När är du klar! Du behöver inte göra något annat förrän nästa gång du loggar in.

Om du väljer att använda verifieringskoder i appen du vill visa dem på startsidan. De kan ändra var 30: e sekund så att du alltid har en ny kod när du behöver en. Men du behöver inte göra något med dem förrän du logga in och uppmanas att ange en Verifieringskod.  
