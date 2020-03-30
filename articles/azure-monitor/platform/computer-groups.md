---
title: Datorgrupper i Azure Monitor-loggfrågor | Microsoft-dokument
description: Med datorgrupper i Azure Monitor kan du begränsa loggfrågor till en viss uppsättning datorer.  I den här artikeln beskrivs de olika metoder som du kan använda för att skapa datorgrupper och hur du använder dem i en loggfråga.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274780"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Datorgrupper i Azure Monitor-loggfrågor
Med datorgrupper i Azure Monitor kan du begränsa [loggfrågor](../log-query/log-query-overview.md) till en viss uppsättning datorer.  Varje grupp fylls i med datorer som antingen använder en fråga som du definierar eller genom att importera grupper från olika källor.  När gruppen ingår i en loggfråga begränsas resultaten till poster som matchar datorerna i gruppen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Skapa en datorgrupp
Du kan skapa en datorgrupp i Azure Monitor med någon av metoderna i följande tabell.  Närmare uppgifter om varje metod finns i avsnitten nedan. 

| Metod | Beskrivning |
|:--- |:--- |
| Loggfråga |Skapa en loggfråga som returnerar en lista över datorer. |
| Loggsöknings-API |Använd loggsök-API:et för att programmässigt skapa en datorgrupp baserat på resultatet av en loggfråga. |
| Active Directory |Genomsök automatiskt gruppmedlemskapet för alla agentdatorer som är medlemmar i en Active Directory-domän och skapa en grupp i Azure Monitor för varje säkerhetsgrupp. (Endast Windows-datorer)|
| Configuration Manager | Importera samlingar från Microsoft Endpoint Configuration Manager och skapa en grupp i Azure Monitor för varje. |
| Windows Server Update Services |Sök automatiskt igenom WSUS-servrar eller klienter efter inriktningsgrupper och skapa en grupp i Azure Monitor för varje. |

### <a name="log-query"></a>Loggfråga
Datorgrupper som skapats från en loggfråga innehåller alla datorer som returneras av en fråga som du definierar.  Den här frågan körs varje gång datorgruppen används så att alla ändringar sedan gruppen skapades återspeglas.  

Du kan använda valfri fråga för en datorgrupp, men den `distinct Computer`måste returnera en avgränsad uppsättning datorer med hjälp av .  Följande är en typisk exempelfråga som du kan använda för som en datorgrupp.

    Heartbeat | where Computer contains "srv" | distinct Computer

Använd följande procedur för att skapa en datorgrupp från en loggsökning i Azure-portalen.

1. Klicka på **Loggar** på **Azure Monitor-menyn** i Azure-portalen.
1. Skapa och kör en fråga som returnerar de datorer som du vill använda i gruppen.
1. Klicka på **Spara** högst upp på skärmen.
1. Ändra **Spara som** **funktion** och välj Spara frågan som **en datorgrupp**.
1. Ange värden för varje egenskap för den datorgrupp som beskrivs i tabellen och klicka på **Spara**.

I följande tabell beskrivs de egenskaper som definierar en datorgrupp.

| Egenskap | Beskrivning |
|:---|:---|
| Namn   | Namn på frågan som ska visas i portalen. |
| Funktionsalias | Ett unikt alias som används för att identifiera datorgruppen i en fråga. |
| Kategori       | Kategori för att ordna frågorna i portalen. |


### <a name="active-directory"></a>Active Directory
När du konfigurerar Azure Monitor för att importera Active Directory-gruppmedlemskap analyseras gruppmedlemskapet för alla Windows-domäner som är anslutna till datorer med Log Analytics-agenten.  En datorgrupp skapas i Azure Monitor för varje säkerhetsgrupp i Active Directory och varje Windows-dator läggs till i de datorgrupper som motsvarar de säkerhetsgrupper de är medlemmar i.  Detta medlemskap uppdateras kontinuerligt var 4:e timme.  

> [!NOTE]
> Importerade Active Directory-grupper innehåller bara Windows-datorer.

Du konfigurerar Azure Monitor för att importera Active Directory-säkerhetsgrupper från **avancerade inställningar** på logganalysarbetsytan i Azure-portalen.  Välj **Datorgrupper**, **Active Directory**och importera sedan Medlemskap i Active **Directory-grupper från datorer**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från Active Directory](media/computer-groups/configure-activedirectory.png)

