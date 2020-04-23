---
title: Skapa incidenter från aviseringar i Azure Sentinel | Microsoft-dokument
description: Lär dig hur du skapar incidenter från aviseringar i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 1593b96ae8412632120e8977635a4193996ca88d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025126"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Skapa incidenter automatiskt från Microsofts säkerhetsvarningar

Aviseringar som utlöses i Microsofts säkerhetslösningar som är anslutna till Azure Sentinel, till exempel Microsoft Cloud App Security och Azure Advanced Threat Protection, skapar inte automatiskt incidenter i Azure Sentinel. När du ansluter en Microsoft-lösning till Azure Sentinel lagras som standard alla aviseringar som genereras i den tjänsten som rådata i Azure Sentinel i tabellen Säkerhetsavisering i din Azure Sentinel-arbetsyta. Du kan sedan använda dessa data som alla andra rådata som du ansluter till Sentinel.

Du kan enkelt konfigurera Azure Sentinel för att automatiskt skapa incidenter varje gång en avisering utlöses i en ansluten Microsoft-säkerhetslösning, genom att följa instruktionerna i den här artikeln.

## <a name="prerequisites"></a>Krav
Du måste [ansluta Microsofts säkerhetslösningar](connect-data-sources.md#data-connection-methods) för att aktivera incidentskapande från säkerhetstjänstaviseringar.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Använda analytiska regler för att skapa analytiska regler för Microsoft Security-incidenter

Använd de inbyggda reglerna som är tillgängliga i Azure Sentinel för att välja vilka anslutna Microsoft-säkerhetslösningar som ska skapa Azure Sentinel-incidenter automatiskt i realtid. Du kan också redigera reglerna för att definiera mer specifika alternativ för filtrering vilka av aviseringarna som genereras av Microsofts säkerhetslösning som ska skapa incidenter i Azure Sentinel. Du kan till exempel välja att skapa Azure Sentinel-incidenter automatiskt endast från Azure Security Center-aviseringar med hög allvarlighetsgrad.

1. Välj **Analytics**i Azure-portalen under Azure Sentinel .

1. Välj fliken **Regelmallar** om du vill visa alla inbyggda analytiska regler.

    ![Regelmallar](media/incidents-from-alerts/rule-templates.png)

1. Välj den regelmall för **Microsoft-säkerhetsanalys** som du vill använda och klicka på **Skapa regel**.

    ![Regel för säkerhetsanalys](media/incidents-from-alerts/security-analytics-rule.png)

1. Du kan ändra regelinformationen och välja att filtrera aviseringar som skapar incidenter efter allvarlighetsgrad för aviseringar eller text i aviseringens namn.  
      
    Om du till exempel väljer **Azure Security Center** i **Microsofts säkerhetstjänstfält** och väljer **Hög** i fältet Filter **efter allvarlighetsgrad,** skapas automatiskt incidenter i Azure Sentinel med hög allvarlighetsgrad.  

    ![Guiden Skapa regel](media/incidents-from-alerts/create-rule-wizard.png)

1. Du kan också skapa en ny **Microsoft-säkerhetsregel** som filtrerar aviseringar från olika Microsoft-säkerhetstjänster genom att klicka på **+Skapa** och välja **Microsoft-regel för att skapa incidenter**.

    ![Regel för skapande av incident](media/incidents-from-alerts/incident-creation-rule.png)

  Du kan skapa mer än en **Microsoft Security-analytisk** regel per **Microsoft-säkerhetstjänsttyp.** Detta skapar inte dubblettincidenter eftersom varje regel används som ett filter. Även om en avisering matchar mer än en **Microsoft Security-analytisk** regel skapas bara en Azure Sentinel-incident.

## <a name="enable-incident-generation-automatically-during-connection"></a>Aktivera incidentgenerering automatiskt under anslutningen
 När du ansluter en Microsoft-säkerhetslösning kan du välja om du vill att aviseringarna från säkerhetslösningen automatiskt ska generera incidenter i Azure Sentinel automatiskt.

1. Anslut en datakälla för Microsoft-säkerhetslösning. 

   ![Generera säkerhetsincidenter](media/incidents-from-alerts/generate-security-incidents.png)

1. Under **Skapa incidenter** väljer du **Aktivera** för att aktivera standardanalytiska regeln som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhetstjänsten. Du kan sedan redigera den här regeln under **Analytics** och sedan **aktiva regler**.

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).
- Lär dig hur du [lägger in dina data i Azure Sentinel](quickstart-onboard.md)och får insyn i dina data och [potentiella hot](quickstart-get-visibility.md).
