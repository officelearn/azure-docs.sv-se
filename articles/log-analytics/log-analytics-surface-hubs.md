---
title: "Övervaka Surface Hub med Azure Log Analytics | Microsoft Docs"
description: "Använda Surface Hub-lösning för att spåra hälsotillståndet för Surface Hub-enheter och förstå hur de används."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6ecd0d09589fec85c1633f528afc1165c346b7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Övervaka Surface Hub-enheter med logganalys att spåra deras hälsa

![Visa symbol för hubben](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Den här artikeln beskriver hur du kan använda Surface Hub-lösning i Log Analytics för att övervaka Microsoft Surface Hub-enheter med Microsoft Operations Management Suite (OMS). Logganalys hjälper dig att spåra hälsotillståndet för Surface Hub-enheter samt förstå hur de används.

Varje Surface Hub har Microsoft Monitoring Agent installerad. Dess via agenten att du kan skicka data från Surface Hub till OMS. Loggfiler läses från din Surface Hub-enheter och kan sedan skickas till OMS-tjänsten. Frågor som är offline, servrar kalendern inte synkroniserar eller om enheten kontot är inte kan logga in Skype visas i OMS Surface Hub-instrumentpanelen. Du kan identifiera enheter som inte körs eller som är andra problem som potentiellt gäller korrigeringar för de identifierade problem med hjälp av data på instrumentpanelen.

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen. För att kunna hantera Surface Hub-enheter från Microsoft Operations Management Suite (OMS), behöver du följande:

* En giltig prenumeration för att [OMS](http://www.microsoft.com/oms).
* En [OMS prenumeration](https://azure.microsoft.com/pricing/details/log-analytics/) nivå som stöder många enheter som du vill övervaka. Priser för OMS varierar beroende på hur många enheter registreras och hur mycket data den processer. Du vill ta hänsyn till detta när du planerar distributionen Surface Hub.

Därefter måste du antingen lägga till en OMS-prenumeration i din befintliga prenumeration på Microsoft Azure eller skapa en ny arbetsyta direkt via OMS-portalen. Detaljerade instruktioner för att använda någon av metoderna är på [Kom igång med logganalys](log-analytics-get-started.md). När OMS-prenumerationen har ställts in, finns det två sätt att registrera Surface Hub-enheter:

* Automatiskt via Intune
* Manuellt via **inställningar** på Surface Hub-enhet.

## <a name="set-up-monitoring"></a>Konfigurera övervakning
Du kan övervaka hälsotillståndet och aktiviteten hos din Surface Hub med logganalys i OMS. Du kan registrera den Surface Hub i OMS med hjälp av Intune eller lokalt med hjälp av **inställningar** på Surface Hub.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Ansluta Surface Hub till OMS via Intune
Du behöver arbetsyte-ID och arbetsytenyckel för OMS-arbetsyta som ska hantera Surface Hub-enheter. Du kan hämta dem från OMS-portalen.

Intune är en Microsoft-produkt som hjälper dig att centralt hantera OMS-konfigurationsinställningar som tillämpas på en eller flera av dina enheter. Följ dessa steg om du vill konfigurera dina enheter via Intune:

1. Logga in på Intune.
2. Gå till **inställningar** > **anslutna källor**.
3. Skapa eller redigera en princip som baseras på mallen Surface Hub.
4. Gå till avsnittet OMS (Azure-åtgärdsinformationen) av principen och lägga till den *arbetsyte-ID* och *Arbetsytenyckel* till principen.
5. Spara principen.
6. Associera principen med den aktuella gruppen med enheter.

   ![Intune-princip](./media/log-analytics-surface-hubs/intune.png)

Intune synkroniserar sedan OMS-inställningar med enheter i målgruppen registrera dem i OMS-arbetsyta.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Ansluta Surface Hub-enheter till OMS med hjälp av appen inställningar
Du behöver arbetsyte-ID och arbetsytenyckel för OMS-arbetsyta som ska hantera Surface Hub-enheter. Du kan hämta dem från OMS-portalen.

Om du inte använder Intune för att hantera din miljö, kan du registrera enheter manuellt via **inställningar** på varje Surface Hub:

1. Öppna från din Surface Hub **inställningar**.
2. Ange administratörsautentiseringsuppgifter enheten när du uppmanas.
3. Klicka på **enheten**, och under **övervakning**, klickar du på **konfigurera inställningarna för OMS**.
4. Välj **aktivera övervakning**.
5. Ange i dialogrutan OMS-inställningar för den **arbetsyte-ID** och skriver den **Arbetsytenyckel**.  
   ![inställningar](./media/log-analytics-surface-hubs/settings.png)
6. Klicka på **OK** för att slutföra konfigurationen.

Om huruvida OMS-konfigurationen har tillämpats på enheten visas en bekräftelse. Om den har visas ett meddelande om att agenten har anslutits till OMS-tjänsten. Enheten börjar skicka data till OMS där du kan visa och arbeta med den.

## <a name="monitor-surface-hubs"></a>Övervakare för Surface hub
Övervaka Surface Hub-enheter påminner med hjälp av OMS övervakning av andra registrerade enheter.

1. Logga in på OMS-portalen.
2. Gå till instrumentpanelen pack Surface Hub-lösning.
3. Enhetens hälsotillstånd visas.

   ![Ytan Hub-instrumentpanelen](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Du kan skapa [aviseringar](log-analytics-alerts.md) baserat på befintliga eller anpassade loggen sökningar. Med data i OMS samlar in från Surface Hub-enheter kan söka du efter problem och avisering på de villkor som du definierar för dina enheter.

## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) att visa detaljerad Surface Hub-data.
* Skapa [aviseringar](log-analytics-alerts.md) att meddela dig när problem uppstår med Surface Hub-enheter.
