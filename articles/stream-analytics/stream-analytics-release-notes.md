---
title: "Viktig information för Stream Analytics | Microsoft Docs"
description: Stream Analytics viktig information
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/03/2017
ms.author: samacha
ms.openlocfilehash: 3251cd47bb917912d63330345dbf392e724448ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-release-notes"></a>Stream Analytics viktig information

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Anteckningar för 2017-06/14 uppdatering av Stream Analytics-verktyg för Visual Studio
Denna uppdatering är för våra Visual Studio Tools. Den här versionen innehåller följande nya funktioner:

| Rubrik | Beskrivning |
| --- | --- |
| Stöd för Java-skript redigeraren |Du kan få inbyggda java-skript editor upplevelse när du har skapat din java-skriptfunktioner.|
| Visa jobb kör tid felmeddelande | Om det finns körningsfel under jobbkörningen kan visa du dem på fliken fel genom att justera tid visningsfönstret. Som standard visas felmeddelanden för senaste 30 minuterna. |
| CSV- och Avro-stöd för lokal testning indata | Förutom JSON, kan nu du använda filformatet csv- och Avro för lokal testning indata.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Anteckningar för 2017-05/03 uppdatering av Stream Analytics
Denna uppdatering är vår felsökning dokumentationen-versionen.

Den [felsökningsguide för](stream-analytics-troubleshooting-guide.md) och andra dokument har släppts. Granska är feedback Välkommen.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Anteckningar för 2017-04/24 uppdatering av Stream Analytics-verktyg för Visual Studio
Denna uppdatering är för våra Visual Studio Tools. Den här versionen innehåller följande nya funktioner:

| Rubrik | Beskrivning |
| --- | --- |
| Visa lokala testresultat i Visual Studio | Om du vill visa det utgående resultatet av det lokala testet, tryck på RETUR i konsolfönstret utdata eller avslutar den. Resultatet visas i ett fönster i Visual Studio i tabellformat. |
| Utgående lokala resultatet i JSON-format | När du kör ett lokala test genereras det utgående resultatet som både JSON och CSV-filformat. |
| Förhandsgranska Blob/table storage indata/utdata-data | Genom att dubbelklicka på en blob eller tabell lagring o i vyn jobb kan förhandsgranska du data i Visual Studio enkelt. |
| Visa felmeddelande för indata/utdata | Om det finns vissa körningsfel som rör ditt jobb indata eller utdata ska visas i diagrammet jobbet där du kan hålla på den för att se det detaljerade felmeddelandet.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>Information om 2017-02/01 versionen av Stream Analytics
Den här versionen innehåller följande uppdatering:

| Rubrik | Beskrivning |
| --- | --- |
| Introduktion till JavaScript användardefinierade funktioner (UDF) |[Java användardefinierade funktioner](stream-analytics-javascript-user-defined-functions.md) är nu tillgängliga för ytterligare flexibilitet för att skapa frågor. |
| Introduktion till verktyg för Visual Studio och Stream Analytics |[Verktyg för Visual Studio](stream-analytics-tools-for-visual-studio.md) är nu tillgängliga för felsökning och större verktyget. |
| Introduktion till diagnostikloggning |[Diagnostikloggning](stream-analytics-job-diagnostic-logs.md) är nu tillgänglig för ytterligare alternativ för felsökning. |
| Introduktion till geospatiala funktioner |[Geospatiala funktioner](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) nu är allmänt tillgänglig. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Information om 2016-04-15 versionen av Stream Analytics
Den här versionen innehåller följande uppdatering:

