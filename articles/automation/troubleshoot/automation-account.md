---
title: Felsökning av automatiseringskonto
description: Läs om hur du felsöker och löser problem med ett Azure-konto.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301010"
---
# <a name="automation-account-troubleshooting"></a>Felsökning av automatiseringskonto

I den här artikeln beskrivs lösningar på problem som kan uppstå när du använder ett Automation-konto. I följande avsnitt markeras specifika felmeddelanden och möjliga lösningar för varje. Allmän information om Automation-konton finns i [Skapa ett Azure-konto](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: Det går inte att registrera Automation Resource Provider för prenumerationer

### <a name="issue"></a>Problem

När du arbetar med hanteringslösningar i ditt Automation-konto uppstår följande fel:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Orsak

Automation Resource Provider är inte registrerad i prenumerationen.

### <a name="resolution"></a>Lösning

Så här registrerar du Automation Resource Provider i Azure-portalen:

1. Gå till [Azure-portalen](https://portal.azure.com)från webbläsaren .

2. Navigera till **Prenumerationer** och välj din prenumeration på sidan Prenumerationer.   

3. Under **Inställningar**väljer du **Resursleverantörer**.

4. Kontrollera att **Microsoft.Automation-resursleverantören** är registrerad i listan över resursleverantörer.

5. Om det inte finns med i listan registrerar du **Microsoft.Automation-providern** genom att följa stegen vid [Lös fel för registrering av resursleverantörer](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Nästa steg

Om du inte har sett problemet eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.