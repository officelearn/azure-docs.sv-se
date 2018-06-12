---
title: Vad är Azure-tjänstens hälsotillstånd meddelanden?
description: Meddelanden om hälsostatus kan du visa meddelanden om hälsotillstånd har publicerats av Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2017
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: d6a87b17041c4ce6cf41da863354ef5a2a37141c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264451"
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
nivå | Nivån för en händelse
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
Properties.incidentType | Ett av följande värden: **ActionRequired**, **Information**, **Incident**, **Underhåll**, eller **säkerhet**.
Properties.trackingId | Som den här händelsen är associerad med incidenten. Används för att korrelera händelser relaterade till en incident.
Properties.impactedServices | En ESC JSON-blob som beskriver de tjänster och regioner som påverkas av incidenten. Egenskapen innehåller en lista över tjänster, som har en **ServiceName**, och en lista över berörda regioner som har en **RegionName**.
Properties.defaultLanguageTitle | Kommunikation på engelska.
Properties.defaultLanguageContent | Kommunikation på engelska som HTML-kod eller oformaterad text.
Properties.Stage | Möjliga värden för **Incident**, och **säkerhet** är **aktiv,** **löst** eller **RCA**. För **ActionRequired** eller **Information** är det enda värdet **Active.** För **Underhåll** de: **Active**, **planerad**, **InProgress**, **avbruten**, **Fram**, **löst**, eller **fullständig**.
Properties.communicationId | Kommunikation som är kopplad till den här händelsen.

### <a name="details-on-service-health-level-information"></a>Information om servicenivåinformation för hälsotillstånd
  <ul>
    <li><b>Åtgärd krävs</b> (properties.incidentType == ActionRequired) <dl>
            <dt>Information</dt>
            <dd>Åtgärd av administratören krävs för att förhindra påverkan på befintliga tjänster</dd>
        </dl>
    </li>
    <li><b>Underhåll</b> (properties.incidentType == underhåll) <dl>
            <dt>Varning</dt>
            <dd>nödfall Underhåll<dd>
            <dt>Information</dt>
            <dd>standard planerat underhåll</dd>
        </dl>
    </li>
    <li><b>Information</b> (properties.incidentType == Information) <dl>
            <dt>Information</dt>
            <dd>Administratören kan krävas för att förhindra påverkan på befintliga tjänster</dd>
        </dl>
    </li>
    <li><b>Säkerhet</b> (properties.incidentType == säkerhet) <dl>
            <dt>Fel</dt>
            <dd>Omfattande problem att komma åt flera tjänster över flera regioner som påverkar ett stort antal kunder.</dd>
            <dt>Varning</dt>
            <dd>Problem med åtkomst till särskilda tjänster och/eller vissa regioner som påverkar en delmängd av kunder.</dd>
            <dt>Information</dt>
            <dd>Problem som påverkar hanteringsåtgärder och/eller fördröjning, inte påverka tjänsttillgängligheten.</dd>
        </dl>
    </li>
    <li><b>Tjänsten problem</b> (properties.incidentType == Incident) <dl>
            <dt>Fel</dt>
            <dd>Omfattande problem att komma åt flera tjänster över flera regioner som påverkar ett stort antal kunder.</dd>
            <dt>Varning</dt>
            <dd>Problem med åtkomst till särskilda tjänster och/eller vissa regioner som påverkar en delmängd av kunder.</dd>
            <dt>Information</dt>
            <dd>Problem som påverkar hanteringsåtgärder och/eller fördröjning, inte påverka tjänsttillgängligheten.</dd>
        </dl>
    </li>
  </ul>

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
