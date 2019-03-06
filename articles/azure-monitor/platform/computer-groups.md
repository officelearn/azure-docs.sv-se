---
title: Datorgrupper i Azure Monitor logga frågor | Microsoft Docs
description: Datorgrupper i Azure Monitor kan du logga attributbegränsade frågor till en viss uppsättning datorer.  Den här artikeln beskrivs de olika metoderna som du kan använda för att skapa datorgrupper och hur de används i en loggfråga.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: 0a29e453c723ecc9ac378ee337365525587aaef2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444131"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Datorgrupper i Azure Monitor log-frågor
Datorgrupper i Azure Monitor kan du även agera omfång [logga frågor](../log-query/log-query-overview.md) till en viss uppsättning datorer.  Varje grupp har fyllts i med datorer som antingen med hjälp av en fråga som du definierar eller genom att importera grupper från olika källor.  När gruppen ingår i en loggfråga, är resultat begränsade till poster som matchar datorerna i gruppen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Skapa en datorgrupp
Du kan skapa en datorgrupp i Azure Monitor med någon av metoderna i följande tabell.  Information om varje metod finns i avsnitten nedan. 

| Metod | Beskrivning |
|:--- |:--- |
| Loggfråga |Skapa en loggfråga som returnerar en lista med datorer. |
| Loggsöknings-API |Använd Log Search API du programmässigt skapar en datorgrupp baserat på resultatet av en loggfråga. |
| Active Directory |Genomsök automatiskt gruppmedlemskap för alla agentdatorer som är medlemmar i en Active Directory-domän och skapa en grupp i Azure Monitor för varje säkerhetsgrupp. (Endast Windows-datorer)|
| Configuration Manager | Importera samlingar från System Center Configuration Manager och skapa en grupp i Azure Monitor för var och en. |
| Windows Server Update Services |Skanna WSUS-servrar eller klienter för grupper och skapa en grupp i Azure Monitor för varje automatiskt. |

### <a name="log-query"></a>Loggfråga
Datorgrupper som skapats från en loggfråga innehåller alla datorer som returneras av en fråga som du definierar.  Den här frågan körs varje gång datorgruppen används så att alla ändringar eftersom gruppen har skapats visas.  

Du kan använda en fråga för en datorgrupp, men den måste returnera en specifik uppsättning datorer med hjälp av `distinct Computer`.  Följande är ett typexempel-fråga som du kan använda för som en datorgrupp.

    Heartbeat | where Computer contains "srv" | distinct Computer

Använd följande procedur för att skapa en datorgrupp från en loggsökning i Azure-portalen.

1. Klicka på **loggar** i den **Azure Monitor** menyn i Azure-portalen.
1. Skapa och kör en fråga som returnerar de datorer som du vill i gruppen.
1. Klicka på **spara** överst på skärmen.
1. Ändra **Spara som** till **funktionen** och välj **spara frågan som en datorgrupp**.
1. Ange värden för varje egenskap för datorgruppen som beskrivs i tabellen och klickar på **spara**.

I följande tabell beskrivs de egenskaper som definierar en datorgrupp.

| Egenskap  | Beskrivning |
|:---|:---|
| Namn   | Namnet på frågan som visas i portalen. |
| Funktionsalias | Ett unikt alias som används för att identifiera datorgruppen i en fråga. |
| Kategori       | Kategori för att organisera frågor i portalen. |


### <a name="active-directory"></a>Active Directory
När du konfigurerar Azure Monitor för att importera Active Directory-gruppmedlemskap, analyserar gruppmedlemskap för alla Windows-domänanslutna datorer med Log Analytics-agenten.  En datorgrupp som har skapats i Azure Monitor för varje säkerhetsgrupp i Active Directory och varje Windows-dator läggs till i de datorgrupper som motsvarar de säkerhetsgrupper som de är medlemmar i.  Det här medlemskapet uppdateras kontinuerligt var fjärde timme.  

> [!NOTE]
> Importerade Active Directory-grupper bara innehålla Windows-datorer.

Du konfigurerar Azure Monitor för att importera Active Directory-säkerhetsgrupper från **avancerade inställningar** i Log Analytics-arbetsytan i Azure-portalen.  Välj **datorgrupper**, **Active Directory**, och sedan **importera Active Directory-gruppmedlemskap från datorer**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från Active Directory](media/computer-groups/configure-activedirectory.png)

