---
title: Visualisera nätverket trafikmönster med öppen källkod verktyg och Azure Nätverksbevakaren | Microsoft Docs
description: Den här sidan beskrivs hur du använder Nätverksbevakaren paketinsamling med Capanalysis visualisera trafikmönster till och från dina virtuella datorer.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 7b1e1383e8e244a7cdb30be1e08514a6a4dd7b14
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302241"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualisera nätverket trafikmönster till och från dina virtuella datorer med hjälp av verktyg för öppen källkod

Paketet insamlingar innehålla nätverksdata så att du kan utföra nätverket dataforensik och djup paketinspektion. Det finns många öppnas källa verktyg som du kan använda för att analysera paket insamlingar för att få insikter om nätverket. Ett sådant verktyg är CapAnalysis, ett verktyg med öppen källkod paket avbilda visualiseringen. Visualisera avbilda paketdata är ett bra sätt att snabbt härleda insikter på trafikmönster och avvikelser i nätverket. Visualiseringar ger också möjlighet att dela sådana insikter på ett sätt som är lätta att använda.

Azures Nätverksbevakaren ger dig möjlighet att samla in data genom att du kan utföra paket insamlingar i nätverket. Den här artikeln innehåller en genomgång av hur du visualisera och få insikter från paketet fångar med Nätverksbevakaren CapAnalysis.

## <a name="scenario"></a>Scenario

Du har en enkel webbapp har distribuerats på en virtuell dator i Azure vill använda öppen källkod verktyg för att visualisera dess nätverkstrafik för att snabbt identifiera flödet mönster och alla möjliga inkonsekvenser. Med Nätverksbevakaren, kan du hämta en paketinsamling för din nätverksmiljö och spara dem direkt på ditt lagringskonto. CapAnalysis kan mata in paketinsamling direkt från storage-blob och visualisera dess innehåll.

![scenario][1]

## <a name="steps"></a>Steg

### <a name="install-capanalysis"></a>Installera CapAnalysis

Om du vill installera CapAnalysis på en virtuell dator som du kan referera till officiella anvisningarna här https://www.capanalysis.net/ca/how-to-install-capanalysis.
I ordning åt CapAnalysis, måste du öppna port 9877 på den virtuella datorn genom att lägga till en ny regel för inkommande säkerhetsregler. Mer information om hur du skapar regler i Nätverkssäkerhetsgrupper avser [skapa regler i en befintlig NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). När regeln har lagts ska du kunna komma åt CapAnalysis från `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Använda Azure Nätverksbevakaren för att starta en session för avbildningen av paketet

Nätverksbevakaren kan du avbilda paket för att spåra trafik till och från en virtuell dator. Du kan se anvisningarna i [hantera paket som samlar in med Nätverksbevakaren](network-watcher-packet-capture-manage-portal.md) starta en session för avbildningen av paketet. En paketinsamling kan lagras i ett blob storage som kan nås av CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Ladda upp en paketinsamling CapAnalysis
Du kan ladda upp en paketinsamling vidtagit nätverksbevakaren på fliken ”importera från URL”, och tillhandahålla en länk till blob storage där paketinsamling lagras direkt.

När du anger en länk till CapAnalysis, se till att lägga till en SAS-token till blob storage-URL.  För att göra detta, navigera till signatur för delad åtkomst från lagringskontot, ange tillåtna behörigheter och tryck på knappen Generera en SAS för att skapa ett token. Du kan sedan lägga till SAS-token till URL: en för paketet avbilda storage blob.

Resulterande URL-Adressen ser ut ungefär som följande URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>När paketet avbildas

CapAnalysis erbjuder olika alternativ för att visualisera dina paketinsamling, varje med analys från olika perspektiv. Med dessa visual sammanfattningar du förstå trenderna nätverket trafik och snabbt hitta några ovanliga aktiviteten. Några av dessa funktioner visas i listan nedan:

1. Flödet tabeller

    Den här tabellen innehåller listan över flöden i datapaketet, tidsstämpeln som är associerade med flödena och olika protokoll som är kopplade till flödet, samt källa och mål-IP.

    ![capanalysis flödar sida][5]

1. Översikt över Protocol

    Det här fönstret kan du snabbt se distributionen av nätverkstrafik via olika protokoll och geografiska områden.

    ![Översikt över capanalysis protocol][6]

1. Statistik

    Det här fönstret kan du visa trafik nätverksstatistik – byte skickas och tas emot från käll- och IP-adresser, flöden för varje käll- och IP-adresser, protokoll som används för olika flödena och varaktighet för flöden.

    ![capanalysis statistik][7]

1. geomap

    Det här fönstret ger dig en kartvyn av nätverkstrafiken, med färger skalning till mängden trafik från varje land. Du kan välja markerade länder att visa ytterligare Flödesstatistik som del av data som skickas och tas emot från IP-adresser i det landet.

    ![geomap][8]

1. Filter

    CapAnalysis innehåller en uppsättning filter för snabb analys av specifika paket. Exempelvis kan du filtrera informationen efter protokoll för att få specifik insikter som delmängd av trafik.

    ![filter][11]

    Besök [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) till mer information om alla CapAnalysis funktioner.

## <a name="conclusion"></a>Sammanfattning

Nätverket Watcher paket avbilda funktionen kan du samla in uppgifter som behövs för att utföra nätverket dataforensik och bättre förstå trafik på nätverket. I det här scenariot visade hur paket insamlingar från Nätverksbevakaren enkelt kan integreras med öppen källkod visualiseringsverktyg. Med öppen källkod, till exempel CapAnalysis visualisera insamlingar av paket kan du utföra djup paketinspektion och snabbt identifiera trender i nätverkstrafiken.

## <a name="next-steps"></a>Nästa steg

Mer information om flödet NSG-loggar finns [NSG flödet loggar](network-watcher-nsg-flow-logging-overview.md)

Lär dig hur du visualisera dina NSG flödet loggar med Power BI genom att besöka [visualisera NSG flödar loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
