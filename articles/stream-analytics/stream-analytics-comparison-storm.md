---
title: 'Analytics-plattformar: Apache Storm jämförelse med Azure Stream Analytics'
description: Vägledning för att välja en molnplattform analytics med hjälp av en Apache Storm jämförelse till Stream Analytics. Förstå funktioner och skillnader.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2017
ms.openlocfilehash: 94ac3c0075014a3e014ede8104a6aa259527a0ae
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Om du väljer en streaming analytics-plattform: jämföra Apache Storm och Azure Stream Analytics
Azure tillhandahåller flera lösningar för att analysera strömmande data: [Azure Streaming Analytics](https://docs.microsoft.com/azure/stream-analytics/) och [Apache Storm på Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Båda analytics-plattformar tillhandahåller fördelarna med en PaaS-lösning. Men plattformarna som har vissa viktiga skillnader i deras funktioner samt i hur du konfigurerar och hanteras. 

Den här artikeln innehåller en sida-vid-sida-jämförelse av funktioner som hjälper dig att välja mellan Apache Storm och Azure Stream Analytics som en plattform i molnet analytics. 

## <a name="general-features"></a>Allmänna funktioner

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Öppna datakällan?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nej. Azure Stream Analytics är en Microsoft-generiska erbjudande.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ja. Apache Storm är en teknik för Apache licensierad.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft support?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ja </p>
            </td>
            <td width="246" valign="top">
                <p>
Ja </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Maskinvarukrav</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ingen. Azure Stream Analytics är en Azure-tjänst.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ingen. Apache Storm är ett Azure-tjänst.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Översta enhet</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Användare distribuera och övervaka strömmande jobb.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Användare distribuera och övervaka ett hela kluster, vilket kan vara värd för flera samtidiga jobb samt andra arbetsbelastningar (inklusive batch).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Priser</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Pris per volym av bearbetade data och antalet strömningsenheter som behövs per timme som jobbet körs. 
                </p>
                    <p>Mer information finns i <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Stream Analytics priser</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Enhet för inköp kluster-baserad och debiteras baserat på den tid som körs på klustret, oberoende av jobb som har distribuerats.
                </p>
                <p>
Mer information finns i <a href="http://azure.microsoft.com/pricing/details/hdinsight/">HDInsight priser</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Redigering

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funktioner: SQL DSL?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ja. Stream Analytics ger ett SQL-liknande språk för att skapa transformationer.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nej. Användare skriva koden i Java- eller C# eller använda Trident-API: er.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funktioner: Temporala operatorer?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Fönsteraggregeringar och temporala kopplingar stöds som standard.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Temporala operatorer måste implementeras av användaren.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Utvecklingsarbetet</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Användare kan skapa, felsöka och övervaka jobb via Azure-portalen med exempeldata som härletts från en direktsänd dataström.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Användare med hjälp av .NET kan utveckla, felsöka och övervaka via Visual Studio. Användare som använder Java eller andra språk kan använda valfri IDE.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Stöd för fjärrfelsökning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Grundläggande jobbloggar för status och åtgärder är tillgängliga för felsökning. Stream Analytics kan för närvarande inte användare som anger vilket innehåll eller hur mycket innehåll som ingår i loggarna (d.v.s. utförligt läge).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Detaljerade loggar är tillgängliga. Användarna kan öppna loggar i Visual Studio eller genom att logga in på klustret och direkt åtkomst till loggarna.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Utökningsbarhet med anpassad kod?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stöd för delvis med JavaScript UDF: er. Mer information finns i <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">JavaScript UDF-integration</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ja. Användare kan skriva anpassade kod i C#, Java eller andra språk som stöds på Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Datakällor (indata) och utdata ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Inkommande datakällor</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Azure Event Hubs och Azure Blob storage.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Kopplingar är tillgängliga för Händelsehubbar i Azure, Azure Service Bus, Kafka och mycket mer. Användare kan skapa ytterligare anslutningar med anpassad kod.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Indata-format</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Användare kan implementera valfritt format med anpassad kod.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>utdata</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ett direktuppspelningsjobb kan ha flera utdata. Stöds utdata är Azure Event Hubs, Azure Blob storage, Azure Table storage, Azure SQL DB och Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm stöder många utdata i en topologi och varje utdata kan ha en egen kod för nedströms bearbetning. Storm innehåller kopplingar för Power BI, Azure Event Hubs, Azure Blob storage, Azure Cosmos DB, SQL och HBase. Användare kan skapa ytterligare anslutningar med anpassad kod.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Datakodning format</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Data måste vara formaterad med UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Användare kan implementera en data-kodningsformat med anpassad kod.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Hantering och åtgärder ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Distributionsmodell för jobb</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Azure-portalen, PowerShell och REST API: er.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Azure-portalen, PowerShell, Visual Studio och REST API: er.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Övervaka (statistik, räknare, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Övervakning har implementerats med hjälp av Azure-portalen och REST API: er. Användare kan också konfigurera Azure-aviseringar.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Övervakning har implementerats med hjälp av Storm-Användargränssnittet och REST API: er.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Skalbarhet</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Skalbarhet bestäms av antalet Streaming Units (SUs) för varje jobb. Varje Streaming Unit bearbetar upp till 1 MB per sekund med ett maximalt 50 enheter. Mer information finns i <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">skala till öka genomflödet</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Skalbarhet bestäms av antalet noder i HDInsight Storm-kluster. Den övre gränsen för antalet noder definieras av användarens Azure kvoten.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Databearbetning gränser</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Användare kan öka databearbetning eller optimera kostnader genom att öka eller minska antalet enheter för strömning med en övre gräns på 1 GB per sekund.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Användare kan skala klusterstorleken uppåt eller nedåt.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Stoppa/Fortsätt</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stoppa och återuppta från senaste plats som har stoppats.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Stoppar och återupptar från senaste placera stoppad baserat på en vattenstämpel.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Uppdatering för tjänsten och framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Automatisk korrigering utan avbrott.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Automatisk korrigering utan avbrott.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Kontinuitet för företag genom en hög tillgänglighet med garanterad servicenivåavtal</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>SLA för 99,9% drifttid</li>
                <li>Automatisk återställning efter fel</li>
                <li>Inbyggda återställning av tillståndskänslig temporala operatorer</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
SLA för 99,9% drifttid för Storm-kluster. 
                </p>
                <p>
Apache Storm är en feltolerant strömmande plattform. Men är det användarens ansvar att se till att strömmande jobb körs utan avbrott.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Avancerade funktioner ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm på HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Sen ankomst och out-ordning händelsehantering</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Inbyggda konfigurerbara principer kan ordna om händelser, ta bort händelser eller ändra tidpunkt för händelsen.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Användare måste implementera logik för att hantera det här scenariot.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Referensdata</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Referensdata är tillgänglig från Azure Blob storage med maximalt 100 MB i cacheminnet. Referensdata uppdateras av tjänsten.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ingen begränsning datastorleken. Kopplingar är tillgängliga för HBase, Azure Cosmos DB, SQL Server och Azure. Användare kan skapa ytterligare anslutningar med anpassad kod. Referensdata måste uppdateras med anpassad kod.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integrering med Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Publicerade Azure Machine Learning-modeller kan konfigureras som funktioner när jobbet skapas. Mer information finns i <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">skala för Machine Learning funktioner</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tillgängliga via Storm bultar.
                </p>
            </td>
        </tr>
    </tbody>
</table>
