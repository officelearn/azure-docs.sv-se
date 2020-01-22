---
title: Vad är Windows Virtual Desktop? – Azure
description: En översikt över virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 01/21/2020
ms.author: helohr
ms.openlocfilehash: 44430f5a150952ba7cfc32b3e54d004cb0d0b761
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312353"
---
# <a name="what-is-windows-virtual-desktop"></a>Vad är Windows Virtual Desktop? 

Windows Virtual Desktop är en Skriv bords-och app Virtualization-tjänst som körs i molnet.

Det här kan du göra när du kör Windows Virtual Desktop på Azure:

* Konfigurera en Windows 10-distribution med flera sessioner som ger en fullständig Windows 10 med skalbarhet
* Virtualisera Office 365 ProPlus och optimera det för att köras i virtuella scenarier med flera användare
* Tillhandahålla virtuella Windows 7-datorer med kostnads fria utökade säkerhets uppdateringar
* Ta med dina befintliga Fjärrskrivbordstjänster (RDS) och Windows Server-datorer och appar till valfri dator
* Virtualisera både Station ära datorer och appar
* Hantera Windows 10-, Windows Server-och Windows 7-datorer och-appar med en enhetlig hanterings upplevelse

## <a name="introductory-video"></a>Introduktions video

Lär dig om virtuella Windows-datorer, varför det är unikt och vad som är nytt i den här videon:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Mer information om virtuella Windows-datorer finns i [vår spelnings lista](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

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
* En Windows Server-Active Directory som synkroniseras med Azure Active Directory. Du kan konfigurera detta med något av följande:
  * Azure AD Connect (för Hybrid organisationer)
  * Azure AD Domain Services (för Hybrid-eller moln organisationer)
* En Azure-prenumeration som innehåller ett virtuellt nätverk som antingen innehåller eller är ansluten till Windows Server-Active Directory
  
De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste vara:

* [Standard domän ansluten](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) eller [hybrid AD-ansluten](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuella datorer kan inte vara Azure AD-anslutna.
* Köra en av följande [OS-avbildningar som stöds](#supported-virtual-machine-os-images).

>[!NOTE]
>Om du behöver en Azure-prenumeration kan du [Registrera dig för en kostnads fri utvärderings version av en månad](https://azure.microsoft.com/free/). Om du använder den kostnads fria utvärderings versionen av Azure bör du använda Azure AD Domain Services för att hålla Windows Server-Active Directory synkroniserad med Azure Active Directory.

De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste ha åtkomst till följande URL: er:

|Adress|Utgående port|Syfte|
|---|---|---|
|*. wvd.microsoft.com|TCP-port 443|Tjänst trafik|
|*.blob.core.windows.net|TCP-port 443|Agent, SXS stack-uppdateringar och agent trafik|
|*.core.windows.net|TCP-port 443|Agent trafik|
|*.servicebus.windows.net|TCP-port 443|Agent trafik|
|prod.warmpath.msftcloudes.com|TCP-port 443|Agent trafik|
|catalogartifact.azureedge.net|TCP-port 443|Azure Marketplace|
|kms.core.windows.net|TCP-port 1688|Aktivering av Windows 10|

>[!IMPORTANT]
>Att öppna dessa URL: er är viktigt för en tillförlitlig Windows-distribution av virtuella skriv bord. Det finns inte stöd för att blockera åtkomst till dessa URL: er och det påverkar service funktionerna. Dessa URL: er motsvarar bara Windows virtuella Skriv bords platser och resurser och inkluderar inte URL: er för andra tjänster som Azure AD.

>[!NOTE]
>Du måste använda jokertecknet (*) för URL: er som involverar tjänst trafiken. Om du inte vill använda * för agent-relaterad trafik så här hittar du URL: erna utan jokertecken:
>
>1. Registrera dina virtuella datorer på Windows-poolen för virtuella skriv bord.
>2. Öppna **logg boken** och navigera till **Windows** > **program loggar** och leta efter händelse-ID 3712.
>3. Vitlista de URL: er som du hittar under händelse-ID 3712. URL: erna under händelse-ID 3712 är landsspecifika. Du måste upprepa vit listning-processen med relevanta URL: er för varje region som du vill distribuera dina virtuella datorer i.

Windows Virtual Desktop består av Windows-datorer och appar som du levererar till användare och hanterings lösningen, som är värdbaserad som en tjänst på Azure av Microsoft. Skriv bord och appar kan distribueras på virtuella datorer i valfri Azure-region och hanterings lösningen och data för dessa virtuella datorer finns i USA. Detta kan leda till att data överförs till USA.

För bästa prestanda bör du kontrol lera att nätverket uppfyller följande krav:

* Svars tid för tur och retur från klientens nätverk till den Azure-region där värdbaserade pooler har distribuerats måste vara mindre än 150 ms.
* Nätverks trafiken kan flöda utanför lands-/region gränser när virtuella datorer som är värdar för Station ära datorer och appar ansluter till hanterings tjänsten.
* För att optimera för nätverks prestanda rekommenderar vi att de virtuella datorerna i samordnad i samma Azure-region som hanterings tjänsten.

## <a name="supported-remote-desktop-clients"></a>Fjärr skrivbords klienter som stöds

Följande fjärr skrivbords klienter stöder virtuellt skriv bord i Windows:

* [Windows](connect-windows-7-and-10.md)
* [Webb](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (för hands version)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>OS-avbildningar för virtuella datorer som stöds

Windows Virtual Desktop stöder följande x64-operativ system avbildningar:

* Windows 10 Enterprise multi-session, version 1809 eller senare
* Windows 10 Enterprise, version 1809 eller senare
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop stöder inte x86 (32-bitars), Windows 10 Enterprise N eller Windows 10 Enterprise KN-operativsystem avbildningar.

Tillgängliga alternativ för Automation och distribution beror på vilket operativ system och vilken version du väljer, som du ser i följande tabell: 

|Operativsystem|Azures avbildnings Galleri|Manuell distribution av virtuella datorer|Azure Resource Manager mall-integrering|Etablera värdbaserade pooler på Azure Marketplace|Uppdateringar av Windows Virtual Desktop agent|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 multi-session, version 1903|Ja|Ja|Ja|Ja|Automatisk|
|Windows 10 multi-session, version 1809|Ja|Ja|Inga|Inga|Automatisk|
|Windows 10 Enterprise, version 1903|Ja|Ja|Ja|Ja|Automatisk|
|Windows 10 Enterprise, version 1809|Ja|Ja|Inga|Inga|Automatisk|
|Windows 7 Enterprise|Ja|Ja|Inga|Inga|Manuellt|
|Windows Server 2019|Ja|Ja|Inga|Inga|Automatisk|
|Windows Server 2016|Ja|Ja|Ja|Ja|Automatisk|
|Windows Server 2012 R2|Ja|Ja|Inga|Inga|Automatisk|

## <a name="next-steps"></a>Nästa steg

För att komma igång måste du skapa en klient. Om du vill veta mer om hur du skapar en klient fortsätter du till självstudien för skapande av innehavare.

> [!div class="nextstepaction"]
> [Skapa en klientorganisation i Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
