---
title: Övervaka Surface Hubs med Azure Monitor | Microsoft-dokument
description: Använd Surface Hub-lösningen för att spåra hälsan hos dina Surface Hubs och förstå hur de används.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7999735bf2d182b2811d01172adcfc89cba27dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662509"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Övervaka Surface Hubs med Azure Monitor för att spåra deras hälsa

![Symbol för Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

I den här artikeln beskrivs hur du kan använda Surface Hub-lösningen i Azure Monitor för att övervaka Microsoft Surface Hub-enheter. Lösningen hjälper dig att spåra hälsan hos dina Surface Hubs samt förstå hur de används.

Varje Surface Hub har Microsoft Monitoring Agent installerat. Det är genom agenten som du kan skicka data från din Surface Hub till en Log Analytics-arbetsyta i Azure Monitor. Loggfiler läss från dina Surface Hubs och skickas sedan till Azure Monitor. Problem som servrar som är offline, kalendern synkroniseras inte eller om enhetskontot inte kan logga in på Skype visas på Surface Hub-instrumentpanelen i Azure Monitor. Genom att använda data på instrumentpanelen kan du identifiera enheter som inte körs eller som har andra problem och eventuellt använda korrigeringar för de upptäckta problemen.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen. För att hantera dina Surface Hubs i Azure Monitor behöver du följande:

* En [Log Analytics-prenumerationsnivå](https://azure.microsoft.com/pricing/details/log-analytics/) som stöder antalet enheter som du vill övervaka. Log Analytics-priserna varierar beroende på hur många enheter som registreras och hur mycket data den bearbetar. Du bör ta hänsyn till detta när du planerar distributionen av Surface Hub.

Därefter lägger du antingen till en befintlig Log Analytics-arbetsyta eller skapar en ny. Detaljerade instruktioner för att använda någon av metoderna finns på [Skapa en Log Analytics-arbetsyta i Azure-portalen](../learn/quick-create-workspace.md). När log analytics-arbetsytan har konfigurerats finns det två sätt att registrera dina Surface Hub-enheter:

* Automatiskt via Intune
* Manuellt via **Inställningar** på Surface Hub-enheten.

## <a name="set-up-monitoring"></a>Konfigurera övervakning
Du kan övervaka hälsotillståndet och aktiviteten för din Surface Hub med Azure Monitor. Du kan registrera Surface Hub med Hjälp av Intune eller lokalt genom att använda **Inställningar** på Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Ansluta Surface Hubs till Azure Monitor via Intune
Du behöver arbetsyte-ID och arbetsyta för arbetsytan Log Analytics som hanterar dina Surface Hubs. Du kan hämta dem från arbetsyteinställningarna i Azure-portalen.

Intune är en Microsoft-produkt som gör att du kan hantera konfigurationsinställningarna för Log Analytics-arbetsytan centralt och som tillämpas på en eller flera av dina enheter. Så här konfigurerar du dina enheter via Intune:

1. Logga in på Intune.
2. Navigera till **Inställningar** > **anslutna källor**.
3. Skapa eller redigera en princip baserat på Mallen För Surface Hub.
4. Navigera till avsnittet Azure Operational Insights i principen och lägg till *logganalysarbetsyte-ID* och *arbetsytanyckeln* i principen.
5. Spara principen.
6. Associera principen med lämplig grupp av enheter.

   ![Intune-princip](./media/surface-hubs/intune.png)

Intune synkroniserar sedan inställningarna för Logganalys med enheterna i målgruppen och registrerar dem på logganalysarbetsytan.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Ansluta Surface Hubs till Azure Monitor med appen Inställningar
Du behöver arbetsyte-ID och arbetsyta för arbetsytan Log Analytics som hanterar dina Surface Hubs. Du kan hämta dem från inställningarna för Log Analytics-arbetsytan i Azure-portalen.

Om du inte använder Intune för att hantera din miljö kan du registrera enheter manuellt via **Inställningar** på varje Surface Hub:

1. Öppna **Inställningar**från Surface Hub .
2. Ange autentiseringsuppgifterna för enhetsadministratörer när du uppmanas att göra det.
3. Klicka på **Den här enheten**och under **Övervakning**klickar du på **Konfigurera logganalysinställningar**.
4. Välj **Aktivera övervakning**.
5. Skriv **logganalysarbetsyta-ID:et** i dialogrutan Logganalysarbetsyta och skriv **arbetsytenyckeln**.  
   ![settings](./media/surface-hubs/settings.png)
6. Klicka på **OK** för att slutföra konfigurationen.

En bekräftelse visas som talar om för dig om konfigurationen har tillämpats på enheten eller inte. Om så var fallet visas ett meddelande om att agenten har anslutit till Azure Monitor. Enheten börjar sedan skicka data till Azure Monitor där du kan visa och agera på den.

## <a name="monitor-surface-hubs"></a>Övervaka Surface-hubbar
Att övervaka dina Surface Hubs med Azure Monitor är ungefär som att övervaka andra registrerade enheter.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

När du klickar på Surface Hub-panelen visas enhetens hälsa.

   ![Instrumentpanelen surface hub](./media/surface-hubs/surface-hub-dashboard.png)

Du kan skapa [aviseringar](../platform/alerts-overview.md) baserat på befintliga eller anpassade loggsökningar. Med hjälp av data som Azure Monitor samlar in från dina Surface Hubs kan du söka efter problem och avisera de villkor som du definierar för dina enheter.

## <a name="next-steps"></a>Nästa steg
* Använd [loggfrågor i Azure Monitor](../log-query/log-query-overview.md) för att visa detaljerade Surface Hub-data.
* Skapa [aviseringar](../platform/alerts-overview.md) för att meddela dig när problem uppstår med dina Surface Hubs.
