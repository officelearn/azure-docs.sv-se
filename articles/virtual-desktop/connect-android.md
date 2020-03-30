---
title: Ansluta till Windows Virtual Desktop från Android - Azure
description: Så här ansluter du till Windows Virtual Desktop med Android-klienten.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295367"
---
# <a name="connect-with-the-android-client"></a>Ansluta med Android-klienten

> Gäller: Android 4.1 och senare Chromebooks med ChromeOS 53 och senare.

>[!NOTE]
> Möjligheten att komma åt Windows Virtual Desktop-resurser från Android-klienten är för närvarande tillgänglig i förhandsversionen.

Du kan komma åt Windows Virtual Desktop-resurser från din Android-enhet med vår nedladdningsbara klient. Du kan också använda Android-klienten på Chromebook-enheter som stöder Google Play Butik. Den här guiden kommer att berätta hur du ställer in Android-klienten.

## <a name="install-the-android-client"></a>Installera Android-klienten

För att komma [igång, ladda ner](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) och installera klienten på din Android-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på ett flöde

Prenumerera på flödet från administratören för att få en lista över hanterade resurser som du kan komma åt på din Android-enhet.

Så här prenumererar du på ett flöde:

1. Tryck på **+** i Anslutningscenter och sedan **på Fjärrresursmatning**.
2. Ange feed-URL:en i fältet **Feed URL.** Feed-URL:en kan vara antingen en WEBBADRESS eller en e-postadress.
   - Om du använder en webbadress använder du den <https://rdweb.wvd.microsoft.com>som administratören gav dig, normalt .
   - Om du vill använda e-post anger du din e-postadress. Klienten söker efter en URL som är kopplad till din e-postadress om administratören har konfigurerat servern på det sättet.
3. Tryck på **NÄSTA**.
4. Ange dina autentiseringsuppgifter när du uppmanas att göra det.
   - För **Användarnamn**ger du användarnamnet med behörighet att komma åt resurser.
   - För **Lösenord**anger du lösenordet som är kopplat till användarnamnet.
   - Du kan också uppmanas att ange ytterligare faktorer om administratören har konfigurerat autentisering på det sättet.

När du har prenumererat bör anslutningscentret visa fjärrresurserna.

När du prenumererar på ett flöde uppdateras feedens innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort baserat på ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder Android-klienten kan du läsa [Kom igång med Android-klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
