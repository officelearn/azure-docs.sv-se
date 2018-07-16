---
title: Microsoft Authenticator-appen för mobiltelefoner – Azure AD | Microsoft Docs
description: Lär dig hur du uppgraderar till den senaste versionen av Azure Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 8241dcaaf5623a22f4fc485f021766276472fb51
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059825"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Kom igång med Microsoft Authenticator-appen
Ger en extra nivå av säkerhet i arbets-eller skolkonto för Microsoft Authenticator-appen (till exempel bsimon@contoso.com) eller ditt Microsoft-konto (till exempel bsimon@outlook.com).

Appen fungerar i ett av två sätt:

* **Meddelande**. Appen kan hjälpa att förhindra obehörig åtkomst till konton och stoppa olagliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Visa meddelandet, och om det är tillförlitligt, Välj **Kontrollera**. Annars kan du välja **neka**.
* **Verifieringskod**. Appen kan användas som en programvarutoken för att skapa en OAuth-Verifieringskod. När du har angett ditt användarnamn och lösenord, ange den kod som tillhandahålls av appen i inloggning-skärmen. Verifieringskoden ger ett andra formen av autentisering.

Microsoft Authenticator-appen ersätter Azure Authenticator-appen. Azure Authenticator-appen fungerar fortfarande, men om du vill flytta till den nya Microsoft Authenticator-appen i den här artikeln kan hjälpa dig.  

## <a name="opt-in-for-two-step-verification"></a>Välja tvåstegsverifiering

Microsoft Authenticator-appen fungerar inte ensamt. Konfigurera var och en av dina konton för att begära en andra verifieringsmetod när du har loggat in med ditt användarnamn och lösenord.

För ett arbets- eller skolkonto konto få inte du vanligtvis väljer du den här funktionen för dig själv. I stället en administratör väljer i för din räkning och sedan meddelar dig att registrera verifieringsmetoder för ditt konto. Om det här scenariot gäller för dig, Läs mer i [vad Azure Multi-Factor Authentication innebär för mig](multi-factor-authentication-end-user.md).

För ett personligt konto måste du konfigurera tvåstegsverifiering själv. Om du har ett Microsoft-konto kan de här stegen finns i [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Du kan också använda Microsoft Authenticator med icke-Microsoft-konton. De kan anropa funktionen något annat än tvåstegsverifiering, men du ska kunna hitta den under säkerhets- eller logga in inställningar.

## <a name="install-the-app"></a>Installera appen
Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) och [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="add-accounts-to-the-app"></a>Lägga till konton i appen
För varje konto som du vill lägga till i Microsoft Authenticator-appen, använder du någon av följande procedurer:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Lägga till ett personligt microsoftkonto i appen

För ett personligt microsoftkonto (en som används för att logga in på Outlook.com, Xbox, Skype, o.s.v.), allt du behöver göra är att logga in på ditt konto i Microsoft Authenticator-appen.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Lägg till ett arbets- eller skolkonto i appen med QR-kodsskannern
1. Gå till skärmen för säkerhetsinställningar verifiering.  Information om hur du kommer till den här skärmen finns [ändrar säkerhetsinställningarna](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Markera kryssrutan bredvid **autentiseringsapp** därefter **konfigurera**.

    ![Knappen Konfigurera på skärmen för säkerhetsinställningar verifiering](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Gör det öppnas ett fönster där en QR-kod på den.

    ![Skärmen som tillhandahåller QR-kod](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Öppna Microsoft Authenticator-appen. På den **konton** väljer **+**, och sedan ange att du vill lägga till ett arbets- eller skolkonto konto.
4. Använda kameran att skanna QR-koden och välj sedan **klar** att Stäng skärmen QR-kod.

    Om din kamera inte fungerar kan du [ange QR-koden och Webbadressen manuellt](#add-an-account-to-the-app-manually).

5. När appen visar namnet på ditt konto med en sexsiffrig kod under det, är du klar.

    ![skärmen konton](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Lägga till ett konto i appen manuellt
1. Gå till skärmen för säkerhetsinställningar verifiering.  Information om hur du kommer till den här skärmen finns [ändrar säkerhetsinställningarna](multi-factor-authentication-end-user-manage-settings.md).
2. Välj **konfigurera**.

    ![Knappen Konfigurera på skärmen för säkerhetsinställningar verifiering](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Gör det öppnas ett fönster där en QR-kod på den.  Observera att koden och Webbadressen.

    ![Skärmbild som visar QR-kod och URL: en](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Öppna Microsoft Authenticator-appen. På den **konton** väljer **+**, och sedan ange att du vill lägga till ett arbets- eller skolkonto konto.

4. Välj i skannern, **ange koden manuellt**.

    ![Skärmen för att skanna en QR-kod](./media/microsoft-authenticator-app-how-to/scan2.png)
5. Ange koden och URL-Adressen i motsvarande rutor i appen och välj sedan **Slutför**.

    ![Skärmen för att ange koden och Webbadressen](./media/microsoft-authenticator-app-how-to/manual.png)

6. När appen visar namnet på ditt konto med en sexsiffrig kod under det, är du klar.

    ![skärmen konton](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Lägg till ett konto i appen med hjälp av enhetens fingeravtryck eller funktioner för ansiktsigenkänning
Din organisation kan kräva en PIN-kod att slutföra verifieringen-kontrollen. Microsoft Authenticator-appen kan använda enhetens fingeravtrycksläsare eller funktioner för ansiktsigenkänning i stället för en PIN-kod. Om du vill konfigurera detta på första verifieringen i appen visas ett alternativ för att använda Touch-ID (för iOS) eller fingeravtryck identifiering i stället. 

Om du vill konfigurera Touch-ID för Microsoft Authenticator, måste du slutföra en normal verifiering kontrollen med en PIN-kod. Microsoft Authenticator in automatiskt det för enheter som har stöd för Touch ID. 

![Verifiering av installationsprogrammet för Touch-ID](./media/microsoft-authenticator-app-how-to/touchid1.png)

Därifrån pekar framåt, när du blir ombedd att verifiera din inloggning, väljer mottagna push-meddelande och genomsöka ditt fingeravtryck istället för att ange din PIN-kod.

![Push-meddelande](./media/microsoft-authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Använda appen när du loggar in

När ditt konto har lagts till appen, kan du uppmanas att göra en test-verifiering för att kontrollera att allt har konfigurerats korrekt. Efter det är du klar! Du behöver inte göra något annat tills nästa gång du loggar in.

Om du väljer att använda verifieringskoder i appen, starta ska kunna se dem på startsidan. De kan ändra med 30 sekunders mellanrum så att du alltid har en ny kod när du behöver en. Men du behöver inte göra något med dem tills du loggar in och uppmanas att ange en Verifieringskod.  
