---
title: Felsökning av problem med Azure Automation önskad tillstånd Configuration (DSC)
description: Den här artikeln innehåller information om hur du felsöker önskad tillstånd Configuration DSC)
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d2eae67fcff74a7016f7f6125e31a9c8c2bda97
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064126"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Felsöka Desired State Configuration DSC)

Den här artikeln innehåller information om felsökning av problem med önskad tillstånd Configuration DSC ().

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Vanliga fel när du arbetar med önskad tillstånd Configuration DSC)

### <a name="failed-not-found"></a>Scenario: Noden är statusen misslyckades med felet ”kunde inte hittas”

#### <a name="issue"></a>Problem

Noden har en rapport med **misslyckades** status och med felet:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligen när noden har tilldelats en Konfigurationsnamnet (till exempel ABC) i stället för ett namn på konfigurationsnod (till exempel ABC. Webbserver).

#### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar noden med ”nodkonfigurationsnamnet” och inte ”Konfigurationsnamnet”.
* Du kan tilldela en konfiguration av noden till en nod med hjälp av Azure-portalen eller med en PowerShell-cmdlet.

  * För att tilldela en konfiguration av noden till en nod med hjälp av Azure portal, öppna den **DSC-noder** och sedan markera en nod och klickar på på **tilldela nodkonfiguration** knappen.  
  * Använd om du vill tilldela en nod med hjälp av PowerShell-cmdlet för konfiguration av noden **Set AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>Scenario: Inga nodkonfigurationer (MOF-filer) skapas när en konfiguration kompileras

#### <a name="issue"></a>Problem

DSC-Kompileringsjobb pausar med fel:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Orsak

När följande uttryck i **nod** nyckelord i DSC-konfigurationen utvärderas till `$null`, och inga nodkonfigurationer produceras.

#### <a name="resolution"></a>Lösning

Något av följande lösningar problemet på:

* Se till att uttrycket bredvid den **nod** nyckelord i konfigurationsdefinition utvärderas inte till $null.
* Om du skickar ConfigurationData vid kompilering av konfigurationen, kontrollera att du skickar de förväntade värdena konfigurationen kräver från [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenario: Rapporten DSC-nod blir fastnat ”pågående”-tillstånd

#### <a name="issue"></a>Problem

DSC-agenten utdata:

```
No instance found with given property values
```

#### <a name="cause"></a>Orsak

Du har uppgraderat WMF-version och ha skadad WMI.

#### <a name="resolution"></a>Lösning

Åtgärda problemet Följ instruktionerna i den [DSC kända problem och begränsningar](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artikel.

### <a name="issue-using-credential"></a>Scenario: Det går inte att använda en autentiseringsuppgift i en DSC-konfiguration

#### <a name="issue"></a>Problem

DSC-Kompileringsjobb pausades med fel:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Orsak

Du har använt en autentiseringsuppgift i en konfiguration, men inte anger rätt **ConfigurationData** ange **PSDscAllowPlainTextPassword** till true för varje nodkonfiguration.

#### <a name="resolution"></a>Lösning

* Se till att skicka in rätt **ConfigurationData** ange **PSDscAllowPlainTextPassword** till true för varje nodkonfiguration som nämns i konfigurationen. Mer information finns i [tillgångar i Azure Automation DSC](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Nästa steg

Om du inte fick ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Du kan arkivera en incident i Azure-supporten om du behöver mer hjälp. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.