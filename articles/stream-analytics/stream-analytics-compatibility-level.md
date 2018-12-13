---
title: Förstå kompatibilitetsnivån för Azure Stream Analytics-jobb
description: Lär dig hur du ställer in en kompatibilitetsnivå för Azure Stream Analytics-jobb och större ändringar i senaste kompatibilitetsnivå
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: b0e0f26abbf8eb5cbf1cf9ba2014204d773ae15d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187321"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitetsnivån för Azure Stream Analytics-jobb
 
Kompatibilitetsnivån refererar till release-specifika funktioner för ett Azure Stream Analytics-tjänsten. Azure Stream Analytics är en hanterad tjänst med regelbundna funktionsuppdateringar och prestanda. Vanligtvis blir uppdateringar automatiskt tillgängliga för slutanvändare. Vissa nya funktioner kan dock medföra större ändringar sådana som-ändring i beteendet för ett befintligt jobb, ändra i de processer som förbrukar data från dessa jobb osv. En kompatibilitetsnivå som används för att representera en större ändring som introducerades i Stream Analytics. Större ändringar introduceras alltid med en ny kompatibilitetsnivå. 

Kompatibilitetsnivån ser till att befintliga jobb kan köras utan några fel. När du skapar ett nytt Stream Analytics-jobb är en bra idé att skapa den med hjälp av senaste kompatibilitetsnivå som är tillgängliga för dig. 
 
## <a name="set-a-compatibility-level"></a>Ange en kompatibilitetsnivå 

Kompatibilitetsnivån styr beteende under körning av ett stream analytics-jobb. Du kan ange kompatibilitetsnivån för ett Stream Analytics-jobb med hjälp av portalen eller med hjälp av den [skapa jobbet REST API-anrop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics stöder för närvarande två kompatibilitet nivåer – ”1.0” och ”1.1”. Kompatibilitetsnivå är som standard ”1.0”, som introducerades vid den allmänna tillgängligheten av Azure Stream Analytics. Om du vill uppdatera standardvärdet, navigera till ditt befintliga Stream Analytics-jobb > Välj den **kompatibilitetsnivå** alternativet i **konfigurera** och ändra värdet. 

Se till att du stoppa jobbet innan du uppdaterar kompatibilitetsnivå. Du kan inte uppdatera kompatibilitetsnivå om jobbet är i körningstillstånd. 

![Stream Analytics kompatibilitetsnivå i Azure-portalen](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
När du uppdaterar kompatibilitetsnivå, verifierar T-SQL-kompilatorn jobbet med den syntax som motsvarar den valda kompatibilitetsnivån. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Större ändringar i den senaste kompatibilitetsnivån (1.1)

Följande viktiga ändringar har introducerats i kompatibilitetsnivå 1.1:

* **Service Bus XML-format**  

  * **Tidigare versioner:** Azure Stream Analytics används DataContractSerializer, så att innehållet i meddelandet ingår XML-taggar. Exempel:
    
    @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ ”SensorId”: ”1”, ”temperatur”: 64\}\u0001 

  * **aktuell version:** Meddelandeinnehåll innehåller stream direkt med ingen ytterligare taggar. Exempel:
  
    {”SensorId”: ”1”, ”temperatur”: 64} 
 
* **Bevara skiftlägeskänslighet för fältnamn**  

  * **Tidigare versioner:** Fältnamn ändrades till gemener när bearbetas av Azure Stream Analytics-motorn. 

  * **aktuell version:** skiftlägeskänslighet bevaras för fältnamn när de bearbetas av Azure Stream Analytics-motorn. 

    > [!NOTE] 
    > Bevara skiftlägeskänslighet är ännu inte tillgängligt för Stream Analytics-jobb med hjälp av Edge-miljö. Därför kan konverteras alla fältnamn till gemener om ditt jobb finns i Microsoft Edge. 

* **FloatNaNDeserializationDisabled**  

  * **Tidigare versioner:** CREATE TABLE-kommando inte att filtrera händelser med NaN (inte ett tal. Till exempel oändligt, -Infinity) i en kolumn för FLYTTAL anger eftersom de inte uppfyller det dokumenterade intervallet för dessa siffror.

  * **aktuell version:** Skapa tabell kan du ange ett starkt schema. Stream Analytics-motorn validerar att informationen som överensstämmer med det här schemat. Med den här modellen kan kommandot Filtrera händelser med NaN-värden. 

* **Inaktivera automatisk upcast för datetime-strängar i JSON.**  

  * **Tidigare versioner:** JSON-parsern skulle automatiskt ”uppåt” strängvärden med datum / / tidszonsinformation för DateTime-typ och sedan konvertera den till UTC. Detta resulterade i att förlora Tidszonsinformationen.

  * **aktuell version:** Det finns inga fler automatiskt ”uppåt” av strängvärden med datum / / tidszonsinformation för DateTime-typen. Därför kan sparas informationen om tidszonen. 

## <a name="next-steps"></a>Nästa steg
* [Felsöka Azure Stream Analytics-indata](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health-blad](stream-analytics-resource-health.md)
