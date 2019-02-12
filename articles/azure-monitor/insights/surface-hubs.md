---
title: Övervaka Surface Hub med Azure med Azure Monitor | Microsoft Docs
description: Använda Surface Hub-lösning för att spåra hälsotillståndet för dina Surface Hub och förstå hur de används.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 902cf62e53581785caf2730f63af3633d8e1e498
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005445"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Övervaka Surface Hub med Azure Monitor för att spåra deras hälsa

![Surface Hub-symbol](./media/surface-hubs/surface-hub-symbol.png)

Den här artikeln beskrivs hur du kan använda Surface Hub-lösning i Azure Monitor för att övervaka Microsoft Surface Hub-enheter. Lösningen kan du spåra hälsotillståndet för dina Surface Hub samt förstå hur de används.

Varje Surface Hub har Microsoft Monitoring Agent installerad. Dess genom att du kan skicka data från Surface Hub till en Log Analytics-arbetsyta i Azure Monitor-agenten. Loggfiler läses från dina Surface Hub och kan sedan skickas till Azure Monitor. Problem som servrar är offline, kalendern inte synkroniserar, eller om enhetskontot inte kan logga in på Skype visas på instrumentpanelen för Surface Hub i Azure Monitor. Du kan identifiera enheter som inte körs eller som är har andra problem, och eventuellt tillämpa korrigeringar för upptäckta problem med hjälp av data i instrumentpanelen.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen. För att kunna hantera dina Surface Hub i Azure Monitor, behöver du följande:

* En [Log Analytics-prenumeration](https://azure.microsoft.com/pricing/details/log-analytics/) nivå som har stöd för antalet enheter som du vill övervaka. Log Analytics priset varierar beroende på hur många enheter har registrerats och hur mycket data det processer. Du vill ha i åtanke när du planerar distributionen Surface Hub.

Nu ska ska du lägga till en befintlig Log Analytics-arbetsyta eller skapa en ny. Detaljerade instruktioner för med någon av metoderna är på [skapa en Log Analytics-arbetsyta i Azure-portalen](../learn/quick-create-workspace.md). När du har konfigurerat Log Analytics-arbetsytan, finns det två sätt att registrera dina Surface Hub-enheter:

* Automatiskt via Intune
* Manuellt via **inställningar** på Surface Hub-enhet.

## <a name="set-up-monitoring"></a>Konfigurera övervakning
Du kan övervaka hälsotillståndet och aktiviteten hos dina Surface Hub med Azure Monitor. Du kan registrera Surface Hub med hjälp av Intune eller lokalt med hjälp av **inställningar** på Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Ansluta Surface Hub till Azure Monitor via Intune
Du behöver arbetsyte-ID och arbetsytenyckel för Log Analytics-arbetsytan som hanterar Surface hub. Du kan hämta dem från arbetsyteinställningar i Azure-portalen.

Intune är en Microsoftprodukt som låter dig centralt hantera konfigurationsinställningarna för Log Analytics-arbetsyta tillämpas på en eller flera av dina enheter. Följ dessa steg om du vill konfigurera dina enheter via Intune:

1. Logga in på Intune.
2. Gå till **inställningar** > **anslutna källor**.
3. Skapa eller redigera en princip som baseras på Surface Hub-mallen.
4. Gå till avsnittet Azure Operational Insights av principen och lägga till Log Analytics *arbetsyte-ID* och *Arbetsytenyckel* till principen.
5. Spara principen.
6. Associera principen med den aktuella gruppen med enheter.

   ![Intune-princip](./media/surface-hubs/intune.png)

Intune synkroniserar sedan inställningarna för Log Analytics med enheter i målgruppen, registrera dem i Log Analytics-arbetsytan.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Ansluta Surface Hub till Azure Monitor med appen inställningar
Du behöver arbetsyte-ID och arbetsytenyckel för Log Analytics-arbetsytan som hanterar Surface hub. Du kan hämta dem från inställningarna för Log Analytics-arbetsyta i Azure-portalen.

Om du inte använder Intune för att hantera din miljö kan du registrera enheter manuellt via **inställningar** på varje Surface Hub:

1. Öppna från dina Surface Hub **inställningar**.
2. Ange administratörsautentiseringsuppgifter för enheten när du tillfrågas.
3. Klicka på **enheten**, och under **övervakning**, klickar du på **konfigurera inställningar för Log Analytics**.
4. Välj **aktivera övervakning**.
5. I inställningsdialogrutan för Log Analytics, skriver du den Log Analytics **arbetsyte-ID** och Skriv den **Arbetsytenyckel**.  
   ![Inställningar](./media/surface-hubs/settings.png)
6. Klicka på **OK** att slutföra konfigurationen.

Om huruvida konfigurationen tillämpades på enheten visas en bekräftelse. Om den var, visas ett meddelande om att agenten har anslutits till Azure Monitor. Enheten börjar skicka data till Azure Monitor kan du visa och vidta åtgärder för den.

## <a name="monitor-surface-hubs"></a>Övervaka Surface hub
Övervakning av Surface Hub är med Azure Monitor mycket lik övervakning av andra registrerade enheter.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

När du klickar på panelen Surface Hub visas enhetens hälsotillstånd.

   ![Surface Hub-instrumentpanelen](./media/surface-hubs/surface-hub-dashboard.png)

Du kan skapa [aviseringar](../platform/alerts-overview.md) baserat på befintliga eller anpassade sökningar i loggen. Med hjälp av data som Azure Monitor samlar in från Surface Hub, kan du söka efter problem och avisering på de villkor som du definierar för dina enheter.

## <a name="next-steps"></a>Nästa steg
* Använd [logga frågor i Azure Monitor](../log-query/log-query-overview.md) att visa detaljerad Surface Hub-data.
* Skapa [aviseringar](../platform/alerts-overview.md) att meddela dig när problem uppstår med Surface hub.
