---
title: Använd Azure Stream Analytics CI/CD NPM-paketet
description: Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD NPM-paket för att konfigurera en kontinuerlig integrering och distributions process.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173349"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Använd Stream Analytics CI/CD NPM-paketet
Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD NPM-paketet för att skapa en kontinuerlig integrering och distributions process.

## <a name="build-the-vs-code-project"></a>Bygg VS Code-projektet

Du kan aktivera kontinuerlig integrering och distribution för Azure Stream Analytics jobb med hjälp av NPM **-paketet ASA-streamanalytics-cicd** . NPM-paketet innehåller verktyg för att skapa Azure Resource Manager mallar för [Stream Analytics Visual Studio Code-projekt](quick-create-vs-code.md). Den kan användas i Windows, macOS och Linux utan att installera Visual Studio Code.

När du har [hämtat paketet](https://www.npmjs.com/package/azure-streamanalytics-cicd)använder du följande kommando för att skriva ut Azure Resource Manager-mallarna. Argumentet **scriptPath** är den absoluta sökvägen till **asaql** -filen i projektet. Kontrol lera att filerna asaproj. JSON och JobConfig. JSON finns i samma mapp som skript filen. Om **outputPath** inte har angetts placeras mallarna i mappen **Deploy** i mappen **bin** för projektet.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exempel (på macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

När ett Stream Analytics Visual Studio Code-projekt skapas, genererar det följande två Azure Resource Manager mallfiler under mappen **bin/[debug/återförsäljarversion]/Deploy** : 

*  Resource Manager-mallfil

       [ProjectName].JobTemplate.json 

*  Parameter fil för Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Standard parametrarna i filen Parameters. JSON är från inställningarna i ditt Visual Studio Code-projekt. Om du vill distribuera till en annan miljö ersätter du parametrarna på motsvarande sätt.

> [!NOTE]
> Standardvärdena anges till null för alla autentiseringsuppgifter. Du **måste** ange värdena innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Lär dig mer om hur du [distribuerar med en Resource Manager-mallfil och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Lär dig mer om hur du [använder ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Om du vill använda hanterad identitet för Azure Data Lake Store gen1 som utgående mottagare måste du ge åtkomst till tjänstens huvud namn med hjälp av PowerShell innan du distribuerar till Azure. Lär dig mer om hur du [distribuerar ADLS gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Azure Stream Analytics moln jobb i Visual Studio Code (för hands version)](quick-create-vs-code.md)
* [Testa Stream Analytics frågor lokalt med Visual Studio Code (för hands version)](vscode-local-run.md)
* [Utforska Azure Stream Analytics med Visual Studio Code (för hands version)](vscode-explore-jobs.md)
