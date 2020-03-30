---
title: Ansluta till Windows Virtual Desktop från iOS - Azure
description: Så här ansluter du till Windows Virtual Desktop med iOS-klienten.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128250"
---
# <a name="connect-with-the-ios-client"></a>Ansluta med iOS-klienten

> Gäller: iOS 13.0 eller senare. Kompatibel med iPhone, iPad och iPod touch.

Du kan komma åt Windows Virtual Desktop-resurser från din iOS-enhet med vår nedladdningsbara klient. Den här guiden berättar hur du ställer in iOS-klienten.

## <a name="install-the-ios-client"></a>Installera iOS-klienten

För att komma [igång, ladda ner](https://aka.ms/rdios) och installera klienten på din iOS-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på ett flöde

Prenumerera på flödet från administratören för att få en lista över hanterade resurser som du kan komma åt på din iOS-enhet.

Så här prenumererar du på ett flöde:

1. Tryck på **+** i Anslutningscenter och sedan **på Lägg till arbetsyta**.
2. Ange feed-URL:en i fältet **Feed URL.** Feed-URL:en kan vara antingen en WEBBADRESS eller en e-postadress.
   - Om du använder en webbadress använder du den som administratören gav dig. Normalt är <https://rdweb.wvd.microsoft.com>webbadressen .
   - Om du vill använda e-post anger du din e-postadress. Detta talar om för klienten att söka efter en URL som är associerad med din e-postadress om administratören konfigurerade servern på det sättet.
3. Tryck på **Nästa**.
4. Ange dina autentiseringsuppgifter när du uppmanas att göra det.
   - För **Användarnamn**ger du användarnamnet med behörighet att komma åt resurser.
   - För **Lösenord**anger du lösenordet som är kopplat till användarnamnet.
   - Du kan också uppmanas att ange ytterligare faktorer om administratören har konfigurerat autentisering på det sättet.
5. Tryck på **Spara**.

Därefter ska anslutningscentret visa fjärrresurserna.

När du prenumererar på ett flöde uppdateras feedens innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort baserat på ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder iOS-klienten kan du läsa [komma igång med iOS-klientdokumentationen.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)
