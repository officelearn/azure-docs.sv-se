---
title: Hantera inställningar för tvåstegsverifiering - Azure Active Directory | Microsoft Docs
description: Hantera hur du använder Azure Multi-Factor Authentication, inklusive ändrar kontaktinformation eller konfigurera dina enheter.
services: active-directory
keywords: Multi-Factor authentication-klienten, problem med autentisering, Korrelations-ID
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433c2d712ca4867a5ec59f86c333511070b6d507
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334496"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Hantera dina inställningar för tvåstegsverifiering
Den här artikeln ger svar på frågor om hur du uppdaterar inställningar för tvåstegsverifiering verifieringen eller Multi-Factor authentication. Om du har problem med loggar in på ditt konto, se [har du problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md) för hjälp med felsökning.

## <a name="where-to-find-the-settings-page"></a>Var du hittar inställningssidan
Beroende på hur ditt företag har konfigurerat Azure Multi-Factor Authentication, finns det vissa ställen där du kan ändra dina inställningar som ditt telefonnummer.

Om företagets support skickas ut en specifik URL eller steg för att hantera tvåstegsverifiering, följer du dessa instruktioner. I annat fall bör följande anvisningar fungera för alla andra. Om du gör så här men inte ser samma alternativ, betyder det att arbetsplatsen eller skolan anpassat sin egen portal. Be din administratör om länken till Azure Multi-Factor Authentication-portalen.

**Gå till sidan ytterligare verifiering**

- Gå till https://aka.ms/MFASetup.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Om du klickar på länken inte fungerar för dig, du kan också få till den **ytterligare säkerhetsverifiering** sidan genom att följa dessa steg:

1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2. Välj namnet på ditt konto längst upp till höger och välj sedan **profil**.

3. Välj **ytterligare säkerhetsverifiering**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. Ytterligare verifiering sidan läses in med dina inställningar.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Jag vill ändra mitt telefonnummer eller lägga till ett sekundärt tal
Det är viktigt att du konfigurerar ett telefonnummer för sekundär autentisering.  Eftersom din primära telefonnummer och din mobilapp har förmodligen samma telefonen kan är det sekundära telefonnumret det snabbaste sättet som du kommer att kunna komma åt ditt konto om din telefon tappas bort eller blir stulen.

> [!NOTE]
> Om du inte har åtkomst till ditt primära telefonnummer och behöver hjälp med att komma in på ditt konto, se den [har du problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md) artikeln för mer hjälp.  

**Ändra ditt primära telefonnummer:**  

1. På den **ytterligare säkerhetsverifiering** väljer du textrutan med ditt aktuella telefonnummer och redigera den med ditt nya telefonnummer.  
2. Välj **Spara**.  
3. Om det här telefonnumret är det tal som du använder för din önskade verifieringsalternativet, måste du kontrollera det nya numret innan du kan spara den.  

**Lägga till ett sekundärt telefonnummer:**  

1. På sidan för verifiering av ytterligare säkerhet markerar du kryssrutan bredvid **Alternativ telefon för autentisering.**  
2. Ange ditt sekundära telefonnummer i textrutan.  
3. Välj **spara** och ändringarna är klar.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Tvåstegsverifiering igen på en enhet som du har markerat som betrodda

Beroende på inställningarna för din organisation kan du ha en kryssruta med texten ”fråga inte igen för **X** dagar” när du utför tvåstegsverifiering på din webbläsare. Om du markerar den här kryssrutan och sedan tappar bort din enhet eller tror att ditt konto komprometteras, bör du återställa tvåstegsverifiering till alla dina enheter.

1. På sidan för verifiering av ytterligare säkerhet väljer **Återställ multifaktorautentisering på tidigare betrodda enheter**.
2. Nästa gång du loggar in på valfri enhet, uppmanas du att utföra tvåstegsverifiering.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Hur jag för att rensa Microsoft Authenticator från min gamla enhet och flytta till en ny?
När du avinstallerar appen från enheten eller återställa enheten, tas inte bort aktivering på serverdelen. Mer information finns i [Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="next-steps"></a>Nästa steg
* Få felsökningstips och hjälp om [har du problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md)
* Konfigurera [applösenord](multi-factor-authentication-end-user-app-passwords.md) för alla appar som inte stöder tvåstegsverifiering.
