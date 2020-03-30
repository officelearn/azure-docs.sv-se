---
title: ta med fil
description: ta med fil
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272703"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Schemats datamodell

|Field|Datatyp|Beskrivning|
|----|----|----|
|**AlertDisplayName**|String|Visningsnamnet på aviseringen.|
|**AlertType (Aviseringstyp)**|String|Typ av avisering. Aviseringar av samma typ bör ha samma värde. Det här fältet är en nyckelsträng som representerar typen av avisering och inte av en aviseringsinstans. Alla varningsinstanser från samma identifieringslogik/analytiska bör ha samma värde för aviseringstyp.|
|**Kompromettitet**|String|Visningsnamnet för den resurs som är mest relaterad till den här aviseringen.|
|**Beskrivning**|String|Beskrivning av varningen.|
|**EndTimeUtc (Sluttidutkvisa)**|DateTime|Tidpunkten för den senaste händelsen eller aktiviteten som ingår i aviseringen.  Fältet ska vara en sträng som överensstämmer med ISO8601-formatet, inklusive UTC-tidszonsinformation.|
|**Entiteter**|IEnumerable (IEntity)|En lista över entiteter som är relaterade till aviseringen. Den här listan kan innehålla en blandning av enheter av olika typer. Enhetstypen kan vara någon av de typer som definieras i avsnittet Entiteter. Entiteter som inte finns med i listan nedan kan också skickas, men det är inte garanterat att de kommer att bearbetas (aviseringen misslyckas inte med validering med nya typer av entiteter).|
|**Utökade egenskaper**|Ordlista (sträng,sträng)|Leverantörer kan (eventuellt) inkludera anpassade fält här.|
|**Avsikt**|Enum|Dödskedjan relaterade uppsåt bakom varningen. Lista över värden som stöds och förklaringar av Azure Security Center som stöds för att döda kedjeavsikter finns i [Avsikter](../articles/security-center/alerts-reference.md#intentions).<br/>Det här fältet kan ha flera värden (avgränsade med kommatecken).|
|**IsIncident (IsIncident)**|Bool|Det här fältet avgör om aviseringen är en incident (en sammansatt gruppering av flera aviseringar) eller en enda avisering. Standardvärdet för fältet är "falskt" (vilket innebär att det är en enda avisering).|
|**ProcessingEndTime**|DateTime|Den tidpunkt då aviseringen var tillgänglig för slutanvändaren i den ursprungliga produkten som innehar varningen.|
|**ProductName**|String|Namnet på den produkt som publicerade den här aviseringen (Azure Security Center, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS och så vidare).|
|**ÅtgärdSteg**|Lista<String>|Manuella åtgärdsobjekt som ska vidtas för att åtgärda aviseringen.|
|**ResourceIdentifiers**|Lista (resursidentifierare)|Resursidentifierarna för den här aviseringen som kan användas för att dirigera aviseringen till rätt produktexponeringsgrupp (klient, arbetsyta, prenumeration osv.). Det kan finnas flera identifierare av olika typ per avisering.|
|**Allvarlighetsgrad**|Enum|Allvarlighetsgraden för aviseringen som rapporterats av providern. Möjliga värden: Informativ, Låg, Medel och Hög.|
|**StartTimeUtc**|DateTime|Tiden för den första händelsen eller aktiviteten som ingår i aviseringen. Fältet ska vara en sträng som överensstämmer med ISO8601-formatet, inklusive UTC-tidszonsinformation.|
|**Status**|Enum|Aviseringens livscykelstatus.<br/>Status som stöds är: Ny, Löst, Avvisad, Okänd.<br/>En avisering som anger ett annat värde än alternativen som stöds tilldelas statusen Okänd.<br/>En avisering som inte anger ett värde tilldelas statusen "Ny".|
|**SystemAlertId**|String|Aviseringsidentifieraren.|
|**TimeGenerated**|DateTime|Den tid då aviseringen genererades av aviseringsprovidern. Om den inte rapporteras av interna varningsleverantörer kan en produkt välja att tilldela den tid den togs emot för bearbetning av produkten.  Fältet ska vara en sträng som överensstämmer med ISO8601-formatet, inklusive UTC-tidszonsinformation.|
|**Leverantörsnamn**|String|Namnet på leverantören som höjer aviseringen.|
|||
