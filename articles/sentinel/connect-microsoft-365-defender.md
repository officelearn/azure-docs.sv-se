---
title: Anslut Microsoft 365 Defender rå data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du matar in rå händelse data från Microsoft 365 Defender till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 756c245fe06ae81545a125dd98f30fb27fdff2dd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655587"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Anslut data från Microsoft 365 Defender till Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** kallades tidigare **Microsoft Threat Protection** eller **MTP**.
>
> **Microsoft Defender för slut punkten** kallades tidigare **Microsoft Defender Advanced Threat Protection** eller **MDATP**.
>
> Du kan se att de gamla namnen fortfarande används under en viss tids period.

## <a name="background"></a>Bakgrund

Med den nya [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) -anslutaren kan du strömma **avancerade jakt** loggar – en typ av rå händelse data – från Microsoft 365 Defender till Azure Sentinel. 

Med integreringen av [Microsoft Defender för slut punkt (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) i säkerhets paraplyen för Microsoft 365 Defender kan du nu samla in dina Microsoft Defender för slut punkts [avancerade jakt](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) händelser med hjälp av Microsoft 365 Defender-anslutningen och strömma dem direkt i nya syfte-skapade tabeller i Azure Sentinel-arbetsytan. Tabellerna bygger på samma schema som används i Microsoft 365 Defender-portalen, vilket ger dig fullständig åtkomst till hela uppsättningen med avancerade jakt loggar och gör att du kan göra följande:

- Kopiera enkelt dina befintliga Microsoft Defender ATP-frågor för avancerad jakt till Azure Sentinel.

- Använd obehandlade händelse loggar för att ge ytterligare insikter om dina aviseringar, jakt och undersökning och korrelera händelser med data från ytterligare data källor i Azure Sentinel.

- Lagra loggarna med ökad kvarhållning bortom Microsoft Defender för slut punkt eller Microsoft 365 Defender standard kvarhållning på 30 dagar. Du kan göra det genom att konfigurera kvarhållning av din arbets yta eller genom att konfigurera kvarhållning per tabell i Log Analytics.

> [!IMPORTANT]
>
> Microsoft 365 Defender-anslutaren är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en giltig licens för Microsoft Defender för slut punkt enligt beskrivningen i [Konfigurera Microsoft Defender för slut punkts distribution](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Användaren måste tilldelas rollen global administratör på klienten (i Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Anslut till Microsoft 365 Defender

Om Microsoft Defender för slut punkt distribueras och data matas in kan händelse loggarna enkelt strömmas till Azure Sentinel.

1. I Azure Sentinel väljer du **data kopplingar**, väljer **Microsoft 365 Defender (förhands granskning)** från galleriet och väljer **Öppna kopplings sida**.

1. Följande typer av händelser kan samlas in från motsvarande avancerade jakt tabeller. Markera kryss rutorna för de händelse typer som du vill samla in:

    | Händelse typ | Tabellnamn |
    |-|-|
    | Dator information (inklusive OS-information) | DeviceInfo |
    | Nätverks egenskaper för datorer | DeviceNetworkInfo |
    | Skapande av processer och relaterade händelser | DeviceProcessEvents |
    | Nätverks anslutning och relaterade händelser | DeviceNetworkEvents |
    | Skapa, ändra och andra fil system händelser | DeviceFileEvents |
    | Skapa och ändra register poster | DeviceRegistryEvents |
    | Inloggningar och andra autentiserings händelser | DeviceLogonEvents |
    | DLL inläsning av händelser | DeviceImageLoadEvents |
    | Ytterligare händelse typer | DeviceEvents |
    |

1. Klicka på **tillämpa ändringar**. 

1. Om du vill fråga avancerade jakt tabeller i Log Analytics anger du tabell namnet från listan ovan i frågefönstret.

## <a name="verify-data-ingestion"></a>Verifiera data inmatning

Data grafen på kopplings sidan visar att du matar in data. Du ser att det visar en enskild rad som sammanställer händelse volymen i alla aktiverade tabeller. När du har aktiverat anslutnings tjänsten kan du använda följande KQL-fråga för att generera ett liknande diagram över händelse volymen för en enskild tabell (ändra *DeviceEvents* -tabellen till önskad tabell som du väljer):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

På fliken **Nästa steg** hittar du några exempel frågor som har inkluderats. Du kan köra dem på platsen eller ändra och spara dem.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du hämtar rå data från Microsoft Defender för slut punkt till Azure Sentinel med hjälp av Microsoft 365 Defender-anslutaren. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).