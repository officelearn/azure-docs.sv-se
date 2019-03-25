---
title: Windows Virtual Desktop förhandsversionsmiljön - Azure
description: De grundläggande delarna i en förhandsversion för virtuella skrivbord i Windows-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403518"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Förhandsversionsmiljön Windows virtuella skrivbord

Windows Virtual Desktop Preview är en tjänst som ger användarna enkel och säker åtkomst till sina virtualiserade skrivbord och RemoteApps. Det här avsnittet får du veta lite mer om den allmänna strukturen för virtuellt skrivbord i Windows-miljön.

## <a name="tenants"></a>Klienter

Virtuellt skrivbord i Windows-klient är det primära gränssnittet för att hantera din miljö för virtuella Windows-skrivbordet. Varje virtuellt skrivbord i Windows-klient måste vara associerad med Azure Active Directory som innehåller de användare som ska logga in i miljön. Från virtuellt skrivbord i Windows-klient kan du börja skapa värd-pooler för att köra dina användares arbetsbelastningar.

## <a name="host-pools"></a>Värd-pooler

En värd-pool är en samling Azure-datorer som registrerar till virtuella Windows-skrivbordet som sessionen värdar när du kör virtuella Windows-skrivbordet agenten. Alla session virtuella datorer i en pool med värden ska hämtas från samma avbildning för en konsekvent användarupplevelse.

Poolen värden kan vara något av två typer:

- Personal, där varje värd för fjärrskrivbordssession tilldelas till enskilda användare.
- Tilldelade i poler, där sessionen värdar kan godkänna anslutningar från alla användare med behörighet att en appgrupp i poolen för värden.

Du kan ange ytterligare egenskaper på värd-poolen och ändra dess belastningsutjämningen, hur många sessioner som varje värd kan ta och vad användaren kan göra att sessionen värdar i poolen värd loggat in på sina virtuella skrivbordet i Windows-sessioner. Du kan styra de resurser som har publicerats för användare via app-grupper.

## <a name="app-groups"></a>App-grupper

En app-gruppen är en logisk gruppering av program som har installerats på sessionen värdar i poolen värden. En appgrupp kan vara något av två typer:

- RemoteApp, där du vill att användarna ska komma åt RemoteApps du individuellt väljer och publicera till app-gruppen
- Desktop, där du vill att användarna ska komma åt hela skrivbordet

Som standard skapas automatiskt en skrivbordsapp grupp (med namnet ”Desktop programgruppen”) när du skapar en pool med värden. Du kan ta bort den här appen gruppen när som helst. Men skapa du inte någon annan skrivbordsapp grupp i poolen värden även om det finns en skrivbordsapp-grupp. Om du vill publicera RemoteApps, måste du skapa en RemoteApp-app-grupp. Du kan skapa flera RemoteApp app-grupper för att hantera olika arbetsscenarier. Olika RemoteApp-app-grupper kan också innehålla överlappande RemoteApps.

För att publicera resurser för användare, måste du tilldela dem till app-grupper. När du tilldelar användare till app-grupper, Överväg följande saker:

- En användare kan inte tilldelas till både en skrivbordsapp grupp och en RemoteApp-app-gruppen i samma pool för värden.
- En användare kan tilldelas till flera appgrupper inom samma värd-pool och deras flödet kommer att vara en anhopning av både app-grupper.

## <a name="tenant-groups"></a>Klient-grupper

I virtuella Windows-skrivbordet är virtuellt skrivbord i Windows-klient där installationen och konfigurationen inträffar. Virtuellt skrivbord i Windows-klienten innehåller värden pooler, app-grupper och app grupptilldelningar för användaren. Det kan dock finnas vissa situationer där du behöver hantera flera virtuella Windows-skrivbordet klienter samtidigt, särskilt om du är en Cloud Service Provider (CSP) eller en värdtjänst partner. I sådana fall kan använda du en anpassad grupp av virtuella Windows-skrivbordet klient att placera var och en av kundernas virtuella skrivbordet i Windows-klienter och centralt hantera åtkomst. Om du bara hanterar ett enda virtuellt skrivbord i Windows-klient, klient grupp konceptet gäller inte och du kan fortsätta att fungera och hantera din klient finns i standardgruppen för klient.

## <a name="end-users"></a>Slutanvändare

När du har tilldelat användare till deras app-grupper, kan de ansluta till en distribution med virtuella Windows-skrivbordet med någon av klienterna virtuella Windows-skrivbordet.

## <a name="next-steps"></a>Nästa steg

Mer information om delegerad åtkomst och tilldela roller till användare i [delegerad åtkomst i Windows Virtual Desktop förhandsversion](delegated-access-virtual-desktop.md).

Läs hur du ställer in din klient för virtuella Windows-skrivbordet i [skapa en klient i förhandsversion för virtuella skrivbord i Windows](tenant-setup-azure-active-directory.md).

Om du vill lära dig mer om att ansluta till virtuella Windows-skrivbordet, finns i följande artiklar:

- [Ansluta till fjärrskrivbord-klienten på Windows 7 och Windows 10](connect-windows-7-and-10.md)
- [Ansluta till webbklienten förhandsversion för virtuella skrivbord i Windows](connect-web.md)
