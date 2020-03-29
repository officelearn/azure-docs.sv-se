---
title: Visualisera nätverkstrafikmönster med verktyg med öppen källkod
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du använder nätverksbevakningspaketfångst med Capanalysis för att visualisera trafikmönster till och från dina virtuella datorer.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: f36db28b58cd57b6407019b378a82632aa6c6228
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840663"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualisera nätverkstrafikmönster till och från virtuella datorer med hjälp av verktyg med öppen källkod

Paketinsamlingar innehåller nätverksdata som gör att du kan utföra nätverksasensiker och djuppaketinspektion. Det finns många öppna källverktyg som du kan använda för att analysera paketinsamlingar för att få insikter om ditt nätverk. Ett sådant verktyg är CapAnalysis, en öppen källkod paket fånga visualiseringsverktyg. Visualisera paketinsamlingsdata är ett värdefullt sätt att snabbt härleda insikter om mönster och avvikelser i nätverket. Visualiseringar ger också ett sätt att dela sådana insikter på ett lätt förbrukningsbart sätt.

Azures Network Watcher ger dig möjlighet att samla in data genom att låta dig utföra paketinsamlingar i nätverket. Den här artikeln ger en genomgång av hur du visualiserar och får insikter från paketinfångningar med CapAnalysis med Network Watcher.

## <a name="scenario"></a>Scenario

Du har ett enkelt webbprogram som distribueras på en virtuell dator i Azure som vill använda verktyg med öppen källkod för att visualisera nätverkstrafiken för att snabbt identifiera flödesmönster och eventuella avvikelser. Med Network Watcher kan du hämta en paketfångst av nätverksmiljön och direkt lagra den på ditt lagringskonto. CapAnalysis kan sedan inta paketet fånga direkt från lagring blob och visualisera dess innehåll.

![scenario][1]

## <a name="steps"></a>Steg

### <a name="install-capanalysis"></a>Installera CapAnalysis

Om du vill installera CapAnalysis på en virtuell dator https://www.capanalysis.net/ca/how-to-install-capanalysiskan du läsa de officiella instruktionerna här .
För åtkomst till CapAnalysis på distans måste du öppna port 9877 på den virtuella datorn genom att lägga till en ny inkommande säkerhetsregel. Mer information om hur du skapar regler i Nätverkssäkerhetsgrupper finns [i Skapa regler i en befintlig NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). När regeln har lagts till bör du kunna komma åt CapAnalysis från`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Använda Azure Network Watcher för att starta en paketfångstsession

Med Network Watcher kan du samla in paket för att spåra trafik in och ut ur en virtuell dator. Du kan läsa instruktionerna på [Hantera paketinsamlingar med Network Watcher](network-watcher-packet-capture-manage-portal.md) för att starta en paketfångstsession. En paketfångst kan lagras i en lagringsblob som ska nås av CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Ladda upp en paketfångst till CapAnalysis
Du kan direkt ladda upp en paketfångst som tagits av nätverksbevakaren med hjälp av fliken "Importera från URL" och tillhandahålla en länk till lagringsblobben där paketfångsten lagras.

När du tillhandahåller en länk till CapAnalysis ska du lägga till en SAS-token i lagringsblobb-URL:en.  Det gör du genom att navigera till Signature för delad åtkomst från lagringskontot, ange tillåtna behörigheter och trycka på knappen Generera SAS för att skapa en token. Du kan sedan lägga till SAS-token i paketfångad lagringsblobb-URL:en.

Den resulterande webbadressen ser ut ungefär så här:http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analysera paketinfångningar

CapAnalysis erbjuder olika alternativ för att visualisera ditt paket fånga, var och en ger analys från ett annat perspektiv. Med dessa visuella sammanfattningar kan du förstå dina nätverkstrafiktrender och snabbt upptäcka ovanlig aktivitet. Några av dessa funktioner visas i följande lista:

1. Flödestabeller

    Den här tabellen ger dig en lista över flöden i paketdata, tidsstämpeln som är associerad med flödena och de olika protokoll som är associerade med flödet samt käll- och mål-IP.

    ![capanalysis flödessida][5]

1. Översikt över protokoll

    I den här rutan kan du snabbt se distributionen av nätverkstrafik över de olika protokollen och geografiska områden.

    ![capanalysis protokoll översikt][6]

1. Statistik

    I det här fönstret kan du visa nätverkstrafikstatistik – byte som skickas och tas emot från käll- och mål-IP-adresser, flöden för var och en av käll- och mål-IP-adresser, protokoll som används för olika flöden och varaktigheten av flödena.

    ![capanalysis statistik][7]

1. Geomap (geografiskt)

    I det här fönstret får du en kartvy över nätverkstrafiken, med färger som skalas till trafikvolymen från varje land/region. Du kan välja markerade länder/regioner om du vill visa ytterligare flödesstatistik, till exempel andelen data som skickas och tas emot från Internet-adresser i det landet/regionen.

    ![geokarta][8]

1. Filter

    CapAnalysis tillhandahåller en uppsättning filter för snabb analys av specifika paket. Du kan till exempel välja att filtrera data efter protokoll för att få specifika insikter om den delmängden av trafiken.

    ![filter][11]

    Besök [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) för att lära dig mer om alla CapAnalysis funktioner.

## <a name="conclusion"></a>Slutsats

Med Network Watchers paketinspelningsfunktion kan du samla in de data som behövs för att utföra nätverkstekniker och bättre förstå nätverkstrafiken. I det här fallet visade vi hur paketinsamlingar från Network Watcher enkelt kan integreras med visualiseringsverktyg med öppen källkod. Genom att använda verktyg med öppen källkod, till exempel CapAnalysis för att visualisera paketinsamlingar, kan du utföra djup paketinspektion och snabbt identifiera trender i nätverkstrafiken.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om NSG-flödesloggar kan du besöka [NSG Flow-loggar](network-watcher-nsg-flow-logging-overview.md)

Lär dig hur du visualiserar dina NSG-flödesloggar med Power BI genom att besöka [Visualize NSG-flöden loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
