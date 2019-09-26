---
title: Ansluta till virtuella Windows-datorer från iOS – Azure
description: Så här ansluter du till det virtuella Windows-skrivbordet från iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: 3e77a77dfa2de003722419f861e957d4cb011b71
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310326"
---
# <a name="connect-from-ios"></a>Anslut från iOS

> Gäller för: iOS 8,0 eller senare. Kompatibel med iPhone, iPad och iPod touch.

>[!NOTE]
> IOS-klienten är fortfarande i för hands version.

Du kan komma åt Windows-resurser för virtuella skriv bord från din iOS-enhet med vår nedladdnings bara klient. I den här guiden får du lära dig hur du konfigurerar iOS-klienten.

## <a name="install-the-ios-beta-client"></a>Installera iOS beta-klienten
Installera iOS beta-klienten:

1. Installera [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) -appen på din iOS-enhet.
2. Öppna en webbläsare på din iOS-enhet och gå till [aka.MS/rdiosbeta](https://aka.ms/rdiosbeta).
3. Under etiketten **steg 2: Delta i beta**versionen och välj **Starta testning**.
4. När du omdirigeras till TestFlight-appen väljer du **acceptera**och väljer sedan **Installera**.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören har fått för att hämta listan över hanterade resurser som du har åtkomst till på din iOS-enhet.

Så här prenumererar du på en feed:

1. I anslutnings Center trycker du på **+** och sedan på **Lägg till arbets yta**.
2. Ange feed-URL: en i fältet **feed URL** . Feed-URL: en kan vara antingen en URL eller en e-postadress.
   - Om du använder en URL kan du använda den som din administratör fick. Normalt är <https://rdweb.wvd.microsoft.com>URL: en.
   - Ange din e-postadress om du vill använda e-post. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om administratören har konfigurerat servern på det sättet.
3. Tryck på **Nästa**.
4. Ange dina autentiseringsuppgifter när du uppmanas till det.
   - Ge användar namnet behörighet att komma åt resurser för **användar namn**.
   - Ange lösen ordet som är associerat med användar namnet för **lösen ord**.
   - Du kan också uppmanas att ange ytterligare faktorer om din administratör har konfigurerat autentiseringen på det sättet.
5. Tryck på **Spara**.

Därefter bör anslutnings centret Visa fjär resurserna.

När du prenumererar på en feed uppdateras feedens innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="client-documentation"></a>Klient dokumentation

Mer information om hur du använder iOS beta-klienten finns i [komma igång med klient](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) dokumentationen för iOS.
