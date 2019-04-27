---
title: Visualisera nätverkstrafikmönster med Azure Network Watcher och verktyg för öppen källkod | Microsoft Docs
description: Den här sidan beskriver hur du använder Network Watcher-infångade med Capanalysis för att visualisera trafikmönster till och från dina virtuella datorer.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7c239bbf577645ddb8ab12c525d1b3a8832421df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680563"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualisera nätverkstrafikmönster till och från dina virtuella datorer med hjälp av verktyg för öppen källkod

Fånga in innehålla nätverksdata som gör det möjligt att utföra nätverk datautredning och djup paketinspektion. Det finns många öppnas käll-verktyg som du kan använda för att analysera infångade paket för att få insikter om ditt nätverk. Ett sådant verktyg är CapAnalysis, ett verktyg med öppen källkod paket capture visualisering. Visualisera data för packet-capture är ett bra sätt att snabbt härleda insikter om mönster och avvikelser i nätverket. Visualiseringar ger också möjlighet att dela sådan analys på ett lättanvänt sätt.

Azure Network Watcher ger dig möjlighet att samla in data genom att du kan utföra infångade paket i nätverket. Den här artikeln innehåller en genomgång av hur du visualiserar och få insikter från paketet fångar med Network Watcher CapAnalysis.

## <a name="scenario"></a>Scenario

Du har en enkel webbapp distribuerats på en virtuell dator i Azure ska kunna använda open source-verktyg för att visualisera dess nätverkstrafik för att snabbt identifiera flow mönster och avvikelser möjligt. Med Network Watcher kan du hämta ett infångat paket för din nätverksmiljö och lagra dem direkt på ditt lagringskonto. CapAnalysis kan sedan mata in infångade direkt från blob storage och visualisera dess innehåll.

![scenario][1]

## <a name="steps"></a>Steg

### <a name="install-capanalysis"></a>Installera CapAnalysis

Om du vill installera CapAnalysis på en virtuell dator som du kan referera till de officiella anvisningarna här https://www.capanalysis.net/ca/how-to-install-capanalysis.
Åtkomst CapAnalysis via en fjärranslutning måste du öppna port 9877 på den virtuella datorn genom att lägga till en ny inkommande regel. Mer information om hur du skapar regler i Nätverkssäkerhetsgrupper som avser [skapa regler i en befintlig NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). När regeln har lagts ska du kunna komma åt CapAnalysis från `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Använd Azure Network Watcher för att starta avbildningssessionen ett paket

Network Watcher kan du fånga paket för att spåra trafik in och ut från en virtuell dator. Du kan läsa anvisningarna i [fjärrinfångade hantera paket med Network Watcher](network-watcher-packet-capture-manage-portal.md) att starta en packet capture. Ett infångat kan lagras i en storage-blob som kan nås av CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Ladda upp ett infångat paket till CapAnalysis
Du kan ladda upp ett infångat vidtas av network watcher använda fliken ”importera från URL” och ge en länk till blob storage där paketfångsten lagras direkt.

När du anger en länk till CapAnalysis, se till att lägga till en SAS-token i URL: en för storage blob.  Gör du genom att gå till signatur för delad åtkomst från storage-kontot, ange tillåtna behörigheter och tryck på knappen Generera SAS för att skapa en token. Du kan sedan lägga till SAS-token till packet capture storage blob URL.

URL som bildas ser ut ungefär som följande URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Fjärrinfångade analysera paket

CapAnalysis erbjuder olika alternativ för att visualisera dina paketinsamling, varje tillhandahåller analys från ett annat perspektiv. Du kan förstå dina nätverk trafik trender och snabbt upptäcka någon ovanlig aktivitet med dessa visuella sammanfattningar. Några av dessa funktioner visas i listan nedan:

1. Flow-tabeller

    Den här tabellen visas listan över flöden i datapaketet, tidsstämpel som är associerade med flödena och olika protokoll som är associerade med flow, samt källans och målets IP.

    ![capanalysis flow sidan][5]

1. Protokollöversikt

    Det här fönstret kan du snabbt se fördelningen av nätverkstrafik via olika protokoll och geografiska områden.

    ![capanalysis Protokollöversikt][6]

1. Statistik

    Det här fönstret kan du visa trafik nätverksstatistik – byte skickas och tas emot från källans och målets IP-adresser, flöden för var och en av källans och målets IP-adresser, protokoll som används för olika flöden och varaktigheten för flöden.

    ![capanalysis statistik][7]

1. Geomap

    Det här fönstret innehåller en karta vy över din nätverkstrafik med färger skala till vilken trafikvolym från varje land/region. Du kan välja markerade länder att visa ytterligare flow statistik, som del av data som skickas och tas emot från IP-adresser i det landet.

    ![geomap][8]

1. Filter

    CapAnalysis innehåller en uppsättning filter för snabb analys av specifika paket. Du kan exempelvis välja att filtrera data av protokollet specifika insikter på den delmängden av trafik.

    ![filter][11]

    Besök [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) mer information om alla CapAnalysis funktioner.

## <a name="conclusion"></a>Sammanfattning

Network Watcher packet-avbildningsfunktionen kan du samla in nödvändiga data för att utföra nätverk datautredning och bättre förstå din nätverkstrafik. I det här scenariot visade vi hur infångade paket från Network Watcher enkelt kan integreras med öppen källkod visualiseringsverktyg. Du kan utföra djup paketinspektion och snabbt identifiera trender i din nätverkstrafik med hjälp av verktyg för öppen källkod, till exempel CapAnalysis för att visualisera paket insamlingar.

## <a name="next-steps"></a>Nästa steg

Läs mer om NSG-flödesloggar [NSG-flödesloggarna](network-watcher-nsg-flow-logging-overview.md)

Lär dig att visualisera dina NSG-flödesloggar med Power BI genom att besöka [visualisera NSG-flödesloggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
