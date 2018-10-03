---
title: Datorgrupper i Azure Log Analytics loggsökningar | Microsoft Docs
description: Datorgrupper i Log Analytics kan du omfång på sökningar i loggen för en viss uppsättning datorer.  Den här artikeln beskrivs de olika metoderna som du kan använda för att skapa datorgrupper och hur du använder dem i en loggsökning.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 7e4889148a752b552f8bd65702ea5dda450ded31
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044305"
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Datorgrupper i Log Analytics loggsökningar

Datorgrupper i Log Analytics kan du även agera omfång [loggsökningar](log-analytics-log-search-new.md) till en viss uppsättning datorer.  Varje grupp har fyllts i med datorer som antingen med hjälp av en fråga som du definierar eller genom att importera grupper från olika källor.  När gruppen ingår i en loggsökning, är resultatet begränsade till poster som matchar datorerna i gruppen.

## <a name="creating-a-computer-group"></a>Skapa en datorgrupp
Du kan skapa en datorgrupp i Log Analytics med hjälp av någon av metoderna i följande tabell.  Information om varje metod finns i avsnitten nedan. 

| Metod | Beskrivning |
|:--- |:--- |
| Loggsökning |Skapa en loggsökning som returnerar en lista med datorer. |
| Loggsöknings-API |Använd Log Search API du programmässigt skapar en datorgrupp baserat på resultatet av en loggsökning. |
| Active Directory |Genomsök automatiskt gruppmedlemskap för alla agentdatorer som är medlemmar i en Active Directory-domän och skapa en grupp i Log Analytics för varje säkerhetsgrupp. |
| Configuration Manager | Importera samlingar från System Center Configuration Manager och skapa en grupp i Log Analytics för var och en. |
| Windows Server Update Services |Skanna WSUS-servrar eller klienter för grupper och skapa en grupp i Log Analytics för varje automatiskt. |

### <a name="log-search"></a>Loggsökning
Datorgrupper som skapats från en Loggsökning som innehåller alla datorer som returneras av en fråga som du definierar.  Den här frågan körs varje gång datorgruppen används så att alla ändringar eftersom gruppen har skapats visas.  

Du kan använda en fråga för en datorgrupp, men den måste returnera en specifik uppsättning datorer med hjälp av `distinct Computer`.  Följande är ett typexempel sökning som du kan använda för som en datorgrupp.

    Heartbeat | where Computer contains "srv" | distinct Computer

I följande tabell beskrivs de egenskaper som definierar en datorgrupp.

| Egenskap  | Beskrivning |
|:---|:---|
| Visningsnamn   | Namn på sökningen ska visas i portalen. |
| Kategori       | Kategori för att organisera sökningar i portalen. |
| Fråga          | Fråga för datorgruppen. |
| Funktionsalias | Ett unikt alias som används för att identifiera datorgruppen i en fråga. |

Använd följande procedur för att skapa en datorgrupp från en loggsökning i Azure-portalen.

2. Öppna **Loggsökning** och klicka sedan på **sparade sökningar** överst på skärmen.
3. Klicka på **Lägg till** och ange värden för varje egenskap för datorgruppen.
4. Välj **spara frågan som en datorgrupp** och klicka på **OK**.



### <a name="active-directory"></a>Active Directory
När du konfigurerar Log Analytics för att importera Active Directory-gruppmedlemskap, analyserar gruppmedlemskap för alla domänanslutna datorer med OMS-agenten.  En datorgrupp skapas i Log Analytics för varje säkerhetsgrupp i Active Directory och varje dator läggs till i de datorgrupper som motsvarar de säkerhetsgrupper som de är medlemmar i.  Det här medlemskapet uppdateras kontinuerligt var fjärde timme.  

Du konfigurerar Log Analytics för att importera Active Directory-säkerhetsgrupper från Log Analytics **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper**, **Active Directory**, och sedan **importera Active Directory-gruppmedlemskap från datorer**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

När grupper har importerats visas på menyn antalet datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

### <a name="windows-server-update-service"></a>Windows Server Update Service
När du konfigurerar Log Analytics för att importera WSUS-gruppmedlemskap, analyserar målobjekt gruppmedlemskap för alla datorer med OMS-agenten.  Om du använder klientsidan har mål, alla datorer som är ansluten till Log Analytics och är en del av alla WSUS riktar in sig på grupper dess gruppmedlemskap som importerats till Log Analytics. Om du använder serversidan ska med använder OMS agenten installeras på WSUS-servern för information som ska importeras till Log Analytics gruppmedlemskap.  Det här medlemskapet uppdateras kontinuerligt var fjärde timme. 

Du konfigurerar Log Analytics för att importera WSUS-grupper från Log Analytics **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper**, **WSUS**, och sedan **importera WSUS-gruppmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från WSUS](media/log-analytics-computer-groups/configure-wsus.png)

När grupper har importerats visas på menyn antalet datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
När du konfigurerar Log Analytics för att importera Configuration Manager-samlingsmedlemskap, skapas en datorgrupp för varje samling.  Samling medlemskapsinformation hämtas var tredje timme om du vill behålla det aktuella datorgrupper. 

Innan du kan importera samlingar i Configuration Manager, måste du [Anslut Konfigurationshanteraren till Log Analytics](log-analytics-sccm.md).  Importera från Log Analytics kan du konfigurera **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper**, **SCCM**, och sedan **Import Configuration Manager-samlingsmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från SCCM](media/log-analytics-computer-groups/configure-sccm.png)

När samlingar har importerats visas på menyn antalet datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

## <a name="managing-computer-groups"></a>Hantera datorgrupper
Du kan visa datorgrupper som har skapats från en loggsökning eller Log Search-API från Log Analytics **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper** och sedan **sparade grupper**.  

Klicka på den **x** i den **ta bort** kolumnen för att ta bort datorgruppen.  Klicka på den **visa medlemmarna** ikon för en grupp för att köra gruppens loggsökning som returnerar dess medlemmar.  Du kan inte ändra en datorgrupp men i stället måste ta bort och återskapa den med ändrade inställningar.

![Sparade datorgrupper](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Använda en datorgrupp i en loggsökning
Du kan använda en datorgrupp som skapats från en loggsökning i en fråga genom att behandla dess alias som en funktion, vanligtvis med följande syntax:

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
En post skapas i Log Analytics-arbetsyta för varje datorgruppmedlemskap som skapats från Active Directory eller WSUS.  Dessa poster har en typ av **ComputerGroup** och har egenskaperna i följande tabell.  Poster skapas inte för datorgrupper baserat på sökningar i loggen.

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
* Lär dig mer om [loggsökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.  

