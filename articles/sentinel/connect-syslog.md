---
title: Samla in Syslog-data i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Lär dig mer om att samla in Syslog-data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55949da97f58f1d8c1670f69d25e92d6bb4e9eef
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447310"
---
# <a name="connect-your-external-solution-using-syslog"></a>Ansluta din externa lösning med hjälp av Syslog

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta alla lokala installation som stöder Syslog till Sentinel-Azure. Detta görs med hjälp av en agent som baseras på en Linux-dator mellan enheten och Azure Sentinel. Om din Linux-dator i Azure kan strömma du loggar från din enheten eller programmet till en dedikerad arbetsyta som du skapar i Azure och ansluter den. Om din Linux-dator inte finns i Azure kan du strömma loggar från din installation till en dedikerad i lokala virtuella datorn eller datorn där du installerar agenten för Linux. 

> [!NOTE]
> Om din installation stöder Syslog CEF, anslutningen är fullständig och du bör välja det här alternativet och följ instruktionerna i [samla in data från CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Hur det fungerar

Sysloginsamling åstadkoms med hjälp av en agent för Linux. Som standard av agenten för Linux tar emot händelser från Syslog-daemon över UDP, men i fall där en Linux-dator förväntas samla in en stor mängd Syslog-händelser, t.ex. när en Linux-agenten tar emot händelser från andra enheter, konfigurationen har ändrats till Använd TCP-transport mellan Syslog-daemon och agenten.

## <a name="connect-your-syslog-appliance"></a>Ansluta din Syslog-installation

1. I Sentinel-Azure-portalen väljer **datainsamling** och välj den **Syslog** panelen.
2. Om din Linux-dator inte är i Azure, hämta och installera Azure Sentinel **agenten för Linux** på din installation. 
1. Om du arbetar i Azure, Välj eller skapa en virtuell dator som i Azure Sentinel arbetsyta som är dedikerad för att ta emot Syslog-meddelanden. Välj den virtuella datorn i Azure Sentinel-arbetsytor och klicka på **Connect** överst i den vänstra rutan.
3. Klicka på **konfigurera loggarna som ska samlas in** i den Syslog connector-konfigurationen. 
4. Klicka på **Tryck här för att öppna bladet**.
1. Välj **Data** och sedan **Syslog**.
   - Kontrollera att varje resurs som du skickar av Syslog finns i tabellen. För varje funktion ska du övervaka, ange en allvarlighetsgrad. Klicka på **Verkställ**.
1. Kontrollera att du skickar dem i din Syslog-dator. 

3. Om du vill använda relevanta schemat i Log Analytics för Syslog-loggar, Sök efter **Syslog**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Syslog lokala installationer till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
