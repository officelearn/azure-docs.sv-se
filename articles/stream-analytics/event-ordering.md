---
title: Konfigurera principer för Händelseordning för Azure Stream Analytics
description: Den här artikeln beskrivs hur du konfigurerar även ordning inställningarna i Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190568"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurera principer för Händelseordning för Azure Stream Analytics

Den här artikeln beskriver hur du konfigurerar och använder sen ankomst och out ordning händelse principer i Azure Stream Analytics. Dessa principer används bara när du använder den [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) -sats i frågan.

## <a name="event-time-and-arrival-time"></a>Tidpunkt för händelsen och ankomsttid

Ditt Stream Analytics-jobb kan bearbeta händelser baserat på något *händelsetid* eller *ankomsttid*. **Tid för händelse/program** är tidsstämpeln i händelsenyttolast (när händelsen skapades). **Ankomsttid** är tidsstämpel när händelsen togs emot på Indatakällan (Event Hubs/IoT Hub/Blob storage). 

Som standard Stream Analytics bearbetar händelser efter *ankomsttid*, men du kan välja att bearbeta händelser efter *händelsetid* med hjälp av [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) -sats i frågan. Sen ankomst och out ordning principer är gäller endast om du vill bearbeta händelser av tidpunkt för händelsen. Överväg att kraven på svarstid och är korrekt för ditt scenario när du konfigurerar de här inställningarna. 

## <a name="what-is-late-arrival-policy"></a>Vad är sent ankomst principen?

Händelser kommer ibland sent på grund av olika anledningar. Exempelvis kan en händelse som kommer sent 40 sekunder har händelsetid = 00:10:00 och ankomst tid = 40:10:00. Om du anger principen för sent ankomst till 15 sekunder och alla händelser som kommer senare än 15 sekunder tas antingen bort (inte bearbetas av Stream Analytics) eller har sina händelsetid justerade. I exemplet ovan som händelsen införda 40 sekunder sent (mer än principuppsättningen), justeras dess händelsetid till högsta av sen ankomst princip 00:10:25 (ankomsttid - principvärdet för sent ankomst). Sen ankomst standardprincipen för är 5 sekunder.

## <a name="what-is-out-of-order-policy"></a>Vad är out ordning principen? 

Händelsen kan komma till oordnade samt. När händelsetid justeras utifrån sent ankomst principen, kan du också välja att automatiskt ta bort eller ändra händelser som är out ordning. Om du ställer in den här principen till 8 sekunder sorteras eventuella händelser som kommer oordnade men inom 8-sekundersfönster av tidpunkt för händelsen. Som kommer senare ska vara antingen tappade eller justerade händelser till principvärdet för maximal out ordning. Standardprincipen för out ordning är 0 sekunder. 

## <a name="adjust-or-drop-events"></a>Ändra eller ta bort händelser

Om händelser anländer sent eller out ordning baserat på de principer som du har konfigurerat, kan du ta bort sådana händelser (inte bearbetas av Stream Analytics) eller har sina tidpunkt för händelsen som justeras.

Låt oss se ett exempel på dessa principer fungerar i praktiken.
<br> **Sen ankomst princip:** 15 sekunder
<br> **Princip för out ordning:** 8 sekunder

| Händelsen Nej. | Tidpunkt för händelse | Ankomsttid | System.Timestamp | Förklaring |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Händelsen anlänt sent och utanför toleransnivån. Tidpunkt för händelsen hämtar så justeras till högsta tolerans för sent ankomst.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Händelsen anlänt sent men inom toleransnivån. Tidpunkt för händelsen därför inte få justeras.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Händelsen hamnat i tid. Ingen justering som behövs.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Händelsen kommit ut ordning men 8 sekunder. Tidpunkt för händelsen därför inte få justeras. För analys, ska den här händelsen betraktas som föregående händelsenummer 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Händelsen kommit ut ordning och utanför tolerans på 8 sekunder. Därför justeras händelsetid till den maximala toleransnivån out ordning. |

## <a name="can-these-settings-delay-output-of-my-job"></a>De här inställningarna kan fördröja utdata för Mina jobb? 

Ja. Som standard anges out ordning principen till noll (00 minuter och 00 sekunder). Om du ändrar standardvärdet är din första jobbutdata fördröjd av det här värdet (eller senare). 

Om någon av partitionerna i dina indata inte ta emot händelser, bör du förväntar dig dina utdata till fördröjas med principvärdet för sent ankomst. Läs avsnittet InputPartition felet nedan om du vill ta reda på varför. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Kan jag se LateInputEvents meddelanden i min aktivitetsloggen

Dessa meddelanden visas om att har anlänt sent och är antingen tappade eller justerade händelser enligt din konfiguration. Du kan ignorera dessa meddelanden om du har konfigurerat principen för sent ankomst på rätt sätt. 

Exempel på det här meddelandet är: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Kan jag se InputPartitionNotProgressing i min aktivitetsloggen

Din Indatakällan (Event Hub/IoT Hub) sannolikt har flera partitioner. Azure Stream Analytics ger utdata för tidsstämpel t1 förrän alla partitioner som kombineras är minst tid t1. Anta exempelvis att frågan som läser från event hubs-partitionen som har två partitioner. Händelser är ett av partitioner, P1, tills tid t1. Den andra partitionen, P2, har händelser fram tid t1 + x. Sedan utdata till tid t1. Men om det finns en explicit Partition by-sats PartitionId, både partitioner förloppet oberoende av varandra. 

När flera partitioner från samma Indataströmmen kombineras, är sen ankomst toleransen den maximala tid som varje partition väntar tills nya data. Om det finns en partition i din Event Hub, eller om IoT Hub inte får indata, tidslinjen för den partitionen inte vidare tills når den slutet ankomst tolerans tröskelvärdet. Detta fördröjer dina utdata av sent ankomst tolerans tröskelvärdet. I sådana fall kan du se följande meddelande:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Det här meddelandet om att minst en partition i dina indata är tom och att fördröja dina utdata av sent ankomst tröskelvärdet. Om du vill lösa det, bör du antingen: 1. Se till att alla partitioner i Event Hub/IoT-hubben ta emot indata. 2. Använd Partition by PartitionID-sats i frågan. 

## <a name="next-steps"></a>Nästa steg
* [Överväganden för hantering av tid](stream-analytics-time-handling.md)
* [Mått som är tillgängliga i Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
