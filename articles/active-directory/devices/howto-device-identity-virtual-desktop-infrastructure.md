---
title: Enhetsidentitet och skrivbordsvirtualisering – Azure Active Directory
description: Lär dig hur VDI- och Azure AD-enhetsidentiteter kan användas tillsammans
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
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900379"
---
# <a name="device-identity-and-desktop-virtualization"></a>Enhetsidentitet och skrivbordsvirtualisering

Administratörer distribuerar ofta VDI-plattformar (Virtual Desktop Infrastructure) som är värdar för Windows-operativsystem i sina organisationer. Administratörer distribuerar VDI till:

- Effektivisera hanteringen.
- Minska kostnaderna genom konsolidering och centralisering av resurser.
- Leverera slutanvändares rörlighet och friheten att komma åt virtuella skrivbord när som helst, var som helst, på vilken enhet som helst.

Det finns två primära typer av virtuella skrivbord:

- Permanent
- Icke-beständig

Beständiga versioner använder en unik skrivbordsavbildning för varje användare eller en pool av användare. Dessa unika skrivbord kan anpassas och sparas för framtida bruk. 

Icke-beständiga versioner använder en samling skrivbord som användarna kan komma åt efter behov. Dessa icke-beständiga skrivbord återgår till sitt ursprungliga tillstånd när användaren har loggat ut.

Den här artikeln kommer att omfatta Microsofts vägledning till administratörer om stöd för enhetsidentitet och VDI. Mer information om enhetsidentitet finns i artikeln [Vad är en enhetsidentitet](overview.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Innan du konfigurerar enhetsidentiteter i Azure AD för din VDI-miljö kan du bekanta dig med scenarierna som stöds. Tabellen nedan visar vilka etableringsscenarier som stöds. Etablering i den här kontexten innebär att en administratör kan konfigurera enhetsidentiteter i stor skala utan att kräva någon interaktion från slutanvändaren.

| Identitetstyp för enhet | Identitetsinfrastruktur | Windows-enheter | VDI-plattformsversion | Stöds |
| --- | --- | --- | --- | --- |
| Hybrid Azure AD-ansluten | Federerade* | Windows nuvarande*** och Windows på ned-nivå**** | Permanent | Ja |
|   |   | Windows nuvarande | Icke-beständig | Inga |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja |
|   | Hanterad** | Windows nuvarande och Windows på ned-nivå | Permanent | Ja |
|   |   | Windows nuvarande | Icke-beständig | Inga |
|   |   | Äldre Windows-enheter | Icke-beständig | Ja |
| Azure AD-ansluten | Federerade | Windows nuvarande | Permanent | Inga |
|   |   |   | Icke-beständig | Inga |
|   | Hanterade | Windows nuvarande | Permanent | Inga |
|   |   |   | Icke-beständig | Inga |
| Azure AD-registrerad | Federerade | Windows nuvarande | Permanent | Inga |
|   |   |   | Icke-beständig | Inga |
|   | Hanterade | Windows nuvarande | Permanent | Inga |
|   |   |   | Icke-beständig | Inga |

\*En **federerad identitetsinfrastrukturmiljö** representerar en miljö med en identitetsprovider som AD FS eller annan tredjeparts-IDP.

\*\*En miljö för **hanterad** identitetsinfrastruktur representerar en miljö med Azure AD som identitetsprovider som distribueras med antingen [lösenordshim-synkronisering (PHS)](../hybrid/whatis-phs.md) eller [genomströmningsautentisering (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md).

\*\*\***Windows-enheter** representerar Windows 10, Windows Server 2016 och Windows Server 2019.

\*\*\*\***Enheter på windows på lägre nivå** representerar Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Supportinformation om Windows 7 finns i [Support för Windows 7 avslutas](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Supportinformation om Windows Server 2008 R2 finns i [Förbereda för Windows Server 2008 på support](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Microsofts vägledning

Administratörer bör referera till följande artiklar, baserat på deras identitetsinfrastruktur, för att lära sig hur du konfigurerar hybrid Azure AD-koppling.

- [Konfigurera hybrid-Azure Active Directory-anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybrid-Azure Active Directory-anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

Om du förlitar dig på systemförberedelseverktyget (sysprep.exe) och om du använder en avbildning före Windows 10 1809 för installation kontrollerar du att avbildningen inte kommer från en enhet som redan är registrerad hos Azure AD som hybrid Azure AD-ansluten.

Om du förlitar dig på en ögonblicksbild av virtuella datorer (VM) för att skapa ytterligare virtuella datorer kontrollerar du att ögonblicksbilden inte kommer från en virtuell dator som redan är registrerad med Azure AD som Hybrid Azure AD-koppling.

När du distribuerar icke-beständig VDI bör IT-administratörer vara mycket uppmärksamma på att hantera inaktuella enheter i Azure AD. Microsoft rekommenderar att IT-administratörer implementerar vägledningen nedan. Om du inte gör det kan katalogen få många inaktuella Hybrid Azure AD-anslutna enheter som har registrerats från din icke-beständiga VDI-plattform.

- Skapa och använd ett prefix för visningsnamnet på datorn som anger skrivbordet som VDI-baserat.
- Implementera följande kommando som en del av utloggningsskriptet. Det här kommandot utlöser ett anrop till Azure AD för att ta bort enheten.
   - För Enheter på nednivå – autoworkplace.exe /leave
- Definiera och implementera processen för [hantering av inaktuella enheter](manage-stale-devices.md).
   - När du har en strategi för att identifiera dina icke-beständiga Hybrid Azure AD-anslutna enheter kan du vara mer aggressiv när du rensar dessa enheter för att säkerställa att katalogen inte förbrukas med många inaktuella enheter.
 
## <a name="next-steps"></a>Nästa steg

[Konfigurera hybrid-Azure Active Directory-anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
