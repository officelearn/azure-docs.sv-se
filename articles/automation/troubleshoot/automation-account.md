---
title: Felsöka Automation-konton
description: Läs om hur du felsöker och löser problem med ett Azure-konto.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679389"
---
# <a name="troubleshoot-the-automation-account"></a>Felsöka Automation-kontot

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

5. Om leverantören inte finns med i listan registrerar du den enligt beskrivningen i [Lös fel för registrering av resursprovider](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.