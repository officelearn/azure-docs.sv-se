---
title: Förstå kompatibilitetsnivån för Azure Stream Analytics-jobb
description: Lär dig hur du ställer in en kompatibilitetsnivå för Azure Stream Analytics-jobb och större ändringar i senaste kompatibilitetsnivå
services: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: 83bbb777f5af6d29736db3b53ca39c449402c78e
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977721"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitetsnivån för Azure Stream Analytics-jobb
 
Kompatibilitetsnivån refererar till release-specifika funktioner för ett Azure Stream Analytics-tjänsten. Azure Stream Analytics är en hanterad tjänst med regelbundna funktionsuppdateringar och prestanda. Vanligtvis blir uppdateringar automatiskt tillgängliga för slutanvändare. Vissa nya funktioner kan dock medföra större ändringar sådana som-ändring i beteendet för ett befintligt jobb, ändra i de processer som förbrukar data från dessa jobb osv. En kompatibilitetsnivå som används för att representera en större ändring som introducerades i Stream Analytics. Större ändringar introduceras alltid med en ny kompatibilitetsnivå. 

Kompatibilitetsnivån ser till att befintliga jobb kan köras utan några fel. När du skapar ett nytt Stream Analytics-jobb är en bra idé att skapa den med hjälp av senaste kompatibilitetsnivå som är tillgängliga för dig. 
 
## <a name="set-a-compatibility-level"></a>Ange en kompatibilitetsnivå 

Kompatibilitetsnivån styr beteende under körning av ett stream analytics-jobb. Du kan ange kompatibilitetsnivån för ett Stream Analytics-jobb med hjälp av portalen eller med hjälp av den [skapa jobbet REST API-anrop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics stöder för närvarande två kompatibilitet nivåer – ”1.0” och ”1.1”. Kompatibilitetsnivå är som standard ”1.0”, som introducerades vid den allmänna tillgängligheten av Azure Stream Analytics. Om du vill uppdatera standardvärdet, navigera till ditt befintliga Stream Analytics-jobb > Välj den **kompatibilitetsnivå** alternativet i **konfigurera** och ändra värdet. 

Se till att du stoppa jobbet innan du uppdaterar kompatibilitetsnivå. Du kan inte uppdatera kompatibilitetsnivå om jobbet är i körningstillstånd. 

![Kompatibilitetsnivån i portalen](media\stream-analytics-compatibility-level/image1.png)

 
När du uppdaterar kompatibilitetsnivå, verifierar T-SQL-kompilatorn jobbet med den syntax som motsvarar den valda kompatibilitetsnivån. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Större ändringar i den senaste kompatibilitetsnivån (1.1)

Följande viktiga ändringar har introducerats i kompatibilitetsnivå 1.1:

* **Service Bus XML-format**  

  * **tidigare versioner:** Azure Stream Analytics används DataContractSerializer, så att innehållet i meddelandet ingår XML-taggar. Exempel:
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ ”SensorId”: ”1”, ”temperatur”: 64\}\u0001 

  * **aktuell version:** meddelandeinnehållet innehåller stream direkt med ingen ytterligare taggar. Exempel:
  
   {”SensorId”: ”1”, ”temperatur”: 64} 
 
* **Bevara skiftlägeskänslighet för fältnamn**  

  * **tidigare versioner:** fältnamn ändrades till gemener när bearbetas av Azure Stream Analytics-motorn. 

  * **aktuell version:** skiftlägeskänslighet bevaras för fältnamn när de bearbetas av Azure Stream Analytics-motorn. 

  > [!NOTE] 
  > Bevara skiftlägeskänslighet är ännu inte tillgängligt för Stream Analytics-jobb med hjälp av Edge-miljö. Därför kan konverteras alla fältnamn till gemener om ditt jobb finns i Microsoft Edge. 

* **FloatNaNDeserializationDisabled**  

  * **tidigare versioner:** CREATE TABLE-kommando inte att filtrera händelser med NaN (inte ett tal. Till exempel oändligt, -Infinity) i en kolumn för FLYTTAL anger eftersom de inte uppfyller det dokumenterade intervallet för dessa siffror.

  * **aktuell version:** CREATE TABLE kan du ange ett starkt schema. Stream Analytics-motorn validerar att informationen som överensstämmer med det här schemat. Med den här modellen kan kommandot Filtrera händelser med NaN-värden. 

* **Inaktivera automatisk upcast för datetime-strängar i JSON.**  

  * **tidigare versioner:** JSON-parsern skulle automatiskt ”uppåt” sträng värden med datum / / tidszonsinformation för DateTime anger och sedan konvertera den till UTC. Detta resulterade i att förlora Tidszonsinformationen.

  * **aktuell version:** det finns inga fler automatiskt ”uppåt” av strängvärden med datum / / tidszonsinformation för DateTime-typen. Därför kan sparas informationen om tidszonen. 

## <a name="next-steps"></a>Nästa steg
* [Felsöka Azure Stream Analytics-indata](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health-blad](stream-analytics-resource-health.md)