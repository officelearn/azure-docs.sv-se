---
title: Ansluta till Windows Virtual Desktop Windows 10 eller 7 – Azure
description: Ansluta till Windows Virtual Desktop med Windows Desktop-klienten.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154346"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ansluta med Windows-skrivbordsklienten

> Gäller: Windows 7, Windows 10 och Windows 10 IoT Enterprise

Du kan komma åt Windows Virtual Desktop-resurser på enheter med Windows 7, Windows 10 och Windows 10 IoT Enterprise med Windows Desktop-klienten.

> [!IMPORTANT]
> Windows Virtual Desktop stöder inte RADC-klienten (RemoteApp and Desktop Connections) eller MSTSC-klienten (Remote Desktop Connection).

> [!IMPORTANT]
> Windows Virtual Desktop stöder för närvarande inte klienten för fjärrskrivbord från Windows Store. Stöd för den här klienten kommer att läggas till i en framtida version.

## <a name="install-the-windows-desktop-client"></a>Installera Windows Desktop-klienten

Välj den klient som matchar din version av Windows:

- [Windows 64-bitars](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bitars](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Du kan installera klienten för den aktuella användaren, som inte kräver administratörsrättigheter, eller så kan administratören installera och konfigurera klienten så att alla användare på enheten kan komma åt den.

En gång installerat, klienten kan startas från Start-menyn genom att söka efter **Fjärrskrivbord**.

## <a name="subscribe-to-a-feed"></a>Prenumerera på ett flöde

Hämta listan över hanterade resurser som är tillgängliga för dig genom att prenumerera på flödet som tillhandahålls av administratören. Genom att prenumerera blir resurserna tillgängliga på den lokala datorn.

Så här prenumererar du på ett flöde:

1. Öppna Windows Desktop-klienten.
2. Välj **Prenumerera** på huvudsidan för att ansluta till tjänsten och hämta dina resurser.
3. Logga in med ditt användarkonto när du uppmanas att göra det.

När du har loggat in bör du se en lista över de resurser du kan komma åt.

Du kan starta resurser med en av två metoder.

- Dubbelklicka på en resurs från klientens huvudsida för att starta den.
- Starta en resurs som vanligt andra appar från Start-menyn.
  - Du kan också söka efter apparna i sökfältet.

När du prenumererar på ett flöde uppdateras innehållet i flödet automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort baserat på ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Windows Desktop-klienten finns i [Kom igång med Windows Desktop-klienten](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
