---
title: Felsöka Automation-konton
description: Lär dig hur du felsöker och löser problem med ett Azure-konto.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679389"
---
# <a name="troubleshoot-the-automation-account"></a>Felsöka Automation-kontot

I den här artikeln beskrivs lösningar på problem som kan uppstå när du använder ett Automation-konto. I följande avsnitt beskrivs vissa fel meddelanden och möjliga lösningar för var och en. Allmän information om Automation-konton finns i [skapa ett Azure-konto](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: det går inte att registrera Automation Resource Provider för prenumerationer

### <a name="issue"></a>Problem

När du arbetar med hanterings lösningar i ditt Automation-konto uppstår följande fel:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Orsak

Den automatiska resurs leverantören är inte registrerad i prenumerationen.

### <a name="resolution"></a>Lösning

Registrera automatiserings resurs leverantören genom att följa de här stegen i Azure Portal:

1. Gå till [Azure Portal](https://portal.azure.com)i webbläsaren.

2. Gå till **prenumerationer** och välj din prenumeration på sidan prenumerationer.   

3. Under **Inställningar**väljer du **resurs leverantörer**.

4. Kontrol lera att **Microsoft. Automation** Resource Provider är registrerad i listan över resurs leverantörer.

5. Om providern inte finns med i listan registrerar du den enligt beskrivningen i [lösa fel för registrering av resurs leverantör](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport), det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.