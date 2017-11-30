---
title: "Vad är meddelanden om hälsostatus | Microsoft Docs"
description: "Meddelanden om hälsostatus kan du visa tjänstens hälsa publicera meddelanden av Microsoft Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: efdd42d244710b27fc33154b708cfbe40312e3b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="service-health-notifications"></a>Meddelanden om hälsostatus
## <a name="overview"></a>Översikt

Den här artikeln visar hur du visar hälsotillstånd tjänstmeddelanden med Azure-portalen.

Meddelanden om hälsostatus kan du visa meddelanden om hälsotillstånd har publicerats av Azure-teamet som kan påverka resurser i din prenumeration. Dessa aviseringar är en underklass till aktiviteten logghändelser och finns även i aktivitetsloggen. Meddelanden om hälsostatus kan vara information eller tillämplig beroende på klassen.

Det finns fem typer av meddelanden om hälsostatus:  

- **Åtgärd krävs:** från gång till gång Azure hända något annorlunda hända om ditt konto. Azure behöver arbeta med dig för att åtgärda detta. Azure kommer att skicka ett meddelande som antingen med information om åtgärderna du måste vidta eller med information om hur du kontaktar Azure tekniker eller support.  
- **Assisterad återställning:** en händelse har inträffat och tekniker har bekräftat att du fortfarande har effekt. Azure tekniker behöver dig direkt för att återställa tjänsterna för fullständig hälsa.  
- **Incident:** en tjänst som påverkar händelse för närvarande påverkar en eller flera resurser i din prenumeration.  
- **Underhåll:** detta är ett meddelande informerar dig om en aktivitet för planerat underhåll som kan påverka en eller flera av resurserna i din prenumeration.  
- **Information:** då och då du Azure kan skicka meddelanden med information om potentiella optimeringar som kan förbättra din resursutnyttjande.  
- **Säkerhet:** brådskande säkerhetsrelaterade information om din solution(s) som körs på Azure.

Varje meddelande för health service innehåller information om omfattningen av och påverkan på dina resurser. Innehåller:

Egenskapsnamn | Beskrivning
-------- | -----------
kanaler | Är ett av följande värden: ”Admin”, ”åtgärden”
correlationId | Är vanligtvis ett GUID i strängformatet. Händelser med som tillhör samma uber åtgärd vanligtvis delar samma correlationId.
eventDataId | Är den unika identifieraren för en händelse
EventName | Är titeln på händelsen
nivå | Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig”
resourceProviderName | Namnet på resursprovidern för resursen påverkas
resourceType| Typ av resurs på den berörda resursen
subStatus | Vanligtvis HTTP-statuskoden motsvarande rest anropa, men kan även innehålla andra strängar som beskriver en sådan, till exempel värdena vanliga: OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), det gick inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod : 409), internt serverfel (HTTP-statuskod: 500), tjänsten inte tillgänglig (HTTP-statuskod: 503), Gateway-Timeout (HTTP-statuskod: 504).
eventTimestamp | Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades.
submissionTimestamp |   Tidsstämpel när händelsen blev tillgängliga för frågor.
subscriptionId | Azure-prenumeration som den här händelsen loggades
status | Sträng som beskriver status för åtgärden. Vissa vanliga värden: startas i pågår, slutfört, misslyckades, aktiv, löst.
operationName | Namnet på åtgärden.
category | ”ServiceHealth”
resourceId | Resurs-ID för resursen påverkas.
Properties.title | Lokaliserade rubriken för den här kommunikationen. Engelska är standardspråk.
Properties.Communication | Lokaliserad information om kommunikationen med HTML-kod. Engelska är standard.
Properties.incidentType | Möjliga värden: AssistedRecovery, ActionRequired, Information, incidenter, underhåll, säkerhet
Properties.trackingId | Identifierar den här händelsen är associerad med incidenten. Används för att korrelera händelser relaterade till en incident.
Properties.impactedServices | En ESC JSON-blob som beskriver de tjänster och regioner som påverkas av incidenten. En lista över tjänster, som har en ServiceName och en lista över ImpactedRegions, som har en RegionName.
Properties.defaultLanguageTitle | Kommunikation på engelska
Properties.defaultLanguageContent | Kommunikation på engelska som HTML-kod eller oformaterad text
Properties.Stage | Möjliga värden för AssistedRecovery, ActionRequired, Information, incidenter, säkerhet: är aktiv, löst. De är för underhåll: aktiv, planerad, InProgress, Avbruten, Rescheduled, löst, Slutför
Properties.communicationId | Kommunikationen den här händelsen är kopplat.


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>Visa din tjänstmeddelanden hälsotillstånd i Azure-portalen
1.  I den [portal](https://portal.azure.com), navigera till den **övervakaren** service

    ![Övervaka](./media/monitoring-service-notifications/home-monitor.png)
2.  Klicka på den **övervakaren** alternativet för att öppna Monitor-upplevelse. Azure-Monitor samlar alla dina övervakning inställningar och data till en samlad vy. Först öppnas den i avsnittet **Aktivitetslogg**.

3.  Klicka på **aviseringar** avsnitt

    ![Övervaka](./media/monitoring-service-notifications/service-health-summary.png)
4. Klicka på den **+ Lägg till loggen varning** och konfigurera en avisering om du vill se till att du får ett meddelande för framtida tjänstmeddelanden. Mer information om hur du konfigurerar aviseringar på tjänstmeddelanden [finns på aktiviteten loggen aviseringar och tjänstmeddelanden](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>Nästa steg:
Ta emot [aviseringar när ett meddelande om tjänstens hälsa](monitoring-activity-log-alerts-on-service-notifications.md) skickas  
Lär dig mer om [aktivitet loggen aviseringar](monitoring-activity-log-alerts.md)
