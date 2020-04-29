---
title: Ansluta till Windows Virtual Desktop från Android – Azure
description: Ansluta till virtuella Windows-datorer med Android-klienten.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295367"
---
# <a name="connect-with-the-android-client"></a>Ansluta med Android-klienten

> Gäller för: Android 4,1 och senare, Chromebooks med Chrome 53 och senare.

>[!NOTE]
> Möjligheten att komma åt Windows-resurser för virtuella skriv bord från Android-klienten är för närvarande tillgänglig som för hands version.

Du kan komma åt Windows-resurser för virtuella skriv bord från din Android-enhet med vår nedladdnings bara klient. Du kan också använda Android-klienten på Chromebook-enheter som har stöd för Google Play Butik. I den här guiden får du lära dig hur du konfigurerar Android-klienten.

## <a name="install-the-android-client"></a>Installera Android-klienten

Kom igång genom att [Ladda ned](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) och installera-klienten på din Android-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören har fått för att hämta listan över hanterade resurser som du har åtkomst till på din Android-enhet.

Så här prenumererar du på en feed:

1. I anslutnings Center trycker du på **+** och trycker sedan på **fjär resurs matning**.
2. Ange feed-URL: en i fältet **feed URL** . Feed-URL: en kan vara antingen en URL eller en e-postadress.
   - Om du använder en URL kan du använda den som din administratör har gett dig <https://rdweb.wvd.microsoft.com>, vanligt vis.
   - Ange din e-postadress om du vill använda e-post. Klienten söker efter en URL som är kopplad till din e-postadress om din administratör har konfigurerat servern på det sättet.
3. Tryck på **Nästa**.
4. Ange dina autentiseringsuppgifter när du uppmanas till det.
   - Ge användar namnet behörighet att komma åt resurser för **användar namn**.
   - Ange lösen ordet som är associerat med användar namnet för **lösen ord**.
   - Du kan också uppmanas att ange ytterligare faktorer om din administratör har konfigurerat autentiseringen på det sättet.

När du har prenumererat bör anslutnings centret Visa fjär resurserna.

När du prenumererar på en feed uppdateras feedens innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Android-klienten finns i [komma igång med Android-klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
