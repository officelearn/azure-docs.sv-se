---
title: ta med fil
description: ta med fil
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538836"
---
### <a name="the-data-model-of-the-schema"></a>Schemats datamodell

|Field|Beskrivning|
|----|----|
|**AlertName (Aviseringsnamn)**|Visningsnamn för varning|
|**AlertType (Aviseringstyp)**|unik varningsidentifierare|
|**FörtroendeNivå**|(Valfritt) Konfidensnivån för denna varning (Hög/Låg)|
|**ConfidenceScore**|(Valfritt) Numerisk konfidensindikator för säkerhetsvarningen|
|**Beskrivning**|Beskrivningstext för aviseringen|
|**Displayname**|Aviseringens visningsnamn|
|**Endtime**|Aviseringens sluttid för påverkan (tidpunkten för den senaste händelsen som bidrar till aviseringen)|
|**Entiteter**|En lista över entiteter som är relaterade till aviseringen. Denna lista kan innehålla en blandning av enheter av olika typer|
|**ExtendedLinks**|(Valfritt) En väska för alla länkar relaterade till varningen. Denna väska kan hålla en blandning av länkar för olika typer|
|**Utökade egenskaper**|En påse med ytterligare fält som är relevanta för aviseringen|
|**IsIncident (IsIncident)**|Avgör om aviseringen är en incident eller en vanlig avisering. En incident är en säkerhetsvarning som sammanställer flera aviseringar till en säkerhetsincident|
|**ProcessingEndTime**|UTC-tidsstämpel där aviseringen skapades|
|**ProductComponentName**|(Valfritt) Namnet på en komponent inuti produkten som genererade aviseringen.|
|**ProductName**|konstant ("Azure Security Center")|
|**ProviderName**|Oanvända|
|**ÅtgärdSteg**|Manuella åtgärdsobjekt att vidta för att åtgärda säkerhetshotet|
|**ResourceId**|Fullständig identifierare för den berörda resursen|
|**Allvarlighetsgrad**|Allvarlighetsgrad för varningen (hög/medel/låg/informativ)|
|**SourceComputerId**|ett unikt GUID för den berörda servern (om aviseringen genereras på servern)|
|**SourceSystem**|Oanvända|
|**Starttime**|Aviseringens starttid för påverkan (tidpunkten för den första händelsen som bidrar till aviseringen)|
|**SystemAlertId**|Unik identifierare för den här säkerhetsvarningsinstansen|
|**TenantId (5)**|identifieraren för den överordnade Azure Active Directory-klienten för prenumerationen som den skannade resursen finns under|
|**TimeGenerated**|UTC-tidsstämpel som utvärderingen ägde rum på (Säkerhetscentrets skanningstid) (identisk med DiscoveredTimeUTC)|
|**Typ**|konstant ("SecurityAlert")|
|**Leverantörsnamn**|Namnet på den leverantör som tillhandahöll aviseringen (t.ex.|
|**LeverantörOriginalId**|Oanvända|
|**WorkspaceResourceGroup**|om aviseringen genereras på en VM-, server-, virtual machine-skaluppsättning eller apptjänstinstans som rapporterar till en arbetsyta, innehåller namnet på arbetsyteresursgruppen|
|**WorkspaceSubscriptionId**|om aviseringen genereras på en VM-, server-, virtual machine-skaluppsättning eller apptjänstinstans som rapporterar till en arbetsyta, innehåller den arbetsytan subscriptionId|
|||