När grupper har importerats visas på menyn antalet datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

### <a name="windows-server-update-service"></a>Windows Server Update Service
När du konfigurerar Azure Monitor för att importera WSUS-gruppmedlemskap, analyserar målobjekt gruppmedlemskap för alla datorer med Log Analytics-agenten.  Om du använder klientsidan har mål, alla datorer som är ansluten till Azure Monitor och är en del av alla WSUS riktar in sig på grupper dess gruppmedlemskap som importeras till Azure Monitor. Om du använder serversidan ska med använder Log Analytics agenten installeras på WSUS-servern för information som ska importeras till Azure Monitor gruppmedlemskap.  Det här medlemskapet uppdateras kontinuerligt var fjärde timme. 

Du konfigurerar Azure Monitor för att importera WSUS-grupper från **avancerade inställningar** i Log Analytics-arbetsytan i Azure-portalen.  Välj **datorgrupper**, **WSUS**, och sedan **importera WSUS-gruppmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från WSUS](media/computer-groups/configure-wsus.png)

När grupper har importerats visas på menyn antalet datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
När du konfigurerar Azure Monitor för att importera Configuration Manager-samlingsmedlemskap, skapas en datorgrupp för varje samling.  Samling medlemskapsinformation hämtas var tredje timme om du vill behålla det aktuella datorgrupper. 

Innan du kan importera samlingar i Configuration Manager, måste du [ansluta Configuration Manager till Azure Monitor](collect-sccm.md).  Du kan sedan konfigurera importera från **avancerade inställningar** i Log Analytics-arbetsytan i Azure-portalen.  Välj **datorgrupper**, **SCCM**, och sedan **Import Configuration Manager-samlingsmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från SCCM](media/computer-groups/configure-sccm.png)

När samlingar har importerats visas på menyn antalet datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

## <a name="managing-computer-groups"></a>Hantera datorgrupper
Du kan visa datorgrupper som har skapats från en loggfråga eller Log Search-API från **avancerade inställningar** i Log Analytics-arbetsytan i Azure-portalen.  Välj **datorgrupper** och sedan **sparade grupper**.  

Klicka på den **x** i den **ta bort** kolumnen för att ta bort datorgruppen.  Klicka på den **visa medlemmarna** ikon för en grupp för att köra gruppens loggsökning som returnerar dess medlemmar.  Du kan inte ändra en datorgrupp men i stället måste ta bort och återskapa den med ändrade inställningar.

![Sparade datorgrupper](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Använda en datorgrupp i en loggfråga
Du kan använda en datorgrupp som skapats från en loggfråga i en fråga genom att behandla dess alias som en funktion, vanligtvis med följande syntax:

  `Table | where Computer in (ComputerGroup)`

Du kan exempelvis använda följande för att returnera UpdateSummary-poster för endast datorer i en datorgrupp som kallas mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Lagras importerade datorgrupper och deras datorer som ingår i den **ComputerGroup** tabell.  Följande fråga skulle till exempel returnera en lista med datorer i gruppen datorer i domänen från Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Följande fråga returnerar UpdateSummary-poster för endast datorer i Domändatorer.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Datorn gruppera poster
En post skapas i Log Analytics-arbetsyta för varje datorgruppmedlemskap som skapats från Active Directory eller WSUS.  Dessa poster har en typ av **ComputerGroup** och har egenskaperna i följande tabell.  Poster skapas inte för datorgrupper baserat på loggfrågor.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Typ |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Dator |Namnet på datorn som är medlem. |
| Grupp |Namnet på gruppen. |
| GroupFullName |Fullständig sökväg till gruppen, inklusive käll- och namn på datakälla. |
| GroupSource |Käll-gruppen har samlats in från. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Namnet på källan som gruppen har samlats in från.  Detta är domännamnet för Active Directory. |
| ManagementGroupName |Namnet på hanteringsgruppen för SCOM-agenter.  För andra agenter är detta AOI -\<arbetsyte-ID\> |
| TimeGenerated |Datum och tid i datorgruppen skapades eller uppdaterades. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga frågor](../log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar.  

