---
title: Windows Virtual Desktop-miljö – Azure
description: De grundläggande elementen i en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127915"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Virtual Desktop-miljö

Windows Virtual Desktop är en tjänst som ger användarna enkel och säker åtkomst till sina virtualiserade skrivbord och RemoteApps. Det här avsnittet berättar lite mer om den allmänna strukturen i Windows Virtual Desktop-miljön.

## <a name="tenants"></a>Klientorganisationer

Windows Virtual Desktop-klienten är det primära gränssnittet för hantering av din Windows Virtual Desktop-miljö. Varje Windows Virtual Desktop-klient måste associeras med Azure Active Directory som innehåller de användare som loggar in på miljön. Från Windows Virtual Desktop-klienten kan du börja skapa värdpooler för att köra användarnas arbetsbelastningar.

## <a name="host-pools"></a>Värdpooler

En värdpool är en samling virtuella Azure-datorer som registrerar sig på Windows Virtual Desktop som sessionsvärdar när du kör Windows Virtual Desktop-agenten. Alla virtuella datorer för sessionsvärden i en värdpool ska hämtas från samma avbildning för en konsekvent användarupplevelse.

En värdpool kan vara en av två typer:

- Personlig, där varje sessionsvärd tilldelas enskilda användare.
- Poolad, där sessionsvärdar kan acceptera anslutningar från alla användare som har behörighet till en appgrupp i värdpoolen.

Du kan ange ytterligare egenskaper i värdpoolen för att ändra dess belastningsutjämningsbeteende, hur många sessioner varje sessionsvärd kan ta och vad användaren kan göra för att sessionsvärdar i värdpoolen när de är inloggade på sina Windows Virtual Desktop-sessioner. Du styr de resurser som publiceras för användare via appgrupper.

## <a name="app-groups"></a>Appgrupper

En appgrupp är en logisk gruppering av program som är installerade på sessionsvärdar i värdpoolen. En appgrupp kan vara en av två typer:

- RemoteApp, där användare kommer åt remoteapps som du väljer och publicerar för appgruppen individuellt
- Skrivbordet, där användarna kommer åt hela skrivbordet

Som standard skapas en skrivbordsappgrupp (med namnet "Desktop Application Group") automatiskt när du skapar en värdpool. Du kan ta bort den här appgruppen när som helst. Du kan dock inte skapa en annan skrivbordsappgrupp i värdpoolen medan det finns en skrivbordsappgrupp. Om du vill publicera RemoteApps måste du skapa en RemoteApp-appgrupp. Du kan skapa flera RemoteApp-appgrupper för att hantera olika arbetsscenarier. Olika RemoteApp-appgrupper kan också innehålla överlappande RemoteApps.

Om du vill publicera resurser till användare måste du tilldela dem till appgrupper. När du tilldelar användare till appgrupper bör du tänka på följande:

- En användare kan inte tilldelas både en skrivbordsappgrupp och en RemoteApp-appgrupp i samma värdpool.
- En användare kan tilldelas flera appgrupper i samma värdpool och deras flöde är en ackumulering av båda appgrupperna.

## <a name="tenant-groups"></a>Klientgrupper

I Windows Virtual Desktop är Windows Virtual Desktop-klienten där de flesta av installationerna och konfigurationen sker. Windows Virtual Desktop-klienten innehåller värdpooler, appgrupper och appgruppanvändartilldelningar. Det kan dock finnas vissa situationer där du måste hantera flera Windows Virtual Desktop-klienter samtidigt, särskilt om du är en CSP (Cloud Service Provider) eller en värdpartner. I dessa situationer kan du använda en anpassad Windows Virtual Desktop-klientgrupp för att placera var och en av kundernas Windows Virtual Desktop-klienter och centralt hantera åtkomst. Men om du bara hanterar en enda Windows Virtual Desktop-klientorganisation gäller inte klientgruppskonceptet och du kan fortsätta att driva och hantera din klientorganisation som finns i standardklientgruppen.

## <a name="end-users"></a>Slutanvändare

När du har tilldelat användare till deras appgrupper kan de ansluta till en Windows Virtual Desktop-distribution med någon av Windows Virtual Desktop-klienter.

## <a name="next-steps"></a>Nästa steg

Läs mer om delegerad åtkomst och hur du tilldelar roller till användare på [Delegerad åtkomst i Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Mer information om hur du konfigurerar windows virtual desktop-klienten finns [i Skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Mer information om hur du ansluter till Windows Virtual Desktop finns i någon av följande artiklar:

- [Ansluta från Windows 10 eller Windows 7](connect-windows-7-and-10.md)
- [Ansluta från en webbläsare](connect-web.md)
