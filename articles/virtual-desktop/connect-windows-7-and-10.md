---
title: Ansluta till Windows virtuella skrivbord förhandsversion från Windows 10 eller Windows 7 - Azure
description: Hur du ansluter till Windows-Preview för virtuella skrivbord från Windows 10 eller Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145992"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Ansluta från Windows 10 eller Windows 7

> Gäller för Windows 7 och Windows 10.

En nedladdningsbar klient är tillgängliga som ger tillgång till Windows Virtual Desktop resurser från enheter som kör Windows 7 och Windows 10.

> [!IMPORTANT]
> Använd inte **RemoteApp- och Desktop-anslutningar (RADC)** eller **anslutning till fjärrskrivbord (MSTSC)** åtkomst till virtuella Windows-skrivbordet resurser eftersom antingen klienten inte stöd för virtuella Windows-skrivbordet.

## <a name="install-the-client"></a>Installera klienten

[Ladda ned](https://go.microsoft.com/fwlink/?linkid=2068602) och installera klienten på din lokala dator. Installationen kräver administratörsrättigheter.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Hämta listan över hanterade resurser som är tillgängliga för dig genom att prenumerera på den feed som tillhandahålls av din administratör. Prenumerera gör resurser tillgängliga på din lokala dator.

Prenumerera på en feed:

1. Starta klienten från listan alla appar, leta upp för **Remote Desktop**.
1. Välj **prenumerera** på huvudsidan för att ansluta till tjänsten och hämta dina resurser.
1. **Logga in** med ditt användarkonto när du tillfrågas.

Efter autentisering, bör du nu se en lista över resurser som är tillgängliga för dig.

Du kan starta resurser genom någon av två metoder.

- Dubbelklicka på en resurs för att starta den klientens huvudsidan.
- Starta en resurs som du vanligtvis använder andra appar från Start-menyn.
  - Du kan också söka efter appar i sökfältet.

När du prenumererar på en feed uppdateras innehållet i flödet automatiskt med jämna mellanrum. Resurser kan läggas till, ändras eller tas bort baserat på ändringar som gjorts av administratören.

## <a name="view-the-details-of-a-feed"></a>Visa information om en feed

När du prenumererar kan du visa mer information om flödet genom att öppna informationspanelen.

1. Från klientens huvudsidan, Välj ellipsen (**...** ) till höger om namnet på feed.
1. Välj den nedrullningsbara menyn **information**.
1. Panelen information som visas på höger sida av klienten.

Informationspanelen innehåller användbar information om flödet:

- URL: en och användarnamn som används för att prenumerera på
- Antalet appar och stationära datorer
- Datum och tid för den senaste uppdateringen
- Status för den senaste uppdateringen

Om det behövs kan du starta en manuell uppdatering genom att välja på **Uppdatera nu**.

## <a name="unsubscribe-from-a-feed"></a>Slutar prenumerera på en feed

Det här avsnittet får du lära dig att avbryta prenumerationen på en feed. Du kan avbryta prenumerationen för att prenumerera på igen med ett annat konto eller ta bort dina resurser från systemet.

1. Från klientens huvudsidan, Välj ellipsen (**...** ) till höger om namnet på feed.
1. Välj den nedrullningsbara menyn **Unsubscribe**.
1. Granska och välj **Fortsätt** från dialogrutan.

## <a name="update-the-client"></a>Uppdatera klienten

När en ny version av klienten är tillgänglig, kommer du att meddelas av klienten och Windows Åtgärdscenter. Välj meddelandet för att starta uppdateringen.
