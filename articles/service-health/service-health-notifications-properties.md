---
title: Vad är hälsomeddelanden för Azure-tjänsten?
description: Med hälsomeddelanden för tjänsten kan du visa tjänsthälsomeddelanden som publicerats av Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653976"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen

Tjänsthälsomeddelanden publiceras av Azure och innehåller information om resurserna under din prenumeration. Dessa meddelanden är en underklass av aktivitetslogghändelser och finns även i aktivitetsloggen. Hälsomeddelanden för tjänster kan vara informativa eller användbara, beroende på klass.

Det finns olika klasser av service hälsa anmälningar:  

- **Åtgärder som krävs:** Azure kanske märker något ovanligt hända på ditt konto och arbeta med dig för att åtgärda detta. Azure skickar ett meddelande som beskriver vilka åtgärder du behöver vidta eller hur du kontaktar Azure-teknik eller support.  
- **Incident:** En händelse som påverkar tjänsten påverkar för närvarande en eller flera av resurserna i prenumerationen.  
- **Underhåll:** En planerad underhållsaktivitet som kan påverka en eller flera av resurserna under prenumerationen.  
- **Information:** Potentiella optimeringar som kan bidra till att förbättra resursanvändningen. 
- **Säkerhet:** Brådskande säkerhetsrelaterad information om dina lösningar som körs på Azure.

Varje tjänst hälsoavisering innehåller information om omfattningen och inverkan på dina resurser. Detaljer inkluderar:

Egenskapsnamn | Beskrivning
-------- | -----------
Kanaler | Ett av följande värden: **Admin** eller **Operation**.
correlationId | Vanligtvis ett GUID i strängformat. Händelser som tillhör samma åtgärd delar vanligtvis samma correlationId.
eventDataId | Den unika identifieraren för en händelse.
händelseNamn | Titeln på en händelse.
nivå | Nivån på en händelse
resursProviderNamn | Namnet på resursprovidern för den påverkade resursen.
resourceType| Resurstypen för den påverkade resursen.
understatus | Vanligtvis HTTP-statuskoden för motsvarande REST-anrop, men kan också innehålla andra strängar som beskriver en understatus. Till exempel: OK (HTTP-statuskod: 200), Skapad (HTTP-statuskod: 201), Accepterad (HTTP-statuskod: 202), Inget innehåll (HTTP-statuskod: 204), Felaktig begäran (HTTP-statuskod: 400), Hittades inte (HTTP-statuskod: 404), Konflikt (HTTP-statuskod: 409), Intern server Fel (HTTP-statuskod: 500), Tjänsten är inte tillgänglig (HTTP-statuskod: 503) och TIMEOUT för gateway (HTTP-statuskod: 504).
händelseTimestamp | Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen.
inlämningTimestamp | Tidsstämpel när händelsen blev tillgänglig för frågor.
subscriptionId | Azure-prenumerationen där den här händelsen loggades.
status | Sträng som beskriver åtgärdens status. Några vanliga värden är: **Startat**, **Pågår**, **Lyckades**, **Misslyckades**, **Aktiv**och **Löst**.
operationName | Namnet på operationen.
category | Den här egenskapen är alltid **ServiceHealth.**
resourceId | Resurs-ID för den påverkade resursen.
Egenskaper.titel | Den lokaliserade titeln för den här kommunikationen. Engelska är standard.
Egenskaper.kommunikation | Den lokaliserade informationen om kommunikationen med HTML-markering. Engelska är standard.
Properties.incidentType | Ett av följande värden: **ActionRequired**, **Informational,** **Incident,** **Underhåll**eller **Säkerhet**.
Properties.trackingId | Incidenten som händelsen är associerad med. Använd detta för att korrelera händelser relaterade till en incident.
Egenskaper.påverkadeTjänster | En förrymd JSON blob som beskriver de tjänster och regioner som påverkas av händelsen. Egenskapen innehåller en lista över tjänster, som var och en har ett **ServiceName,** och en lista över påverkade regioner, som alla har ett **RegionName**.
Egenskaper.standardSpråkTitle | Kommunikationen på engelska.
Egenskaper.defaultLanguageContent | Kommunikationen på engelska som antingen HTML-markering eller oformaterad text.
Egenskaper.scen | De möjliga värdena för **Incident**och **Säkerhet** är **aktiva,** **lösta** eller **RCA**. För **ActionRequired** eller **Informational** är det enda värdet **Aktivt.** För **underhåll** är de: **Aktiv**, **Planerad**, **InProgress**, **Avbruten**, **Omplanerad,** **Löst**eller **Slutförd**.
Egenskaper.communicationId | Den kommunikation som den här händelsen är associerad med.

### <a name="details-on-service-health-level-information"></a>Information om hälsonivå för tjänster

**Åtgärd krävs** (properties.incidentType == ActionRequired)
- Informationsåtgärd - Administratörsåtgärd krävs för att förhindra påverkan på befintliga tjänster.
    
**Underhåll** (properties.incidentType == Underhåll)
- Varning - Nödunderhåll
- Informations- och standardplanerat underhåll

**Information** (properties.incidentType == Information)
- Informationsbaserad - Administratör kan krävas för att förhindra påverkan på befintliga tjänster.

**Säkerhet** (properties.incidentType == Säkerhet)
- Varning - Säkerhetsrådgivning som påverkar befintliga tjänster och kan kräva administratörsåtgärder.
- Informations- Säkerhetsrådgivning som påverkar befintliga tjänster.

**Serviceproblem** (properties.incidentType == Incident)
- Fel – Omfattande problem med åtkomst till flera tjänster i flera regioner påverkar en bred uppsättning kunder.
- Varning - Problem med åtkomst till specifika tjänster och/eller specifika regioner påverkar en delmängd av kunderna.
- Informations- Problem som påverkar hanteringsåtgärder och/eller svarstid, påverkar inte tjänstens tillgänglighet.
