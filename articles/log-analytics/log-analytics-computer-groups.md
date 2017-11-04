---
title: "Datorgrupper i logganalys logga sökningar | Microsoft Docs"
description: "Datorgrupper i logganalys kan du omfång loggen sökningen till en viss uppsättning datorer.  Den här artikeln beskriver de olika metoder som du kan använda för att skapa datorgrupper och hur de används i en sökning i loggen."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: bwren
ms.openlocfilehash: 17a59a38b6a445a7f42df171a711669f95fc84c2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Datorgrupper i logganalys logga sökningar

Datorgrupper i Log Analytics gör att du kan scope [logga sökningar](log-analytics-log-search-new.md) till en viss uppsättning datorer.  Varje grupp fylls med datorer som antingen använder en fråga som du definierar eller genom att importera grupper från olika källor.  När gruppen ingår i en logg-sökning, är resultatet begränsade till poster som matchar datorerna i gruppen.

## <a name="creating-a-computer-group"></a>Skapa en datorgrupp
Du kan skapa en datorgrupp i Log Analytics med hjälp av någon av metoderna i följande tabell.  Information om varje metod finns i avsnitten nedan. 

| Metod | Beskrivning |
|:--- |:--- |
| Loggsökning |Skapa en logg sökning som returnerar en lista med datorer. |
| Loggsöknings-API |Använd loggen Sök API för att programmatiskt skapa en datorgrupp baserat på resultatet av en sökning i loggen. |
| Active Directory |Sök igenom gruppmedlemskap för alla agentdatorer som som är medlemmar i en Active Directory-domän och skapa en grupp i Log Analytics för varje säkerhetsgrupp automatiskt. |
| Configuration Manager | Importera samlingar från System Center Configuration Manager och skapa en grupp i Log Analytics för varje. |
| Windows Server Update Services |Skanna WSUS-servrar eller klienter för att rikta sig till grupper och skapa en grupp i Log Analytics för varje automatiskt. |

### <a name="log-search"></a>Loggsökning
Datorgrupper som skapas från en sökning i loggen innehåller alla datorer som returneras av en fråga som du definierar.  Den här frågan körs varje gång datorgruppen används så att ändringar eftersom gruppen har skapats visas.  

Du kan använda en fråga för en datorgrupp, men det måste returnera en specifik uppsättning datorer med hjälp av `distinct Computer`.  Följande är ett typexempel sökning som du kan använda för som en datorgrupp.

    Heartbeat | where Computer contains "srv" | distinct Computer

I följande tabell beskrivs de egenskaper som definierar en datorgrupp.

| Egenskap | Beskrivning |
|:---|:---|
| Visningsnamn   | Namn på sökningen ska visas i portalen. |
| Kategori       | Kategori för att organisera sökningar i portalen. |
| Fråga          | Frågan för datorgruppen. |
| Funktionen alias | Ett unikt alias som används för att identifiera datorgruppen i en fråga. |

Använd följande procedur för att skapa en datorgrupp från en logg sökning i Azure-portalen.

2. Öppna **loggen Sök** och klicka sedan på **sparade sökningar** överst på skärmen.
3. Klicka på **Lägg till** och ange värden för varje egenskap för datorgruppen.
4. Välj **spara frågan som en datorgrupp** och på **OK**.


Använd följande procedur för att skapa en datorgrupp från en logg sökning i OMS-portalen.

1. Öppna **loggen Sök** och skapa log-sökning för datorgruppen.  
2. Klicka på den **spara** längst upp på skärmen.
3. Välj **Ja** till **spara frågan som en datorgrupp**.
5. Ange värden för varje egenskap för datorgruppen. 


>[!NOTE]
> Om arbetsytan fortfarande använder den [äldre Log Analytics-frågespråket](log-analytics-log-search-upgrade.md) du använda samma procedur för att skapa en datorgrupp, men du måste använda syntaxen för äldre frågespråket.


