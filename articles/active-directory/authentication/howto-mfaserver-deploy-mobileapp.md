---
title: Webbtjänst för Azure MFA Server Mobile App – Azure Active Directory
description: Konfigurera MFA-servern till att skicka push-meddelanden till användare med Microsoft Authenticator-appen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f62aacf8264eb583c523b6ee785f1908f0241644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848161"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Aktivera mobilappautentisering och Azure Multi-Factor Authentication Server

Microsoft Authenticator-appen erbjuder ytterligare ett verifieringsalternativ utanför IP-nätverket. I stället för att ringa ett automatiserat telefonsamtal eller skicka ett SMS till användaren vid inloggningen skickar Azure Multi-Factor Authentication ett meddelande till Microsoft Authentication-appen på användarens smartphone eller surfplatta. Användaren trycker bara på **Autentisera** (eller anger en PIN-kod och trycker på ”Autentisera”) i appen för att slutföra deras inloggning.

Att använda en mobilapp för tvåstegsverifiering rekommenderas när mobilmottagningen är opålitlig. Om du använder appen som en OATH-tokengenerator behövs ingen nätverks- eller internetanslutning.

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure Multi-Factor-autentisering. Befintliga kunder som har aktiverat MFA Server före den 1 juli kommer att kunna ladda ner den senaste versionen, framtida uppdateringar och generera aktiveringsautentiseringsuppgifter som vanligt.

> [!IMPORTANT]
> Om du har installerat Azure Multi-Factor Authentication Server v8.x eller senare krävs inte de flesta av de nedanstående stegen. Mobilappsautentisering kan konfigureras med anvisningarna under [Konfigurera mobilappen](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Krav

För att använda Microsoft Authenticator-appen måste du köra Multi-Factor Authentication Server v8.x eller senare

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server

1. I Multi-Factor Authentication-servern klickar du på ikonen Användarportal. Om användarna ska kunna styra sina autentiseringsmetoder markerar du **Mobilapp** under **Tillåt användare att välja metod** på fliken Inställningar. Om den här funktionen inte är aktiverad måste slutanvändarna kontakta din supportavdelning för att slutföra aktiveringen av mobilappen.
2. Markera kryssrutan **Tillåt användare att aktivera mobilapp**.
3. Markera kryssrutan **Tillåt användarregistrering**.
4. Klicka på ikonen för **Mobilapp**.
5. Fyll i fältet **Kontonamn** med företagets eller organisationens namn som ska visas i kontots mobilapp.
   ![MFA-serverkonfiguration för mobilappinställningar](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Nästa steg

- [Avancerade scenarier med Azure Multi-Factor Autentisering och tredjeparts-VPN](howto-mfaserver-nps-vpn.md).
