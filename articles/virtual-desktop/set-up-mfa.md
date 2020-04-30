---
title: Konfigurera Azure-Multi-Factor Authentication för Windows Virtual Desktop – Azure
description: Konfigurera Azure-Multi-Factor Authentication för ökad säkerhet i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 069d2a153e307ed94032ce1d980f26521969fc56
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508351"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Aktivera Azure Multi-Factor Authentication för virtuellt Windows-skrivbord

Windows-klienten för virtuellt Windows-skrivbord är ett utmärkt alternativ för att integrera virtuella Windows-datorer med din lokala dator. Men när du konfigurerar ditt Windows-konto för virtuella skriv bord till Windows-klienten, finns det vissa mått som du måste vidta för att hålla dig trygg och dina användare.

När du först loggar in frågar klienten efter ditt användar namn, lösen ord och Azure MFA. Efter det kommer klienten att komma ihåg din token från din Azure Active Directory (AD) företags program nästa gången du loggar in. När du väljer **kom ihåg mig**kan användarna logga in efter omstart av klienten utan att behöva ange sina autentiseringsuppgifter på annat sätt.

Även om det är praktiskt att komma ihåg autentiseringsuppgifterna, kan det också göra distributioner i företags scenarier eller personliga enheter mindre säkra. För att skydda dina användare måste du kontrol lera att klienten fortfarande ber om autentiseringsuppgifter för Azure Multi-Factor Authentication (MFA). I den här artikeln visas hur du konfigurerar principen för villkorlig åtkomst för Windows Virtual Desktop för att aktivera den här inställningen.

## <a name="prerequisites"></a>Krav

Det här behöver du för att komma igång:

- Tilldela användare en licens som innehåller Azure Active Directory Premium P1 eller P2.
- En Azure Active Directory grupp med dina användare tilldelade som grupp medlemmar.
- Aktivera Azure MFA för alla dina användare. Mer information om hur du gör finns i [så här kräver du tvåstegsverifiering för en användare](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Följande inställning gäller även för [webb klienten för virtuella Windows-datorer](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

I det här avsnittet visas hur du skapar en princip för villkorlig åtkomst som kräver Multi-Factor Authentication när du ansluter till det virtuella Windows-skrivbordet.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory** > **säkerhet** > **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera**väljer du **Välj användare och grupper** > **användare och grupper** > väljer den grupp som skapades i förutsättnings stadiet.
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder** > **inkluderar**väljer du **Välj appar**.
   1. Välj **Windows Virtual Desktop** och **Windows Virtual Desktop Client**och välj sedan **Välj** . **Done**
   ![En skärm bild av sidan molnappar eller åtgärder. Klient program för virtuella Windows-datorer och Windows-appar för virtuella skriv bord är markerade i rött.](media/cloud-apps-enterprise-selected.png)
1. Under **åtkomst kontroller** > **tilldelar**väljer du **bevilja åtkomst**, **kräver Multi-Factor Authentication**och **väljer**sedan.
1. Under **Access Controls** > **session**väljer du **inloggnings frekvens**, anger värdet till **1** och enheten till **timmar**och **väljer**sedan.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Läs mer om användar inloggnings frekvens](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
