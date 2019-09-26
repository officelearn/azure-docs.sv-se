---
title: Ansluta till Windows Virtual Desktop från macOS – Azure
description: Så här ansluter du till det virtuella Windows-skrivbordet från macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bf6bfb52bd763eabd88f23701c3d1f240cdf4844
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310196"
---
# <a name="connect-from-macos"></a>Anslut från macOS

> Gäller för: macOS 10,12 eller senare

>[!NOTE]
> Möjligheten att komma åt Windows virtuella Skriv bords resurser från macOS-klienten är för närvarande tillgänglig i för hands versionen.

Du kan komma åt Windows virtuella Skriv bords resurser från dina macOS-enheter med vår nedladdnings bara klient. I den här guiden får du lära dig hur du konfigurerar-klienten.

## <a name="install-the-client"></a>Installera-klienten

Kom igång genom att [Ladda ned](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) och installera-klienten på din MacOS-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören fick dig att hämta listan över hanterade resurser som är tillgängliga för dig på din macOS-enhet.

Så här prenumererar du på en feed:

1. Välj **Lägg till feed** på huvud sidan för att ansluta till tjänsten och hämta dina resurser.
2. Ange feed-URL: en. Detta kan vara en URL eller en e-post adress:
   - Om du använder en URL kan du använda den som din administratör fick. Normalt är <https://rdweb.wvd.microsoft.com>URL: en.
   - Ange din e-postadress om du vill använda e-post. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om administratören har konfigurerat servern på det sättet.
3. Välj **Prenumerera**.
4. Logga in med ditt användar konto när du uppmanas till det.

När du har loggat in bör du se en lista över tillgängliga resurser.

När du prenumererar på en feed uppdateras innehållet i feeden automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="client-documentation"></a>Klient dokumentation

Mer information om macOS-klienten finns i [Kom igång med MacOS-klientens](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac) dokumentation.