När grupper har importerats visar menyn antalet datorer med gruppmedlemskap som har identifierats och antalet importerade grupper.  Du kan klicka på någon av dessa länkar för att returnera **ComputerGroup-posterna** med den här informationen.

### <a name="windows-server-update-service"></a>Windows Server Update Service
När du konfigurerar Azure Monitor för att importera WSUS-gruppmedlemskap analyseras målgruppsmedlemskapet för alla datorer med Log Analytics-agenten.  Om du använder inriktning på klientsidan har alla datorer som är anslutna till Azure Monitor och som ingår i alla WSUS-inriktningsgrupper sitt gruppmedlemskap importerat till Azure Monitor. Om du använder inriktning på serversidan bör Log Analytics-agenten installeras på WSUS-servern för att gruppmedlemskapsinformationen ska importeras till Azure Monitor.  Detta medlemskap uppdateras kontinuerligt var 4:e timme. 

Du konfigurerar Azure Monitor för att importera WSUS-grupper från **avancerade inställningar** i logganalysarbetsytan i Azure-portalen.  Välj **Datorgrupper**, **WSUS**och importera sedan **WSUS-gruppmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från WSUS](media/computer-groups/configure-wsus.png)

När grupper har importerats visar menyn antalet datorer med gruppmedlemskap som har identifierats och antalet importerade grupper.  Du kan klicka på någon av dessa länkar för att returnera **ComputerGroup-posterna** med den här informationen.

### <a name="configuration-manager"></a>Configuration Manager
När du konfigurerar Azure Monitor för att importera Configuration Manager-samlingsmedlemskap skapas en datorgrupp för varje samling.  Information om insamlingsmedlemskap hämtas var tredje timme för att hålla datorgrupperna aktuella. 

Innan du kan importera Configuration Manager-samlingar måste du [ansluta Configuration Manager till Azure Monitor](collect-sccm.md).  

![Datorgrupper från SCCM](media/computer-groups/configure-sccm.png)

När samlingar har importerats visar menyn antalet datorer med gruppmedlemskap som har identifierats och antalet importerade grupper.  Du kan klicka på någon av dessa länkar för att returnera **ComputerGroup-posterna** med den här informationen.

## <a name="managing-computer-groups"></a>Hantera datorgrupper
Du kan visa datorgrupper som har skapats från en loggfråga eller API:et för loggsökning från **avancerade inställningar** på logganalysarbetsytan i Azure-portalen.  Välj **Datorgrupper** och spara **sedan grupper**.  

Klicka på **krysset** i kolumnen **Ta bort** om du vill ta bort datorgruppen.  Klicka på ikonen **Visa medlemmar** för en grupp om du vill köra gruppens loggsökning som returnerar medlemmarna.  Du kan inte ändra en datorgrupp utan måste i stället ta bort och sedan återskapa den med de ändrade inställningarna.

![Sparade datorgrupper](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Använda en datorgrupp i en loggfråga
Du använder en datorgrupp som skapats från en loggfråga i en fråga genom att behandla dess alias som en funktion, vanligtvis med följande syntax:

  `Table | where Computer in (ComputerGroup)`

Du kan till exempel använda följande för att returnera UpdateSummary-poster för endast datorer i en datorgrupp som kallas mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Importerade datorgrupper och deras medföljande datorer lagras i tabellen **ComputerGroup.**  Följande fråga returnerar till exempel en lista över datorer i gruppen Domändatorer från Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Följande fråga returnerar UpdateSummary-poster för endast datorer i Domändatorer.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Poster för datorgrupp
En post skapas på arbetsytan Log Analytics för varje datorgruppmedlemskap som skapats från Active Directory eller WSUS.  Dessa poster har en typ av **ComputerGroup** och har egenskaperna i följande tabell.  Poster skapas inte för datorgrupper baserat på loggfrågor.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |*Datorgrupp* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Namn på medlemsdatorn. |
| `Group` |Gruppens namn. |
| `GroupFullName` |Fullständig sökväg till gruppen inklusive käll- och källnamnet. |
| `GroupSource` |Källa som gruppen samlades in från. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientMålsättning |
| `GroupSourceName` |Namnet på källan som gruppen hämtades från.  För Active Directory är detta domännamnet. |
| `ManagementGroupName` |Namnet på hanteringsgruppen för SCOM-agenter.  För andra agenter är detta\<AOI- arbetsyte-ID\> |
| `TimeGenerated` |Datum och tid då datorgruppen skapades eller uppdaterades. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar.  

