---
title: Vad är Windows Virtual Desktop? – Azure
description: En översikt över Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 927696d029bf1b8742dc0001e03799322f368191
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261728"
---
# <a name="what-is-windows-virtual-desktop"></a>Vad är Windows Virtual Desktop? 

Windows Virtual Desktop är en skrivbords- och appvirtualiseringstjänst som körs i molnet.

Så här kan du göra när du kör Windows Virtual Desktop på Azure:

* Konfigurera en Windows 10-distribution med flera sessions som ger ett fullständigt Windows 10 med skalbarhet
* Virtualisera Office 365 ProPlus och optimera det så att det körs i virtuella scenarier med flera användare
* Ge virtuella Windows 7-skrivbord kostnadsfria utökade säkerhetsuppdateringar
* Ta med dina befintliga rds-datorer (Remote Desktop Services) och Windows Server till valfri dator
* Virtualisera både skrivbord och appar
* Hantera stationära datorer och appar för Windows 10, Windows Server och Windows 7 med en enhetlig hanteringsupplevelse

## <a name="introductory-video"></a>Introduktionsvideo

Lär dig mer om Windows Virtual Desktop, varför det är unikt och vad som är nytt i den här videon:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Mer information om Windows Virtual Desktop finns i [vår spellista](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med Windows Virtual Desktop kan du konfigurera en skalbar och flexibel miljö:

* Skapa en fullständig virtualiseringsmiljö för skrivbordet i din Azure-prenumeration utan att behöva köra några ytterligare gatewayservrar.
* Publicera så många värdpooler som du behöver för att hantera dina olika arbetsbelastningar.
* Ta med din egen avbildning för produktionsarbetsbelastningar eller testa från Azure Gallery.
* Minska kostnaderna med poolade resurser med flera sessioner. Med den nya multisessionsfunktionen för Windows 10 Enterprise exklusivt för windows virtual desktop och remote desktop session host (RDSH) roll på Windows Server kan du kraftigt minska antalet virtuella datorer och operativsystem (OS) omkostnader samtidigt som du tillhandahåller samma resurser till användarna.
* Ge individuellt ägande via personliga (beständiga) skrivbord.

Du kan distribuera och hantera virtuella skrivbord:

* Använd Windows Virtual Desktop PowerShell- och REST-gränssnitt för att konfigurera värdpoolerna, skapa appgrupper, tilldela användare och publicera resurser.
* Publicera fullständiga skrivbordsappar eller enskilda fjärrappar från en enda värdpool, skapa enskilda appgrupper för olika användargrupper eller tilldela till och med användare till flera appgrupper för att minska antalet bilder.
* När du hanterar din miljö använder du inbyggd delegerad åtkomst för att tilldela roller och samla in diagnostik för att förstå olika konfigurations- eller användarfel.
* Använd den nya diagnostiktjänsten för att felsöka fel.
* Hantera endast avbildningen och virtuella datorer, inte infrastrukturen. Du behöver inte hantera fjärrskrivbordsrollerna som du gör med Fjärrskrivbordstjänster, bara de virtuella datorerna i din Azure-prenumeration.

Du kan också tilldela och ansluta användare till dina virtuella skrivbord:

* När användarna har tilldelats kan de starta alla Windows Virtual Desktop-klienter för att ansluta användare till sina publicerade Windows-datorer och -program. Anslut från valfri enhet via antingen ett inbyggt program på enheten eller windows virtual desktop HTML5-webbklienten.
* Upprätta användare på ett säkert sätt via omvända anslutningar till tjänsten, så du behöver aldrig lämna några inkommande portar öppna.

## <a name="requirements"></a>Krav

Det finns några saker du behöver för att konfigurera Windows Virtual Desktop och ansluta användarna till sina Windows-datorer och -program.

Vi planerar att lägga till stöd för följande OSes, så se till att du har [rätt licenser](https://azure.microsoft.com/pricing/details/virtual-desktop/) för dina användare baserat på skrivbordet och appar som du planerar att distribuera:

|Operativsystem|Obligatorisk licens|
|---|---|
|Windows 10 Enterprise multi-session eller Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Företag<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Företag<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS-klientåtkomstlicens (CAL) med Software Assurance|

Din infrastruktur behöver följande saker för att stödja Windows Virtual Desktop:

* En [Azure Active Directory](/azure/active-directory/)
* En Active Directory för Windows Server synkroniseras med Azure Active Directory. Du kan konfigurera detta med något av följande:
  * Azure AD Connect (för hybridorganisationer)
  * Azure AD Domain Services (för hybrid- eller molnorganisationer)
* En Azure-prenumeration som innehåller ett virtuellt nätverk som antingen innehåller eller är ansluten till Active Directory för Windows Server
  
De virtuella Azure-datorer som du skapar för Windows Virtual Desktop måste vara:

* [Standarddomänansluten](../active-directory-domain-services/active-directory-ds-comparison.md) eller [Hybrid AD-anslutna](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuella datorer kan inte Azure AD-anslutna.
* Köra en av följande [OS-bilder som stöds](#supported-virtual-machine-os-images).

>[!NOTE]
>Om du behöver en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/)på en månad . Om du använder den kostnadsfria utvärderingsversionen av Azure bör du använda Azure AD Domain Services för att hålla Din Active Directory i Windows Server synkroniserad med Azure Active Directory.

De virtuella Azure-datorer som du skapar för Windows Virtual Desktop måste ha åtkomst till följande webbadresser:

|Adress|Utgående TCP-port|Syfte|Service Tag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Trafikerar tjänste-|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Agent- och SXS-stackuppdateringar|AzureCloud|
|*.core.windows.net|443|Agent trafik|AzureCloud|
|*.servicebus.windows.net|443|Agent trafik|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Agent trafik|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-aktivering|Internet|

>[!IMPORTANT]
>Vi rekommenderar att du använder tjänsttaggarna i stället för webbadresser i de flesta fall för att förhindra serviceproblem. Att avblockera dessa webbadresser är viktigt för en tillförlitlig Windows Virtual Desktop-distribution. Att blockera åtkomst till dessa url:er stöds inte och påverkar tjänstens funktioner. Dessa url:er motsvarar bara Windows virtuella skrivbordsplatser och resurser och innehåller inte webbadresser för andra tjänster som Azure Active Directory.

I följande tabell visas valfria url:er som dina virtuella Azure-datorer kan ha åtkomst till:

|Adress|Utgående TCP-port|Syfte|Service Tag|
|---|---|---|---|
|*.microsoftonline.com|443|Autentisering till MS Online Services|Inget|
|*.events.data.microsoft.com|443|Telemetritjänst|Inget|
|www.msftconnecttest.com|443|Identifierar om operativsystemet är anslutet till internet|Inget|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Inget|
|login.windows.net|443|Logga in på MS Online Services, Office 365|Inget|
|*.sfx.ms|443|Uppdateringar för OneDrive-klientprogram|Inget|
|*.digicert.com|443|Kontroll av återkallade certifikat|Inget|


>[!NOTE]
>Windows Virtual Desktop har för närvarande ingen lista över IP-adressintervall som du kan vitlista för att tillåta nätverkstrafik. Vi stöder bara vitlistning av specifika webbadresser just nu.
>
>En lista över Office-relaterade URL:er, inklusive nödvändiga Azure Active Directory-relaterade URL:er, finns [i Url:er och IP-adressintervall för Office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Du måste använda jokertecknet (*) för webbadresser som involverar servicetrafik. Om du föredrar att inte använda * för agentrelaterad trafik gör du så här för att hitta webbadresserna utan jokertecken:
>
>1. Registrera dina virtuella datorer i värdpoolen för Virtuellt skrivbord i Windows.
>2. Öppna **Loggboken** och navigera till **Windows loggar** > **Program** > **WVD-Agent** och leta efter händelse-ID 3702.
>3. Vitlista webbadresserna som du hittar under händelse-ID 3702. Url:erna under händelse-ID 3702 är regionspecifika. Du måste upprepa vitlistningsprocessen med relevanta webbadresser för varje region som du vill distribuera dina virtuella datorer i.

Windows Virtual Desktop består av de Windows-datorer och -appar som du levererar till användare och hanteringslösningen, som är värd för som en tjänst på Azure av Microsoft. Stationära datorer och appar kan distribueras på virtuella datorer i alla Azure-regioner, och hanteringslösningen och data för dessa virtuella datorer finns i USA. Detta kan resultera i dataöverföring till USA.

För bästa prestanda, se till att nätverket uppfyller följande krav:

* Svarstid för rundresa (RTT) från klientens nätverk till Azure-regionen där värdpooler har distribuerats bör vara mindre än 150 ms.
* Nätverkstrafik kan flöda utanför lands-/regiongränser när virtuella datorer som är värdar för stationära datorer och appar ansluter till hanteringstjänsten.
* För att optimera för nätverksprestanda rekommenderar vi att sessionsvärdens virtuella datorer samlokaliserades i samma Azure-region som hanteringstjänsten.

## <a name="supported-remote-desktop-clients"></a>Klienter som stöds av fjärrskrivbord

Följande klienter för fjärrskrivbord stöder Windows Virtual Desktop:

* [Windows-skrivbordet](connect-windows-7-and-10.md)
* [Webb](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (förhandsgranskning)](connect-android.md)

> [!IMPORTANT]
> Windows Virtual Desktop stöder inte RADC-klienten (RemoteApp and Desktop Connections) eller MSTSC-klienten (Remote Desktop Connection).

> [!IMPORTANT]
> Windows Virtual Desktop stöder för närvarande inte klienten för fjärrskrivbord från Windows Store. Stöd för den här klienten kommer att läggas till i en framtida version.

Fjärrskrivbordsklienterna måste ha åtkomst till följande webbadresser:

|Adress|Utgående TCP-port|Syfte|Klient(er)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Trafikerar tjänste-|Alla|
|*.servicebus.windows.net|443|Felsöka data|Alla|
|go.microsoft.com|443|Microsoft FWLinks|Alla|
|aka.ms|443|Microsoft URL förkortare|Alla|
|docs.microsoft.com|443|Dokumentation|Alla|
|privacy.microsoft.com|443|Sekretesspolicy|Alla|
|query.prod.cms.rt.microsoft.com|443|Klientuppdateringar|Windows-skrivbordet|

>[!IMPORTANT]
>Att öppna dessa webbadresser är viktigt för en tillförlitlig klientupplevelse. Att blockera åtkomst till dessa url:er stöds inte och påverkar tjänstens funktioner. Dessa URL:er motsvarar bara klientplatser och resurser och innehåller inte webbadresser för andra tjänster som Azure Active Directory.

## <a name="supported-virtual-machine-os-images"></a>Os-avbildningar som stöds av virtuella datorer

Windows Virtual Desktop stöder följande x64-operativsystemavbildningar:

* Windows 10 Enterprise multi-session, version 1809 eller senare
* Windows 10 Enterprise, version 1809 eller senare
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop stöder inte x86 (32-bitars), Windows 10 Enterprise N eller Windows 10 Enterprise KN operativsystemavbildningar. Windows 7 stöder inte heller några VHD- eller VHDX-baserade profillösningar som finns på hanterad Azure Storage på grund av en sektorstorleksbegränsning.

Tillgängliga automatiserings- och distributionsalternativ beror på vilket operativsystem och vilken version du väljer, som visas i följande tabell: 

|Operativsystem|Azure-avbildningsgalleri|Manuell vm-distribution|Integrering av Azure Resource Manager-mall|Etableringsvärdpooler på Azure Marketplace|Uppdateringar av Windows Virtual Desktop Agent|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 flera session, version 1903|Ja|Ja|Ja|Ja|Automatisk|
|Windows 10 flera session, version 1809|Ja|Ja|Inga|Inga|Automatisk|
|Windows 10 Enterprise, version 1903|Ja|Ja|Ja|Ja|Automatisk|
|Windows 10 Enterprise, version 1809|Ja|Ja|Inga|Inga|Automatisk|
|Windows 7 Enterprise|Ja|Ja|Inga|Inga|Manuell|
|Windows Server 2019|Ja|Ja|Inga|Inga|Automatisk|
|Windows Server 2016|Ja|Ja|Ja|Ja|Automatisk|
|Windows Server 2012 R2|Ja|Ja|Inga|Inga|Automatisk|

## <a name="next-steps"></a>Nästa steg

För att komma igång måste du skapa en klient. Om du vill veta mer om hur du skapar en klient fortsätter du till självstudien för att skapa klient.

> [!div class="nextstepaction"]
> [Skapa en klientorganisation i Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
