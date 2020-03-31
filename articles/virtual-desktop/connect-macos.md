---
title: Ansluta till Windows Virtual Desktop från macOS - Azure
description: Så här ansluter du till Windows Virtual Desktop med macOS-klienten.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 673c20355e9ad4abf2c8318b3eb2ebbcc36ed8fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128242"
---
# <a name="connect-with-the-macos-client"></a>Ansluta med macOS-klienten

> Gäller: macOS 10.12 eller senare

Du kan komma åt Windows Virtual Desktop-resurser från dina macOS-enheter med vår nedladdningsbara klient. Den här guiden berättar hur du ställer in klienten.

## <a name="install-the-client"></a>Installera klienten

För att komma [igång, ladda ner](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) och installera klienten på din macOS-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på ett flöde

Prenumerera på det flöde som administratören gav dig för att få en lista över hanterade resurser som är tillgängliga för dig på din macOS-enhet.

Så här prenumererar du på ett flöde:

1. Välj **Lägg till feed** på huvudsidan för att ansluta till tjänsten och hämta dina resurser.
2. Ange feed-URL:en. Detta kan vara en webbadress eller e-postadress:
   - Om du använder en webbadress använder du den som administratören gav dig. Normalt är <https://rdweb.wvd.microsoft.com>webbadressen .
   - Om du vill använda e-post anger du din e-postadress. Detta talar om för klienten att söka efter en URL som är associerad med din e-postadress om administratören konfigurerade servern på det sättet.
3. Välj **Prenumerera**.
4. Logga in med ditt användarkonto när du uppmanas att göra det.

När du har loggat in bör du se en lista över tillgängliga resurser.

När du prenumererar på ett flöde uppdateras flödets innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort baserat på ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om macOS-klienten kan du läsa [komma igång med macOS-klientdokumentationen.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)
