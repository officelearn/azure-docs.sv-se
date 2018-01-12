---
title: "Vad är Azure-tjänstens hälsotillstånd meddelanden? | Microsoft Docs"
description: "Meddelanden om hälsostatus kan du visa meddelanden om hälsotillstånd har publicerats av Microsoft Azure."
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
ms.openlocfilehash: 4a95e9882515e6a2861292829a44847e11f39063
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visa meddelanden om hälsostatus med hjälp av Azure portal

Meddelanden om hälsostatus publiceras i Azure och innehåller information om resurser i din prenumeration. Dessa aviseringar är en underklass till aktiviteten logghändelser och finns även i aktivitetsloggen. Meddelanden om hälsostatus kan vara information eller tillämplig, beroende på klassen.

Det finns olika typer av meddelanden om hälsostatus:  

- **Åtgärd krävs:** Azure märker något annorlunda inträffa på ditt konto och hjälpa dig med att åtgärda detta. Azure skickar ett meddelande, antingen med detaljer om du behöver vidta åtgärder eller hur du kontaktar Azure tekniker eller support.  
- **Stödd återställning:** en händelse har inträffat och tekniker har bekräftat att du fortfarande har effekt. Azure tekniker behöver dig direkt för att återställa tjänsterna för fullständig hälsa.  
- **Incident:** en händelse som påverkar tjänsten för närvarande påverkar en eller flera resurser i din prenumeration.  
- **Underhåll:** en aktivitet för planerat underhåll som kan påverka en eller flera av resurserna i din prenumeration.  
- **Information:** potentiella optimeringar som kan hjälpa dig att förbättra din Resursanvändning. 
- **Säkerhet:** brådskande säkerhetsrelaterad information om dina lösningar som körs på Azure.

Varje meddelande för health service innehåller information om omfattningen av och påverkan på dina resurser. Innehåller:

Egenskapsnamn | Beskrivning
-------- | -----------
kanaler | Ett av följande värden: **Admin** eller **åtgärden**.
correlationId | Vanligtvis ett GUID i strängformatet. Händelser som hör till samma åtgärd vanligtvis delar samma correlationId.
eventDataId | Den unika identifieraren för en händelse.
EventName | Rubrik på en händelse.
nivå | Nivån i en händelse. Ett av följande värden: **kritisk**, **fel**, **varning** eller **informations**.
resourceProviderName | Namnet på resursprovidern för resursen påverkas.
resourceType| Typ av resurs för resursen påverkas.
subStatus | Vanligtvis HTTP-statuskoden motsvarande rest anropa, men kan även innehålla andra strängar som beskriver en sådan. Till exempel: OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), det gick inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod: 409), intern Server Fel (HTTP-statuskod: 500), tjänsten inte tillgänglig (HTTP-statuskod: 503), och Gateway-Timeout (HTTP-statuskod: 504).
eventTimestamp | Tidsstämpel när händelsen skapades av tjänsten Azure bearbetning av begäran för händelsen.
submissionTimestamp | Tidsstämpel när händelsen blev tillgängliga för frågor.
subscriptionId | Azure-prenumerationen som den här händelsen loggades.
status | Sträng som beskriver status för åtgärden. Några vanliga värden är: **igång**, **pågår**, **lyckades**, **misslyckades**, **Active**, och **Löst**.
operationName | Namnet på åtgärden.
category | Den här egenskapen är alltid **ServiceHealth**.
resourceId | Resurs-ID för resursen påverkas.
Properties.title | Lokaliserade rubriken för den här kommunikationen. Engelska är standard.
Properties.Communication | Lokaliserad information om kommunikationen med HTML-kod. Engelska är standard.
Properties.incidentType | Ett av följande värden: **AssistedRecovery**, **ActionRequired**, **Information**, **Incident**,  **Underhåll**, eller **säkerhet**.
Properties.trackingId | Som den här händelsen är associerad med incidenten. Används för att korrelera händelser relaterade till en incident.
Properties.impactedServices | En ESC JSON-blob som beskriver de tjänster och regioner som påverkas av incidenten. Egenskapen innehåller en lista över tjänster, som har en **ServiceName**, och en lista över berörda regioner som har en **RegionName**.
Properties.defaultLanguageTitle | Kommunikation på engelska.
Properties.defaultLanguageContent | Kommunikation på engelska som HTML-kod eller oformaterad text.
Properties.Stage | Möjliga värden för **AssistedRecovery**, **ActionRequired**, **Information**, **Incident**, och **säkerhet**  är **Active** eller **löst**. För **Underhåll**, de är: **Active**, **planerad**, **InProgress**, **avbruten**, **Fram**, **löst**, eller **fullständig**.
Properties.communicationId | Kommunikation som är kopplad till den här händelsen.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visa din tjänstmeddelanden hälsotillstånd i Azure-portalen
1.  I den [Azure-portalen](https://portal.azure.com)väljer **övervakaren**.

    ![Skärmbild av Azure portal-menyn med Övervakare som valts](./media/monitoring-service-notifications/home-monitor.png)

    Azure-Monitor samlar alla dina övervakning inställningar och data till en samlad vy. Först öppnas den i avsnittet **Aktivitetslogg**.

3.  Välj **aviseringar**.

    ![Skärmbild av övervaka aktivitetsloggen, med aviseringar som har valts](./media/monitoring-service-notifications/service-health-summary.png)
4. Välj **+ Lägg till aktivitet loggen avisering**, och skapar en avisering så får du ett meddelande för framtida tjänstmeddelanden. Mer information finns i [Skapa aktivitet loggen aviseringar på tjänstmeddelanden](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>Nästa steg
Ta emot [aviseringar när ett meddelande om tjänstens hälsa](monitoring-activity-log-alerts-on-service-notifications.md) skickas.  
Lär dig mer om [aktivitet loggen aviseringar](monitoring-activity-log-alerts.md).