### <a name="log-search-api"></a>Logga Sök-API
Datorgrupper som skapats med loggen Sök API är samma som sökningar som skapats med en logg-sökning.  Mer information om hur du skapar en datorgrupp med hjälp av loggen Sök-API finns [datorgrupper i logganalys-loggen Sök REST API](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
När du konfigurerar logganalys importera Active Directory-gruppmedlemskap, analyserar gruppmedlemskap för alla domänanslutna datorer med OMS-agent.  En datorgrupp skapas i Log Analytics för varje säkerhetsgrupp i Active Directory och varje dator läggs till datorgrupper som motsvarar de säkerhetsgrupper som de är medlemmar i.  Det här medlemskapet uppdateras kontinuerligt var fjärde timme.  

Du konfigurerar Log Analytics för att importera Active Directory-säkerhetsgrupper från logganalys **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper**, **Active Directory**, och sedan **importera Active Directory-gruppmedlemskap från datorer**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

När grupper har importerats visas på menyn hur många datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

### <a name="windows-server-update-service"></a>Windows Server Update Service
När du konfigurerar logganalys importera WSUS-gruppmedlemskap, analyserar målobjekt gruppmedlemskap för alla datorer med OMS-agent.  Om du använder klientsidan har målobjekt för alla datorer som är ansluten till OMS och ingår i alla WSUS riktad grupper dess gruppmedlemskap importeras till logganalys. Om du använder serversidan ska inriktning på OMS agenten installeras på WSUS-servern för information som ska importeras till OMS gruppmedlemskap.  Det här medlemskapet uppdateras kontinuerligt var fjärde timme. 

Du konfigurerar Log Analytics för att importera WSUS-grupper från logganalys **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper**, **WSUS**, och sedan **importera WSUS-gruppmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från WSUS](media/log-analytics-computer-groups/configure-wsus.png)

När grupper har importerats visas på menyn hur många datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
När du konfigurerar logganalys att importera samlingsmedlemskap för Configuration Manager, skapas en datorgrupp för varje samling.  Information om medlemskap samling hämtas var 3: e timme om du vill behålla det aktuella datorgrupper. 

Innan du kan importera samlingar i Configuration Manager, måste du [ansluta Configuration Manager till logganalys](log-analytics-sccm.md).  Du kan sedan konfigurera importera från logganalys **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper**, **SCCM**, och sedan **Import Configuration Manager samlingsmedlemskap**.  Det krävs ingen ytterligare konfiguration.

![Datorgrupper från SCCM](media/log-analytics-computer-groups/configure-sccm.png)

När samlingar har importerats visas på menyn hur många datorer med gruppmedlemskap har identifierats och antalet grupper som importeras.  Du kan klicka på någon av dessa länkar för att returnera den **ComputerGroup** poster med den här informationen.

## <a name="managing-computer-groups"></a>Hantera datorgrupper
Du kan visa datorgrupper som skapades från en logg sökning eller loggen Sök-API från logganalys **avancerade inställningar** i Azure-portalen.  Välj **datorgrupper** och sedan **sparade grupper**.  

Klicka på den **x** i den **ta bort** kolumnen att ta bort gruppen.  Klicka på den **visa medlemmar** ikonen för en grupp för att utföra gruppens loggen sökning som returnerar dess medlemmar.  Du kan inte ändra en datorgrupp men i stället måste ta bort och återskapa den med ändrade inställningar.

![Sparade datorgrupper](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Använda en datorgrupp i en logg-sökning
Du kan använda en datorgrupp som skapas från en logg sökning i en fråga genom att behandla dess alias som en funktion, vanligtvis med följande syntax:

  `Table | where Computer in (ComputerGroup)`

Exempelvis kan du använda följande för att returnera UpdateSummary poster för endast datorer i en datorgrupp som kallas mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Importerade datorgrupper och deras inkluderade datorer lagras i den **ComputerGroup** tabell.  Följande fråga skulle till exempel returnera en lista över datorer i gruppen Domändatorer från Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Följande fråga returnerar UpdateSummary poster för endast datorer i Domändatorer.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```



  

>[!NOTE]
> Om arbetsytan fortfarande använder den [äldre Log Analytics-frågespråket](log-analytics-log-search-upgrade.md)>, kan du använda följande syntax för att referera till en datorgrupp i en sökning i loggen.  Ange den **kategori** > är valfria och endast krävs om du har datorgrupper med samma namn i olika kategorier. 
>
>    `$ComputerGroups[Category: Name]`
>
>Datorgrupper används vanligtvis med den **IN** satsen Sök i loggfilen som i följande exempel:
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



## <a name="computer-group-records"></a>Gruppen datorposter
En post har skapats i OMS-databasen för varje datorgruppmedlemskap som skapats från Active Directory eller WSUS.  Dessa poster har en typ av **ComputerGroup** och ha egenskaper i följande tabell.  Poster skapas inte för datorgrupper baserat på loggen sökningar.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Dator |Namnet på datorn som är medlem. |
| Grupp |Namnet på gruppen. |
| GroupFullName |Fullständig sökväg till gruppen inklusive käll- och namnet på datakällan. |
| GroupSource |Datakällan gruppen har samlats in från. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Namnet på källan som gruppen har samlats in från.  Detta är domännamnet för Active Directory. |
| ManagementGroupName |Namnet på hanteringsgruppen för SCOM-agenter.  För andra agenter är AOI -\<arbetsyte-ID\> |
| TimeGenerated |Datum och tid datorgruppen skapats eller uppdaterats. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.  

