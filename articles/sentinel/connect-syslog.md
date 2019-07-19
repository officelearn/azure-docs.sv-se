---
title: Ansluta syslog-data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du ansluter syslog-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: fef9fa128d2ebb84fb82579f254735fdb9aa7ee2
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881073"
---
# <a name="connect-your-external-solution-using-syslog"></a>Anslut din externa lösning med syslog

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta alla lokala installationer som stöder Syslog till Azure Sentinel. Detta görs med hjälp av en agent som baseras på en Linux-dator mellan enheten och Azure Sentinel. Om Linux-datorn finns i Azure kan du strömma loggarna från din enhet eller ditt program till en dedikerad arbets yta som du skapar i Azure och ansluter den. Om Linux-datorn inte finns i Azure kan du strömma loggarna från din installation till en dedikerad lokal VM eller dator där du installerar agenten för Linux. 

> [!NOTE]
> Om din installation stöder Syslog-CEF är anslutningen mer fullständig och du bör välja det här alternativet och följa anvisningarna i att [ansluta data från CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Hur det fungerar

Syslog är ett protokoll för loggning av händelse som är gemensamma för Linux. Program skickar meddelanden som kan lagras på den lokala datorn eller levereras till en Syslog-insamlare. När Log Analytics-agenten för Linux installeras konfigureras den lokala syslog-daemonen för att vidarebefordra meddelanden till agenten. Agenten skickar sedan meddelandet till Azure Monitor där en motsvarande post skapas.

Mer information finns [i syslog-datakällor i Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> Agenten kan samla in loggar från flera källor, men måste installeras på en dedikerad proxyserver.

## <a name="connect-your-syslog-appliance"></a>Anslut syslog-enheten

1. I Azure Sentinel-portalen väljer du **data anslutningar** och väljer **syslog** -raden i tabellen och i fönstret syslog till höger klickar du på **Öppna kopplings sida**.
2. Om Linux-datorn finns i Azure väljer du **Hämta och installera agent på virtuell Azure Linux-dator**. I fönstret virtuella datorer väljer du de datorer som du vill installera agenten på och klickar på **Anslut** högst upp.
1. Om Linux-datorn inte är i Azure väljer du **Ladda ned och installera agent på Linux-datorer som inte är Azure-datorer**. I fönstret **Direct agent** kopierar du kommandot under **Ladda ned och onboarding agent for Linux** och kör det på datorn. 
1. Följ instruktionerna under **Konfigurera loggarna som ska anslutas** i installations fönstret för syslog Connector:
    1. Klicka på länken för att **Öppna konfigurationen av avancerade inställningar för arbets ytan**. 
    1. Välj **data**följt av **syslog**.
    1. I tabellen anger du sedan vilka funktioner du vill att syslog ska samla in. Du bör antingen lägga till eller välja de anläggningar som syslog-apparaten innehåller i sina logg rubriker. Du kan se den här konfigurationen i syslog-enheten i syslog-d i mappen:/etc/rsyslog.d/Security-config-omsagent.conf och i r-syslog under/etc/syslog-ng/Security-config-omsagent.conf. 
       > [!NOTE]
       > Om du markerar kryss rutan för att **använda konfigurationen nedan för mina datorer**, kommer den här konfigurationen att gälla för alla Linux-datorer som är anslutna till den här arbets ytan. Du kan se den här konfigurationen i syslog-datorn under 
1. Klicka på **Tryck här för att öppna konfigurations bladet**.
1. Välj **data** och sedan **syslog**.
   - Se till att varje funktion som du skickar av syslog finns i tabellen. Ange en allvarlighets grad för varje funktion som du ska övervaka. Klicka på **Verkställ**.
1. Kontrol lera att du har skickat dessa anläggningar på syslog-datorn. 

1. Om du vill använda det relevanta schemat i Log Analytics för syslog-loggarna söker du efter **syslog**.
1. Du kan använda funktionen Kusto som beskrivs i [använda funktioner i Azure Monitor logg frågor](../azure-monitor/log-query/functions.md) för att parsa syslog-meddelanden och sedan spara dem som en ny Log Analytics funktion och sedan använda funktionen som en ny datatyp.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter syslog-lokala enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
