---
title: Ansluta till Windows förhandsversion av virtuella skrivbord med Windows 7 och Windows 10 - Azure
description: Hur du ansluter till Windows Virtual Desktop förhandsversionen av tjänsten med Windows 7 eller Windows 10.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/27/2019
ms.author: helohr
ms.openlocfilehash: 8b5a649d767dae9ae9c0fb077f550451a7f83601
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630387"
---
# <a name="connect-with-windows-7-and-windows-10"></a>Ansluta med Windows 7 och Windows 10

> Gäller för Windows 7 och Windows 10.

En nedladdningsbar klient är tillgängliga som ger tillgång till Windows Virtual Desktop resurser från enheter som kör Windows 7 och Windows 10.

> [!IMPORTANT]
> Använd inte **RemoteApp- och Desktop-anslutningar (RADC)** eller **anslutning till fjärrskrivbord (MSTSC)** åtkomst till virtuella Windows-skrivbordet resurser eftersom antingen klienten inte stöd för virtuella Windows-skrivbordet.

## <a name="install-the-client"></a>Installera klienten

[Ladda ned](https://go.microsoft.com/fwlink/?linkid=2068602) och installera klienten på din lokala dator. Detta kräver administratörsrättigheter.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Hämta listan över resurser som är tillgängliga för dig från din lokala dator genom att prenumerera på den feed som tillhandahålls av din administratör.

Prenumerera på en feed:

1. Starta klienten från listan alla appar, leta upp för **Remote Desktop**.
1. Välj **prenumerera** på huvudsidan för att ansluta till tjänsten och hämta dina resurser.
1. **Logga in** med ditt användarkonto när du tillfrågas.

Efter autentisering, bör du nu se en lista över resurser som är tillgängliga för dig.

Du kan starta resurser genom någon av två metoder.

- Dubbelklicka på en resurs för att starta den klientens huvudsidan.
- Starta en resurs som du vanligtvis använder andra appar från Start-menyn.
  - Du kan också söka efter appar i sökfältet.

## <a name="update-the-client"></a>Uppdatera klienten

När en ny version av klienten är tillgänglig, kommer du att meddelas av klienten och Windows Åtgärdscenter. Välj meddelandet för att starta uppdateringen.
