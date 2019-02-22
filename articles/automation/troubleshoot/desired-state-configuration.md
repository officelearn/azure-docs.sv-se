---
title: Felsökning av problem med Azure Automation Desired State Configuration (DSC)
description: Den här artikeln innehåller information om hur du felsöker Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a34dea7e1eb53531db55dc62df8fbad8541f7a35
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586808"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Felsöka Desired State Configuration (DSC)

Den här artikeln innehåller information om felsökning av problem med Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Vanliga fel när du arbetar med Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Scenario: En konfiguration med specialtecken kan inte tas bort från portalen

#### <a name="issue"></a>Problem

När du försöker ta bort en DSC-konfiguration från portalen kan du se följande fel:

```
An error occured while deleteing the DSC configuration '<name>'.  Error-details: The arguement configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Orsak

Det här är ett tillfälligt fel som ska matchas.

#### <a name="resolution"></a>Lösning

* Använd cmdleten ”Remove-AzAutomationDscConfiguration” Az för att ta bort konfigurationen.
* I dokumentationen för denna cmdlet har inte uppdaterats ännu.  Fram till dess, finns i dokumentationen för AzureRM-modulen.
  * [Remove-AzureRmAutomationDSCConfiguration](https://docs.microsoft.com/en-us/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration?view=azurermps-6.13.0)

### <a name="failed-not-found"></a>Scenario: Noden är i ett misslyckat tillstånd med ett ”kunde inte hittas”-fel

#### <a name="issue"></a>Problem

Noden har en rapport med **misslyckades** status och som innehåller felet:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligen när noden har tilldelats ett namn (till exempel ABC) i stället för en nodkonfigurationsnamn (till exempel ABC. Webbserver).

#### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar nod med ”nodkonfigurationsnamn” och inte ”Konfigurationsnamnet”.
* Du kan tilldela en nodkonfiguration till en nod med hjälp av Azure portal eller med en PowerShell-cmdlet.

  * För att tilldela en nodkonfiguration till en nod med hjälp av Azure portal, öppna den **DSC-noder** , och sedan väljer en nod och klickar på **tilldela nodkonfiguration** knappen.  
  * Om du vill tilldela en nodkonfiguration till en nod med hjälp av PowerShell-cmdleten, använda **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>Scenario: Inga nodkonfigurationer (MOF-filer) skapas när en konfiguration kompileras

#### <a name="issue"></a>Problem

Dina DSC-Kompileringsjobb pausar med fel:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Orsak

När följande uttryck i **nod** nyckelord i DSC-konfigurationen utvärderas till `$null`, och inga nodkonfigurationer produceras.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar problemet på:

* Se till att uttrycket bredvid den **noden** nyckelord i konfigurationen definitionen utvärderas inte till $null.
* Om du skickar ConfigurationData vid kompilering konfigurationen, se till att du passerar de förväntade värdena som konfigurationen kräver från [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenario: DSC-nod-rapporten blir fastnat tillståndet ”pågår”

#### <a name="issue"></a>Problem

DSC-agenten utdata:

```
No instance found with given property values
```

#### <a name="cause"></a>Orsak

Du har uppgraderat din version av WMF och har skadad WMI.

#### <a name="resolution"></a>Lösning

Åtgärda problemet Följ instruktionerna i den [DSC kända problem och begränsningar](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artikeln.

### <a name="issue-using-credential"></a>Scenario: Det går inte att använda en autentiseringsuppgift i en DSC-konfiguration

#### <a name="issue"></a>Problem

Dina DSC-Kompileringsjobb pausades med fel:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Orsak

Du har använt en autentiseringsuppgift i en konfiguration, men inte anger rätt **ConfigurationData** att ställa in **PSDscAllowPlainTextPassword** till true för varje nodkonfiguration.

#### <a name="resolution"></a>Lösning

* Se till att skicka in rätt **ConfigurationData** att ställa in **PSDscAllowPlainTextPassword** till true för varje nodkonfiguration som nämns i konfigurationen. Mer information finns i [tillgångar i Azure Automation DSC](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
