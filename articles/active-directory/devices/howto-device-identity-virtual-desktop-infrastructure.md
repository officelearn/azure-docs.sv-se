---
title: Enhets identitet och skriv bords virtualisering – Azure Active Directory
description: Lär dig hur VDI och Azure AD-enhets identiteter kan användas tillsammans
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dde9d8b50233c3c4033daf618e0e626c0174b0c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903161"
---
# <a name="device-identity-and-desktop-virtualization"></a>Enhets identitet och skriv bords virtualisering

Administratörer distribuerar ofta VDI-plattformar (Virtual Desktop Infrastructure) som är värd för Windows-operativsystem i sina organisationer. Administratörer distribuerar VDI till:

- Effektivisera hanteringen.
- Minska kostnaderna genom konsolidering och centralisering av resurser.
- Leverera slutanvändarnas rörlighet och friheten att komma åt virtuella skriv bord när som helst, var som helst, på valfri enhet.

Det finns två primära typer av virtuella skriv bord:

- Permanent
- Icke-beständig

I beständiga versioner används en unik Skriv bords avbildning för varje användare eller pool med användare. Dessa unika skriv bord kan anpassas och sparas för framtida användning. 

Icke-beständiga versioner använder en samling skriv bord som användare kan komma åt vid behov. Dessa icke-beständiga skriv bord återställs till sitt ursprungliga tillstånd när användaren loggar ut.

Den här artikeln beskriver Microsofts vägledning för administratörer om stöd för enhets identiteter och VDI. Mer information om enhets identitet finns i artikeln [Vad är en enhets identitet](overview.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Innan du konfigurerar enhets identiteter i Azure AD för din VDI-miljö kan du bekanta dig med de scenarier som stöds. Tabellen nedan visar vilka etablerings scenarier som stöds. Etableringen i den här kontexten innebär att en administratör kan konfigurera enhets identiteter i skala utan att behöva göra någon slut användar interaktion.

| Enhets identitets typ | Infrastruktur för identiteter | Windows-enheter | VDI-plattforms version | Stöds |
| --- | --- | --- | --- | --- |
| Hybrid Azure AD-ansluten | Externt | Windows-aktuell * * * * och Windows-äldre * * * * | Permanent | Ja |
|   |   | Windows-aktuell | Icke-beständig | Nej |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja |
|   | Hanterad * * | Windows aktuella och Windows-äldre | Permanent | Ja |
|   |   | Windows-aktuell | Icke-beständig | Nej |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja |
| Azure AD-ansluten | Federerade | Windows-aktuell | Permanent | Nej |
|   |   |   | Icke-beständig | Nej |
|   | Hanterade | Windows-aktuell | Permanent | Nej |
|   |   |   | Icke-beständig | Nej |
| Azure AD-registrerad | Federerade | Windows-aktuell | Permanent | Ej tillämpligt |
|   |   |   | Icke-beständig | Ej tillämpligt |
|   | Hanterade | Windows-aktuell | Permanent | Ej tillämpligt |
|   |   |   | Icke-beständig | Ej tillämpligt |

\*En infrastruktur miljö för **federerade** identiteter representerar en miljö med en identitets leverantör som AD FS eller andra IdP från tredje part.

\*\*En infrastruktur miljö för **hanterad** identitet representerar en miljö med Azure AD som den identitets leverantör som distribueras med antingen [PHS (Password hash Sync)](../hybrid/whatis-phs.md) eller [direktautentisering (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md).

\*\*\***Windows aktuella** enheter motsvarar Windows 10, windows Server 2016 och Windows Server 2019.

\*\*\*\***Windows-enheter på äldre nivå** representerar Windows 7, Windows 8,1, windows Server 2008 R2, windows Server 2012 och Windows Server 2012 R2. Information om support för Windows 7 finns i [Support för Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Information om support för Windows Server 2008 R2 finns i [förbereda för Windows server 2008-slut för support](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Microsofts vägledning

Administratörer ska referera till följande artiklar, baserat på deras identitets infrastruktur, för att lära dig hur du konfigurerar hybrid Azure AD Join.

- [Konfigurera hybrid Azure Active Directory anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybrid Azure Active Directorys anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

Om du förlitar dig på system förberedelse verktyget (sysprep.exe) och om du använder en avbildning med tidigare versioner än Windows 10 1809 för installation kontrollerar du att avbildningen inte är från en enhet som redan är registrerad i Azure AD som en hybrid Azure AD-ansluten.

Om du förlitar dig på en ögonblicks bild av en virtuell dator (VM) för att skapa ytterligare virtuella datorer, se till att ögonblicks bilden inte är från en virtuell dator som redan är registrerad i Azure AD som en hybrid Azure AD-anslutning.

Hybrid Azure AD-anslutning för icke-permanent VDI stöds inte i Windows aktuella versioner. När du distribuerar icke-permanent VDI för Windows-äldre bör IT-administratörerna betala nära uppmärksamheten för att hantera inaktuella enheter i Azure AD. Microsoft rekommenderar att IT-administratörer implementerar vägledningen nedan. Om du inte gör det kommer din katalog ha många inaktuella hybrid Azure AD-anslutna enheter som har registrerats från den icke-permanenta VDI-plattformen.

- Skapa och Använd ett prefix för visnings namnet på datorn som anger Skriv bordet som VDI-baserat.
- Implementera följande kommando som en del av utloggnings skriptet. Det här kommandot utlöser ett samtal med bästa prestanda till Azure AD för att ta bort enheten.
   - För Windows-enheter på äldre nivå – autoworkplace.exe/Leave
- Definiera och implementera process för att [Hantera inaktuella enheter](manage-stale-devices.md).
   - När du har en strategi för att identifiera dina icke-beständiga Azure AD-anslutna enheter kan du vara mer aggressiva vid rensning av dessa enheter för att se till att din katalog inte används med massor av inaktuella enheter.
 
## <a name="next-steps"></a>Nästa steg

[Konfigurera hybrid Azure Active Directorys anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
