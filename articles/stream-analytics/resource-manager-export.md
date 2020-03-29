---
title: Exportera en Azure Stream Analytics-jobb azure Resource Manager-mall
description: I den här artikeln beskrivs hur du exporterar en Azure Resource Manager-mall för ditt Azure Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968948"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exportera en Azure Stream Analytics-jobb azure Resource Manager-mall

[Med Azure Resource Manager-mallar](../azure-resource-manager/templates/overview.md) kan du implementera infrastruktur som kod. Mallen är en JSON-fil (JavaScript Object Notation) som definierar infrastrukturen och konfigurationen för dina resurser. Du anger vilka resurser som ska distribueras och egenskaperna för dessa resurser.

Du kan distribuera om ett Azure Stream Analytics-jobb genom att exportera Azure Resource Manager-mallen.

## <a name="open-a-job-in-vs-code"></a>Öppna ett jobb i VS-kod

Innan du kan exportera en mall måste du först öppna ett befintligt Stream Analytics-jobb i Visual Studio-kod. 

Om du vill exportera ett jobb till ett lokalt projekt letar du reda på det jobb som du vill exportera i **Stream Analytics Explorer** i Azure-portalen. Välj Öppna i **Visual Studio**på sidan **Fråga** . Välj sedan **Visual Studio-kod**.

![Öppna Stream Analytics-jobb i Visual Studio-kod](./media/resource-manager-export/open-job-vs-code.png)

Mer information om hur du använder Visual Studio-kod för att hantera Stream Analytics-jobb finns i [snabbstarten för Visual Studio-kod](quick-create-vs-code.md).

## <a name="compile-the-script"></a>Kompilera skriptet 

Nästa steg är att kompilera jobbskriptet till en Azure Resource Manager-mall. Innan du kompilerar skriptet ska du se till att jobbet har minst en indata och en utdata konfigurerad. Om ingen indata eller utdata är konfigurerad måste du konfigurera indata och utdata först.

1. I Visual Studio-kod navigerar du till jobbets *Transformation.asaql-fil.*

   ![Transformation.asaql-fil i Visual Studio-kod](./media/resource-manager-export/transformation-asaql.png)

1. Högerklicka på filen *Transformation.asaql* och välj **ASA: Kompilera skript** på menyn.

1. Observera att en **distributionsmapp** visas på jobbarbetsytan stream analytics.

1. Utforska *filen JobTemplate.json,* som är azure Resource Management-mallen som används för distribution.

## <a name="complete-the-parameters-file"></a>Slutför parameterfilen

Slutför sedan mallparametrarna för Azure Resource Management.

1. Öppna filen *JobTemplate.parameters.json* som finns i mappen **Distribuera** på jobbarbetsytan Stream Analytics i Visual Studio-kod.

1. Observera att in- och utdatanycklarna är null. Ersätt null-värdena med de faktiska åtkomstnycklarna för in- och utdataresurserna.

1. Spara parameterfilen.

## <a name="deploy-using-templates"></a>Distribuera med mallar

Du är redo att distribuera ditt Azure Stream Analytics-jobb med de Azure Resource Manager-mallar som du genererade i föregående avsnitt.

Kör följande kommando i ett PowerShell-fönster. Var noga med att skörda *ResourceGroupName-*, *TemplateFile*och *TemplateParameterFile* med ditt faktiska resursgruppsnamn och de fullständiga filsökvägarna till *JobTemplate.json-* och *JobTemplate.parameters.json-filerna* i **distributionsmappen** för jobbarbetsytan.

Om du inte har Azure PowerShell konfigurerat följer du stegen i [Installera Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Nästa steg

* [Testa Azure Stream Analytics-jobb lokalt med liveindata med Visual Studio-kod](visual-studio-code-local-run-live-input.md)

* [Utforska Azure Stream Analytics-jobb med Visual Studio-kod (förhandsversion)](visual-studio-code-explore-jobs.md)