| Rubrik | Beskrivning |
| --- | --- |
| Allmän tillgänglighet för Power BI-utdata |[Power BI-utdata](stream-analytics-power-bi-dashboard.md) nu är allmänt tillgänglig. 90 dagars auktorisering upphör att gälla för Power BI har tagits bort. Mer information om scenarier där tillstånd måste förnyas finns i [förnya auktorisering](stream-analytics-power-bi-dashboard.md#renew-authorization) avsnitt för att skapa en Power BI-instrumentpanel. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Information om 2016-03-03-versionen av Stream Analytics
Den här versionen innehåller följande uppdatering:

| Rubrik | Beskrivning |
| --- | --- |
| Nya Stream Analytics Query Language-objekt |SAQL har nu [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN sidan"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN sidan") och [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN sidan"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Information om 12/10/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdatering:

| Rubrik | Beskrivning |
| --- | --- |
| Uppdatering för REST API-version |REST API-version har uppdaterats till 2015-10-01. Information finns på MSDN vid [Stream Analytics Management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx) och [Machine Learning-integrering i Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Azure Machine Learning-integrering |Med den här versionen har stöd för Azure Machine Learning användardefinierade funktioner. Finns det [kursen](stream-analytics-machine-learning-integration-tutorial.md) mer information samt de [allmänna blogg meddelande](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Anteckningar för 11/12/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdatering:

| Rubrik | Beskrivning |
| --- | --- |
| Ny funktion i har VALTS |Välj i Stream Analytics har utökats för att tillåta * som en egenskapsaccessor för en kapslad post. Mer information finns i [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "komplexa datatyper"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Information om 22/10/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning |
| --- | --- |
| Ytterligare språk frågefunktioner |Stream Analytics har utökats frågespråket genom att inkludera följande funktioner: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [tak](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [VÅNING](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [LOGGA](https://msdn.microsoft.com/library/azure/mt605290.aspx), [KVADRAT](https://msdn.microsoft.com/library/azure/mt605288.aspx), och [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Ta bort sammanställd begränsningar |Den här versionen tar bort begränsningen av 15 aggregeringar i en fråga. Det finns nu ingen gräns för antalet mängder per fråga. |
| GRUPP av System.Timestamp extrafunktioner |Den [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) funktionen tillåter nu antingen window_type eller [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| Tillagda FÖRSKJUTNINGEN för rullande och Hopping windows |Som standard [rullande](https://msdn.microsoft.com/library/azure/dn835055.aspx) och [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows justeras mot noll tiden (1/1/0001 12:00:00 AM UTC). Den nya (valfria) parametern 'offsetsize' kan du ange en anpassad förskjutningen (eller justering). |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Anteckningar för 29/09/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning |
| --- | --- |
| Azure IoT Suite Public Preview |Stream Analytics ingår i den offentliga förhandsversionen av Azure IoT Suite. |
| Azure portal-integrering |Förutom fortsätter att vara i Azure-portalen, Stream Analytics är nu integrerat i den [Azure-portalen](https://azure.microsoft.com/overview/preview-portal/). Observera att Stream Analytics-funktioner i Preview-portalen för närvarande är en delmängd av funktionerna som erbjuds i Azure-portalen, utan stöd för webbläsarbaserad frågetestning, Power BI-utdata konfiguration, och bläddra till eller skapa nya inkommande och utgående resurserna i prenumerationer som du har åtkomst till. |
| Stöd för Cosmos-DB-utdata |Stream Analytics-jobb kan nu utdata till [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). |
| Stöd för IoT-hubb indata |Stream Analytics-jobb kan nu mata in data från IoT-hubbar. |
| TIMESTAMP BY för heterogena händelser |När en enda dataström innehåller flera händelsetyper med tidsstämplar i olika fält, kan du nu använda [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) med uttryck för att ange olika värdefält för varje fall. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Information om 09/10/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning |
| --- | --- |
| Stöd för PowerBI-grupper |Om du vill aktivera delning av data med andra Power BI-användare, Stream Analytics-jobb kan nu skriva till [PowerBI grupper](stream-analytics-define-outputs.md#power-bi) i Power BI-konto. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Information om 20/08/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning |
| --- | --- |
| Lägga till SENAST funktionen |Den [senaste](http://msdn.microsoft.com/library/mt421186.aspx) funktion är nu tillgängligt i Stream Analytics-jobb, så att du kan hämta den senaste händelsen i en händelseström inom ett angivet tidsintervall. |
| Nya funktioner för matris |Matrisen funktioner [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) och [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) är nu tillgängliga. |
| Den nya posten funktioner |Registrera funktioner [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) och [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) är nu tillgängliga. |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Information om 07/30/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning |
| --- | --- |
| Power BI Org Id frikopplat från Azure-Id |Den här funktionen kan [Power BI-utdata](stream-analytics-power-bi-dashboard.md) för ASA jobb under varje typ av Azure-konto (Live Id eller organisations-Id). Du kan dessutom ha en organisations-Id för din Azure-konto och använda en annan för auktorisering av Power BI-utdata. |
| Stöd för Service Bus-köer utdata |[Service Bus-köer](stream-analytics-define-outputs.md#service-bus-queues) utdata är nu tillgängliga i Stream Analytics-jobb. |
| Stöd för Service Bus-ämnen utdata |[Service Bus-ämnen](stream-analytics-define-outputs.md#service-bus-topics) utdata är nu tillgängliga i Stream Analytics-jobb. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Information om 07/09/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning |
| --- | --- |
| Anpassad Blob utdata partitionering |BLOB storage utdata kan nu ett alternativ för att ange hur ofta dessa BLOB skrivs utdata och struktur och format för utgående data sökväg mappstrukturen. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Information om 05/03/2015-versionen av Stream Analytics
Den här versionen innehåller följande uppdateringar:

| Rubrik | Beskrivning |
| --- | --- |
| Ökad maxvärdet för Out of Tolerance fönstret |Den maximala storleken för Out of ordning tolerans fönster är nu 59:59 (mm: ss) |
| JSON-utdataformat: Radseparerade eller matris |Det är nu ett alternativ när du använder Blob Storage eller Event Hub som antingen en matris av JSON-objekt eller genom att avgränsa JSON-objekt med en ny rad. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Information om 04/16/2015-versionen av Stream Analytics
| Rubrik | Beskrivning |
| --- | --- |
| Fördröjning i konfigurationen av Azure Storage-kontot |När du skapar ett Stream Analytics-jobb i en region för första gången, uppmanas du att skapa ett nytt lagringskonto eller ange ett befintligt konto för övervakning av Stream Analytics-jobb i området. På grund av latens Konfigurera övervakning, skapar en annan Stream Analytics-jobb i samma region inom 30 minuter Kommandotolken för att ange ett andra Storage-konto i stället för visar det nyligen konfigurerade i övervakning Lagringskonto listrutan. För att undvika att skapa ett onödiga Storage-konto, vänta i 30 minuter när du har skapat ett jobb i en region för första gången innan du etablerar ytterligare jobb i området. |
| Jobbet uppgradering |Stream Analytics stöder inte live redigeringar definition eller konfiguration av ett jobb som körs för tillfället. För att kunna ändra indata, utdata, fråga, skala eller konfigurationen av ett jobb som körs, måste du stoppa jobbet. |
| Datatyper härledas från Indatakällan |Om en CREATE TABLE-instruktion används Indatatyp är härledd från Indataformatet, till exempel alla fält i CSV är sträng. Fälten måste uttryckligen konverteras till rätt typ med CAST-funktion för att undvika fel av typen matchningsfel. |
| Fält saknas utdata som null-värden |Refererar till ett fält som inte finns i Indatakällan resulterar i null-värden i output-händelse. |
| MED rapporterna måste föregå SELECT-uttryck |SELECT-satser måste följa underfrågor som definierats i med instruktioner i din fråga. |
| Minnet är slut problemet |Streaming Analytics-jobb med en stor tolerans för out-ordning händelser och/eller komplexa frågor upprätthålla en stor mängd tillstånd kan orsaka att jobbet ska få slut på minne, vilket resulterar i ett jobb startas om. Åtgärder för start och stopp visas i jobbets åtgärdsloggar. Undvik problemet genom att skala ut frågan över flera partitioner. Den här begränsningen är adresserad av försämring av prestanda påverkas jobb i stället för att starta om dem i en framtida version. |
| Stora blob indata utan nyttolast tidsstämpel kan orsaka problem om minnet är slut |Förbrukar stora filer från Blob storage kan orsaka Stream Analytics-jobb kraschar om något tidsstämpelsfält inte har angetts via TIMESTAMP BY. Undvik problemet genom att hålla varje blob under 10 MB i storlek. |
| SQL-databas händelse volym begränsning |När du använder SQL-databas som en output-mål, kan mycket stora mängder utdata orsaka Stream Analytics-jobbet timeout. Lös problemet, minska volymen genom att använda mängdfunktioner eller filteroperatorerna eller välj Azure Blob storage- eller Händelsehubbar som ett mål för utdata i stället. |
| PowerBI datauppsättningar kan bara innehålla en tabell |PowerBI stöder inte mer än en tabell i den angivna datauppsättningen. |

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
