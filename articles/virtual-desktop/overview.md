---
title: Vad är för hands versionen av Windows Virtual Desktop?  – Azure
description: En översikt över för hands versionen av Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 07/17/2019
ms.author: helohr
ms.openlocfilehash: 03105232cb92a65f8c38d6d755910739a3fc4720
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305056"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Vad är för hands versionen av Windows Virtual Desktop? 

För hands versionen av Windows Virtual Desktop är tillgänglig i den offentliga för hands versionen är en stationär dator och app Virtualization-tjänst som körs i molnet.

Det här kan du göra när du kör Windows Virtual Desktop på Azure:

* Konfigurera en Windows 10-distribution med flera sessioner som ger en fullständig Windows 10 med skalbarhet
* Virtualisera Office 365 ProPlus och optimera det för att köras i virtuella scenarier med flera användare
* Tillhandahålla virtuella Windows 7-datorer med kostnads fria utökade säkerhets uppdateringar
* Ta med dina befintliga Fjärrskrivbordstjänster (RDS) och Windows Server-datorer och appar till valfri dator
* Virtualisera både Station ära datorer och appar
* Hantera Windows 10-, Windows Server-och Windows 7-datorer och-appar med en enhetlig hanterings upplevelse

## <a name="introductory-video"></a>Introduktions video

I följande video demonstrerar Scott Manchester några av funktionerna i Windows Virtual Desktop:

<br></br><iframe src="https://www.youtube-nocookie.com/embed/30dOLcZ4_9U" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med Windows Virtual Desktop kan du konfigurera en skalbar och flexibel miljö:

* Skapa en fullständig Virtualization-miljö för skriv bord i din Azure-prenumeration utan att behöva köra några ytterligare Gateway-servrar.
* Publicera så många värdbaserade pooler som du behöver för att hantera dina olika arbets belastningar.
* Ta med din egen bild för produktions arbets belastningar eller test från Azure-galleriet.
* Minska kostnaderna med poolbaserade resurser för flera sessioner. Med den nya funktionen Windows 10 Enterprise multi-session exklusiv till Windows Virtual Desktop och värd för fjärrskrivbordssession (RDSH) i Windows Server kan du minska antalet virtuella datorer och operativ system (OS) samtidigt som du fortfarande tillhandahålla samma resurser för dina användare.
* Ge individuella ägarskap via personliga (beständiga) Skriv bord.

Du kan distribuera och hantera virtuella skriv bord:

* Använd Windows Virtual Desktop PowerShell och REST Interfaces för att konfigurera värd grupper, skapa app-grupper, tilldela användare och publicera resurser.
* Publicera hela Skriv bordet eller enskilda fjärrappar från en enda adresspool, skapa enskilda grupp grupper för olika uppsättningar med användare eller till och med tilldela användare till flera app-grupper för att minska antalet avbildningar.
* När du hanterar din miljö kan du använda inbyggd delegerad åtkomst för att tilldela roller och samla in diagnostik för att förstå olika konfigurations-eller användar fel.
* Använd den nya diagnostik tjänsten för att felsöka fel.
* Hantera bara avbildningen och virtuella datorer, inte infrastrukturen. Du behöver inte personligen hantera de fjärr skrivbords roller som du gör med Fjärrskrivbordstjänster, bara de virtuella datorerna i din Azure-prenumeration.

Du kan också tilldela och ansluta användare till dina virtuella skriv bord:

* När de har tilldelats kan användarna starta en virtuell Windows-klient för att ansluta användare till publicerade Windows-datorer och program. Anslut från valfri enhet via antingen ett inbyggt program på din enhet eller på Windows Virtual Desktop HTML5 webb klient.
* Upprätta en säker anslutning till tjänsten på ett säkert sätt, så att du aldrig behöver lämna några öppna inkommande portar.

## <a name="requirements"></a>Krav

Det finns några saker du behöver för att konfigurera virtuella Windows-datorer och ansluta dina användare till sina Windows-datorer och program.

Vi planerar att lägga till stöd för följande operativ system, så se till att du har [lämpliga licenser](https://azure.microsoft.com/pricing/details/virtual-desktop/) för dina användare baserat på Skriv bordet och appar som du planerar att distribuera:

|OS|Nödvändig licens|
|---|---|
|Windows 10 Enterprise multi-session eller Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Klient åtkomst licens för fjärr skrivbords tjänster med Software Assurance|

Infrastrukturen behöver följande saker för att stödja Windows Virtual Desktop:

* En [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* En Windows Server-Active Directory som synkroniseras med Azure Active Directory. Detta kan aktive ras via:
  * Azure AD Connect
  * Azure AD Domain Services
* En Azure-prenumeration som innehåller ett virtuellt nätverk som antingen innehåller eller är anslutet till Windows Server-Active Directory
  
De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste vara:

* [Standard domän ansluten](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) eller [hybrid AD-ansluten](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuella datorer kan inte vara Azure AD-anslutna.
* Köra en av följande OS-avbildningar som [stöds](#supported-virtual-machine-os-image).

>[!NOTE]
>Om du behöver en Azure-prenumeration kan du [Registrera dig för en kostnads fri utvärderings version av en månad](https://azure.microsoft.com/free/). Om du använder den kostnads fria utvärderings versionen av Azure bör du använda Azure AD Domain Services för att hålla Windows Server-Active Directory synkroniserad med Azure Active Directory.

Windows Virtual Desktop består av Windows-datorer och appar som du levererar till användare och hanterings lösningen, som är värdbaserad som en tjänst på Azure av Microsoft. Under den offentliga för hands versionen kan Station ära datorer och appar distribueras på virtuella datorer i valfri Azure-region, och hanterings lösningen och data för dessa virtuella datorer finns i USA (USA, östra 2). Detta kan leda till att data överförs till USA medan du testar tjänsten under en offentlig för hands version. Vi börjar skala ut hanterings lösningen och data lokaliseringen till alla Azure-regioner som börjar med allmän tillgänglighet.

För bästa prestanda bör du kontrol lera att nätverket uppfyller följande krav:

* Svars tid för tur och retur från klientens nätverk till den Azure-region där värdbaserade pooler har distribuerats måste vara mindre än 150 ms.
* Nätverks trafiken kan flöda utanför lands-/region gränser när virtuella datorer som är värdar för Station ära datorer och appar ansluter till hanterings tjänsten.
* För att optimera för nätverks prestanda rekommenderar vi att de virtuella datorerna i samordnad i samma Azure-region som hanterings tjänsten.

## <a name="supported-remote-desktop-clients"></a>Fjärr skrivbords klienter som stöds

Följande fjärr skrivbords klienter stöder virtuellt skriv bord i Windows:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-image"></a>Operativ system avbildning för virtuella datorer som stöds

Windows Virtual Desktop stöder följande OS-avbildningar:

* Windows 10 Enterprise multi-session
* Windows Server 2016

## <a name="next-steps"></a>Nästa steg

För att komma igång måste du skapa en klient. Om du vill veta mer om hur du skapar en klient fortsätter du till självstudien för skapande av innehavare.

> [!div class="nextstepaction"]
> [Skapa en klient i för hands versionen av Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
