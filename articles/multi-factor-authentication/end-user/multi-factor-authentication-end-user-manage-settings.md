---
title: "Hantera dina inställningar för tvåstegsverifiering | Microsoft Docs"
description: "Hantera hur du använder Azure Multi-Factor Authentication inklusive ändrar kontaktinformation eller konfigurera dina enheter."
services: multi-factor-authentication
keywords: flerfunktionsautentisering klienten, problem med autentisering, Korrelations-ID
documentationcenter: 
author: barlanmsft
manager: femila
editor: yossib
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 87b1b6b03f5aaab5da6491c86132d4193693055a
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="manage-your-settings-for-two-step-verification"></a>Hantera inställningar för tvåstegsverifiering
Den här artikeln innehåller svar på frågor om hur du uppdaterar inställningar för tvåstegsverifiering verifieringen eller Multi-Factor authentication. Om du har problem att logga in på ditt konto, se [har problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md) för hjälp med felsökning.

## <a name="where-to-find-the-settings-page"></a>Här hittar du på inställningssidan
Beroende på hur företaget du konfigurerar Azure Multi-Factor Authentication, finns det några platser där du kan ändra inställningarna som ditt telefonnummer.

Om ditt företag har stöd för skickas en specifik URL eller steg för att hantera tvåstegsverifiering, Följ dessa instruktioner. Annars bör följande instruktioner fungerar för alla andra. Om du gör så här men inte ser samma alternativ, innebär det att ditt arbete eller skola anpassad sina egna portal. Be din administratör för att länken ska flerfunktionsautentisering Azure-portalen.

1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Välj namnet på ditt konto i övre högra och sedan **profil**.  
3. Välj **ytterligare säkerhetsverifiering**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Ytterligare säkerhet verifiering sidan läses in med dina inställningar.

    ![Proofup](./media/multi-factor-authentication-end-user-manage/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Jag vill ändra mitt telefonnummer eller lägga till en sekundär tal
Det är viktigt att du konfigurerar ett telefonnummer för sekundära autentiseringen.  Eftersom ditt primära telefonnummer och din mobila app är förmodligen på samma telefon, är det enda sättet du kommer att kunna komma åt ditt konto om din telefon blir stulen eller sekundära telefonnumret.

> [!NOTE]
> Om du inte har åtkomst till ditt primära telefonnummer och behöver hjälp med att få i till ditt konto, se vår hjälpavsnitten i [har problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md).  

**Ändra ditt primära telefonnummer:**  

1. Markera textrutan med ditt aktuella telefonnummer på sidan ytterligare säkerhet verifiering och redigera den med ditt nya telefonnummer.  
2. Välj **Spara**.  
3. Om detta är det tal som du använder för din verifieringsalternativet måste du kontrollera det nya värdet innan du kan spara den.  

**Lägg till ett sekundärt telefonnummer:**  

1. På sidan ytterligare säkerhet verifiering, markera kryssrutan bredvid **telefon för alternativa autentisering.**  
2. Ange ditt sekundära telefonnummer i textrutan.  
3. Välj **spara** och ändringarna är klara.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Kräv tvåstegsverifiering igen på en enhet som du har markerat som betrodd

Beroende på inställningarna för din organisation kan du ha en kryssruta med texten ”fråga inte igen för **X** dagar” när du utför tvåstegsverifiering i webbläsaren. Om den här kryssrutan och sedan tappar bort din enhet eller anser att ditt konto komprometteras, bör du återställa tvåstegsverifiering till alla dina enheter.

1. Välj på sidan för ytterligare säkerhet verifiering **Återställ multifaktorautentisering på tidigare betrodda enheter**.
2. Nästa gång du loggar in på en enhet, uppmanas du att utföra tvåstegsverifiering.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Hur gör Rensa Microsoft Authenticator från min gamla enhet och gå till en ny?
När du avinstallerar appen från enheten eller återställa enheten, tas inte bort aktivering på serverdelen. Mer information finns i [Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

## <a name="next-steps"></a>Nästa steg
* Hämta felsökningstips och hjälpa på [har problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md)
* Ställ in [applösenord](multi-factor-authentication-end-user-app-passwords.md) för alla program som inte stöder tvåstegsverifiering.
