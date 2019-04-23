---
title: Vad är Windows Virtual Desktop förhandsversionen?  – Azure
description: En översikt över Windows Virtual Desktop-förhandsversion.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 58fb1d73509ab52551bead4526dfb47588cf1ec6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004592"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Vad är Windows Virtual Desktop förhandsversionen? 

Nu tillgängligt i offentlig förhandsversion, Windows virtuella skrivbord Preview är en skrivbords- och app-virtualiseringstjänsten som körs i molnet.

Här är vad du kan göra när du kör virtuella Windows-skrivbordet på Azure:

* Konfigurera en Windows 10-distribution för flera sessioner som levererar en fullständig Windows 10 med skalbarhet
* Virtualisera Office 365 ProPlus och optimera köras i virtuella scenarier för flera användare
* Tillhandahålla virtuella skrivbord i Windows 7 med kostnadsfria utökade säkerhetsuppdateringar
* Ta med din befintliga Remote Desktop Services (RDS) och Windows Server-skrivbord och appar på alla datorer
* Virtualisera både skrivbord och appar
* Hantera Windows 10, Windows Server och Windows 7-skrivbord och appar med en enhetlig hanteringsupplevelse

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med virtuella Windows-skrivbordet, kan du ställa in en skalbar och flexibel miljö:

* Skapa en fullständig skrivbordsvirtualisering-miljö i din Azure-prenumeration utan att behöva köra eventuella ytterligare gateway-servrar.
* Publicera eftersom många värdar för pooler som du behöver för dina olika arbetsbelastningar.
* Ta med din egen avbildning för produktionsarbetsbelastningar eller testa från Azure-galleriet.
* Minska kostnaderna med resurser som pooler, flera session. Med den nya Windows 10 Enterprise flera session funktionen exklusivt för virtuella Windows-skrivbordet och Remote värd för fjärrskrivbordssession (RDSH)-rollen på Windows Server, kan du avsevärt minska antalet virtuella datorer och operativsystem (OS) overhead när du fortfarande att tillhandahålla samma resurser till dina användare.
* Ange enskilda ägarskap via personliga (permanent) skrivbord.

Du kan distribuera och hantera virtuella skrivbord:

* Använda Windows PowerShell för virtuella skrivbord och REST-gränssnitt för att konfigurera värd-pooler, skapa app-grupper, tilldela användare och publicera resurser.
* Publicera helt skrivbord eller enskilda remote appar från en enda värd-pool, skapa grupper med enskilda appar för olika uppsättningar med användare eller även tilldela användare till flera appgrupper för att minska antalet avbildningar.
* När du hanterar din miljö kan du använda inbyggda delegerad åtkomst att tilldela roller och samla in diagnostik för att förstå konfiguration eller användaren fel.
* Använd den nya diagnostik-tjänsten för att felsöka.
* Hantera endast avbildningen och virtuella datorer, inte på infrastrukturen. Du behöver inte personligen hantera fjärrskrivbord-roller som du gör med Fjärrskrivbordstjänster, bara de virtuella datorerna i din Azure-prenumeration.

Du kan också tilldela och ansluta användare till dina virtuella skrivbord:

* När de har tilldelats kan användarna starta alla virtuella skrivbordet i Windows-klienter för att ansluta användare till sina publicerade Windows-skrivbord och program. Anslut från valfri enhet via ett internt program på din enhet eller Windows Virtual Desktop HTML5-webbklienten.
* Upprätta en användare via omvänd anslutningar till tjänsten, så att du aldrig behöver lämna några ingående portar öppna.

## <a name="requirements"></a>Krav

Det finns några saker som du behöver för att konfigurera virtuella Windows-skrivbordet och ansluta dina användare till sina Windows-skrivbord och program.

Virtuella Windows-skrivbordet stöder för närvarande endast följande två OS:
- Windows 10 Enterprise flera session
- Windows Server 2016

Vi planerar att lägga till stöd för följande operativsystem, så se till att du har den [lämpliga licenser](https://azure.microsoft.com/pricing/details/virtual-desktop/) för dina användare baserat på skrivbordet och appar som du planerar att distribuera:

|Operativsystem|Licens som krävs|
|---|---|

| Flera sessionen för Windows 10 Enterprise eller Windows 10 Enterprise | Microsoft E3, E5, A3, A5, företag<br>Windows E3, E5, A3, A5| |Windows 7 Enterprise |Microsoft E3, E5, A3, A5, Business<br>Windows E3, E5, A3, A5 | | Windows Server 2012 R2, 2016, 2019 | RDS-klientåtkomstlicens (CAL) med Software Assurance |

Din infrastruktur behöver följande för virtuella Windows-skrivbordet:

* An [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* En Windows Server Active Directory synkroniserat med Azure Active Directory. Det här kan aktiveras via:
  * Azure AD Connect
  * Azure AD Domain Services
* En Azure-prenumeration, som innehåller ett virtuellt nätverk som innehåller eller är ansluten till Windows Server Active Directory
  
Azure-datorer som du skapar för virtuella Windows-skrivbordet måste vara:

* [Standard-domänanslutna](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) eller [Hybrid AD-anslutna](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuella datorer får inte vara Azure AD-anslutning.
* Kör något av följande stöds OS-avbildningar:
  * Windows 10 Enterprise flera session
  * Windows Server 2016

>[!NOTE]
>Om du behöver en Azure-prenumeration kan du [registrera dig för en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/). Om du använder en kostnadsfri utvärderingsversion av Azure, bör du använda Azure AD Domain Services för att synkronisera dina Windows Server Active Directory med Azure Active Directory.

Virtuella Windows-skrivbordet består av Windows-skrivbord och appar som du leverera till användare och den hanteringslösning som tillhandahålls av Microsoft som en tjänst på Azure. Allmänt tillgängliga förhandsversionen skrivbord och appar kan distribueras på virtuella datorer (VM) i alla Azure-regioner och hanteringslösningen och data för dessa virtuella datorer kommer att finnas i USA (USA, Öst 2 region). Det kan resultera i dataöverföringen till USA medan du testa den offentliga förhandsversionen. Vi börjar att skala ut management lokalisering för lösningen och data till alla Azure-regioner med början vid allmän tillgänglighet.

För bästa prestanda bör du kontrollera nätverket uppfyller följande krav:

* Tidszonsbevarande (RTT) svarstid från klientens nätverk till Azure-region där värden pooler har distribuerats bör vara mindre än 150 ms.
* Nätverkstrafik kan flöda utanför land kantlinjer när virtuella datorer som är värdar för skrivbord och appar som ansluter till management-tjänsten.
* Optimera nätverksprestanda, rekommenderar vi att värd för fjärrskrivbordssession virtuella datorer är samordnad i samma Azure-region som management-tjänsten.

## <a name="provide-feedback"></a>Ge feedback

Gå till den [Windows Desktop Tech-Community virtuella](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) att diskutera virtuellt skrivbord i Windows-tjänsten med produktteamet och aktiva community-medlemmar. Vi inte är för närvarande tar supportärenden när virtuella Windows-skrivbordet är i förhandsversionen.

## <a name="next-steps"></a>Nästa steg

För att komma igång, måste du skapa en klient. Om du vill veta mer om hur du skapar en klient kan du fortsätta till självstudien för klient-skapa.

> [!div class="nextstepaction"]
> [Skapa en klient i förhandsversion för virtuella skrivbord i Windows](tenant-setup-azure-active-directory.md)
