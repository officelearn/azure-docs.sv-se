---
title: "Övervaka Surface Hub med Azure Log Analytics | Microsoft Docs"
description: "Använda Surface Hub-lösning för att spåra hälsotillståndet för Surface Hub-enheter och förstå hur de används."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 525b3ced979834a956f91ef8c6f647b659ca21f1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Övervaka Surface Hub-enheter med logganalys att spåra deras hälsa

![Visa symbol för hubben](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Den här artikeln beskriver hur du kan använda Surface Hub-lösning i Log Analytics för att övervaka Microsoft Surface Hub-enheter. Logganalys hjälper dig att spåra hälsotillståndet för Surface Hub-enheter samt förstå hur de används.

Varje Surface Hub har Microsoft Monitoring Agent installerad. Dess genom att du kan skicka data från Surface Hub till logganalys agenten. Loggfiler läses från din Surface Hub-enheter och kan sedan skickas till logganalys. Frågor som är offline, servrar kalendern inte synkroniserar eller om enheten kontot är inte kan logga in Skype visas i instrumentpanelen i logganalys Surface Hub. Du kan identifiera enheter som inte körs eller som är andra problem som potentiellt gäller korrigeringar för de identifierade problem med hjälp av data på instrumentpanelen.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen. För att kunna hantera din Surface Hub-enheter i logganalys, behöver du följande:

* En [logganalys prenumeration](https://azure.microsoft.com/pricing/details/log-analytics/) nivå som stöder många enheter som du vill övervaka. Priser för log Analytics varierar beroende på hur många enheter registreras och hur mycket data den processer. Du vill ta hänsyn till detta när du planerar distributionen Surface Hub.

Därefter måste du antingen lägga till en befintlig logganalys-arbetsyta eller skapa en ny. Detaljerade instruktioner för att använda någon av metoderna är på [Kom igång med logganalys](log-analytics-get-started.md). När du har konfigurerat logganalys-arbetsytan finns det två sätt att registrera Surface Hub-enheter:

* Automatiskt via Intune
* Manuellt via **inställningar** på Surface Hub-enhet.

## <a name="set-up-monitoring"></a>Konfigurera övervakning
Du kan övervaka hälsotillståndet och aktiviteten hos din Surface Hub med logganalys. Du kan registrera Surface Hub med hjälp av Intune eller lokalt med hjälp av **inställningar** på Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Ansluta Surface Hub till logganalys via Intune
Du behöver arbetsyte-ID och arbetsytenyckel för logganalys-arbetsytan som hanterar Surface Hub-enheter. Du kan hämta dem från arbetsytan inställningar i Azure-portalen.

Intune är en Microsoft-produkt som hjälper dig att centralt hantera logganalys-konfigurationsinställningar som tillämpas på en eller flera av dina enheter. Följ dessa steg om du vill konfigurera dina enheter via Intune:

1. Logga in på Intune.
2. Gå till **inställningar** > **anslutna källor**.
3. Skapa eller redigera en princip som baseras på mallen Surface Hub.
4. Gå till avsnittet OMS (Azure-åtgärdsinformationen) av principen och lägga till Log Analytics *arbetsyte-ID* och *Arbetsytenyckel* till principen.
5. Spara principen.
6. Associera principen med den aktuella gruppen med enheter.

   ![Intune-princip](./media/log-analytics-surface-hubs/intune.png)

Intune synkroniserar sedan logganalys-inställningar med enheter i målgruppen registrera dem i logganalys-arbetsytan.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Ansluta Surface Hub-enheter till Log Analytics med hjälp av appen inställningar
Du behöver arbetsyte-ID och arbetsytenyckel för logganalys-arbetsytan som hanterar Surface Hub-enheter. Du kan hämta dem från inställningarna för logganalys-arbetsytan i Azure-portalen.

Om du inte använder Intune för att hantera din miljö, kan du registrera enheter manuellt via **inställningar** på varje Surface Hub:

1. Öppna från din Surface Hub **inställningar**.
2. Ange administratörsautentiseringsuppgifter enheten när du uppmanas.
3. Klicka på **enheten**, och under **övervakning**, klickar du på **konfigurera inställningarna för OMS**.
4. Välj **aktivera övervakning**.
5. Ange i dialogrutan OMS-inställningar för Log Analytics **arbetsyte-ID** och skriver den **Arbetsytenyckel**.  
   ![Inställningar](./media/log-analytics-surface-hubs/settings.png)
6. Klicka på **OK** för att slutföra konfigurationen.

Om huruvida konfigurationen tillämpades på enheten visas en bekräftelse. Om den har visas ett meddelande om att agenten har anslutits till logganalys. Enheten börjar skicka data till logganalys där du kan visa och arbeta med den.

## <a name="monitor-surface-hubs"></a>Övervakare för Surface hub
Övervaka Surface Hub-enheter är med hjälp av Log Analytics mycket lik övervakning av andra registrerade enheter.

1. Logga in på Azure Portal.
2. Navigera till logganalys-arbetsytan och markera **översikt**.
2. Klicka på panelen Surface Hub.
3. Enhetens hälsotillstånd visas.

   ![Ytan Hub-instrumentpanelen](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Du kan skapa [aviseringar](log-analytics-alerts.md) baserat på befintliga eller anpassade loggen sökningar. Med data logganalys samlar in från Surface Hub-enheter kan söka du efter problem och avisering på de villkor som du definierar för dina enheter.

## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) att visa detaljerad Surface Hub-data.
* Skapa [aviseringar](log-analytics-alerts.md) att meddela dig när problem uppstår med Surface Hub-enheter.
