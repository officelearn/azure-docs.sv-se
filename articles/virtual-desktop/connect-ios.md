---
title: Ansluta till virtuella Windows-datorer från iOS – Azure
description: Ansluta till virtuella Windows-datorer med iOS-klienten.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bfc7efa6e8ead3b53704e3c9bd189b18cb787618
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605841"
---
# <a name="connect-with-the-ios-client"></a>Ansluta med iOS-klienten

> Gäller för: iOS 8,0 eller senare. Kompatibel med iPhone, iPad och iPod touch.

>[!NOTE]
> IOS-klienten är fortfarande i för hands version.

Du kan komma åt Windows-resurser för virtuella skriv bord från din iOS-enhet med vår nedladdnings bara klient. I den här guiden får du lära dig hur du konfigurerar iOS-klienten.

## <a name="install-the-ios-beta-client"></a>Installera iOS beta-klienten
Installera iOS beta-klienten:

1. Installera [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) -appen på din iOS-enhet.
2. Öppna en webbläsare på din iOS-enhet och gå till [aka.MS/rdiosbeta](https://aka.ms/rdiosbeta).
3. Under etiketten **steg 2: delta i Beta versionen**väljer du **Starta testning**.
4. När du omdirigeras till TestFlight-appen väljer du **acceptera**och väljer sedan **Installera**.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören har fått för att hämta listan över hanterade resurser som du har åtkomst till på din iOS-enhet.

Så här prenumererar du på en feed:

1. I anslutnings Center trycker du på **+** och sedan på **Lägg till arbets yta**.
2. Ange feed-URL: en i fältet **feed URL** . Feed-URL: en kan vara antingen en URL eller en e-postadress.
   - Om du använder en URL kan du använda den som din administratör fick. Normalt är URL: en <https://rdweb.wvd.microsoft.com>.
   - Ange din e-postadress om du vill använda e-post. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om administratören har konfigurerat servern på det sättet.
3. Tryck på **Nästa**.
4. Ange dina autentiseringsuppgifter när du uppmanas till det.
   - Ge användar namnet behörighet att komma åt resurser för **användar namn**.
   - Ange lösen ordet som är associerat med användar namnet för **lösen ord**.
   - Du kan också uppmanas att ange ytterligare faktorer om din administratör har konfigurerat autentiseringen på det sättet.
5. Tryck på **Spara**.

Därefter bör anslutnings centret Visa fjär resurserna.

När du prenumererar på en feed uppdateras feedens innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder iOS beta-klienten finns i [komma igång med klient](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) dokumentationen för iOS.
