---
title: Ansluta till Windows Virtual Desktop från Windows 10 eller Windows 7 – Azure
description: Hur du ansluter till virtuella Windows-datorer med hjälp av Windows Skriv bords klienten.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
ms.openlocfilehash: 561dad566d7f1caaf609c8013fa075062e4471ea
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676749"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ansluta med Windows-skrivbordsklienten

> Gäller för: Windows 7 och Windows 10

Du kan komma åt Windows-resurser för virtuella skriv bord på enheter med Windows 7 eller Windows 10 med hjälp av Windows Skriv bords klienten.

> [!IMPORTANT]
> Windows Virtual Desktop stöder inte RADC-klienten (RemoteApp-och Desktop Connections) eller Anslutning till fjärrskrivbord-klienten (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Installera Windows Desktop-klienten

Det här är de klienter som du kan ladda ned för tillfället:

- [Windows 64-bitars](https://go.microsoft.com/fwlink/?linkid=2068602)

Vi uppdaterar den här listan när klienten blir tillgänglig för fler versioner av Windows.

Du kan installera klienten för den aktuella användaren, som inte kräver administratörs rättigheter, eller så kan administratören installera och konfigurera klienten så att alla användare på enheten kan komma åt den.

När du har installerat kan klienten startas från Start-menyn genom att söka efter **fjärr skrivbord**.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Hämta listan över hanterade resurser som är tillgängliga för dig genom att prenumerera på den feed som administratören har fått. När du prenumererar blir resurserna tillgängliga på din lokala dator.

Så här prenumererar du på en feed:

1. Öppna Windows Desktop-klienten.
2. Välj **Prenumerera** på huvud sidan för att ansluta till tjänsten och hämta dina resurser.
3. Logga in med ditt användar konto när du uppmanas till det.

När du har loggat in bör du se en lista över de resurser som du har åtkomst till.

Du kan starta resurser på en av två sätt.

- Från klientens huvud sida dubbelklickar du på en resurs för att starta den.
- Starta en resurs som vanligt vis andra appar från Start-menyn.
  - Du kan också söka efter apparna i Sök fältet.

När du prenumererar på en feed uppdateras innehållet i flödet automatiskt med jämna mellanrum. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder Windows-skrivbordet kan du läsa [komma igång med Windows Skriv bords klienten](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop).
