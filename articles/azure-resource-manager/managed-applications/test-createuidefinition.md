---
title: Testa ui-definitionsfilen
description: Beskriver hur du testar användarupplevelsen för att skapa ditt Azure Managed Application via portalen.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250184"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testa portalgränssnittet för Azure Managed Applications

När [du har skapat filen createUiDefinition.json](create-uidefinition-overview.md) för ditt hanterade program måste du testa användarupplevelsen. Om du vill förenkla testningen använder du en sandbox-miljö som läser in filen i portalen. Du behöver inte distribuera det hanterade programmet. Sandlådan visar ditt användargränssnitt i den aktuella helskärmsportalupplevelsen. Du kan också använda ett skript för att testa gränssnittet. Båda metoderna visas i den här artikeln. Sandlådan är det rekommenderade sättet att förhandsgranska gränssnittet.

## <a name="prerequisites"></a>Krav

* En **createUiDefinition.json-fil.** Om du inte har den här filen kopierar du [exempelfilen](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* En Azure-prenumeration. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="use-sandbox"></a>Använda sandlåda

1. Öppna [sandlådan Skapa användargränssnittsdefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Visa sandlåda](./media/test-createuidefinition/show-sandbox.png)

1. Ersätt den tomma definitionen med innehållet i filen createUiDefinition.json. Välj **Förhandsgranska**.

   ![Välj förhandsgranskning](./media/test-createuidefinition/select-preview.png)

1. Formuläret som du skapade visas. Du kan gå igenom användarupplevelsen och fylla i värdena.

   ![Visa formulär](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Felsökning

Om formuläret inte visas när du har valt **Förhandsgranska**kan du ha ett syntaxfel. Leta efter den röda indikatorn i den högra rullningslisten och navigera till den.

![Visa syntaxfel](./media/test-createuidefinition/show-syntax-error.png)

Om formuläret inte visas, och du i stället ser en ikon i ett moln med tårdroppe, har formuläret ett fel, till exempel en egenskap som saknas. Öppna webbutvecklarverktygen i webbläsaren. **Konsolen** visar viktiga meddelanden om ditt gränssnitt.

![Visa fel](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Använda testskript

Om du vill testa gränssnittet i portalen kopierar du något av följande skript till den lokala datorn:

* [PowerShell-skript för sidoinläsning - Az-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell-skript för sidoinläsning - Azure-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-skript för sidoinläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Om du vill visa gränssnittsfilen i portalen kör du det nedladdade skriptet. Skriptet skapar ett lagringskonto i din Azure-prenumeration och överför filen createUiDefinition.json till lagringskontot. Lagringskontot skapas första gången du kör skriptet eller om lagringskontot har tagits bort. Om lagringskontot redan finns i din Azure-prenumeration återanvänds det genom skriptet. Skriptet öppnar portalen och läser in filen från lagringskontot.

Ange en plats för lagringskontot och ange mappen som har filen createUiDefinition.json.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Om du använder Azure CLI använder du:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Om filen createUiDefinition.json finns i samma mapp som skriptet och du redan har skapat lagringskontot behöver du inte ange dessa parametrar.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Om du använder Azure CLI använder du:

```bash
./sideload-createuidef.sh
```

Skriptet öppnar en ny flik i webbläsaren. Den visar portalen med ditt gränssnitt för att skapa det hanterade programmet.

Ange värden för fälten. När du är klar visas de värden som skickas till mallen som finns i webbläsarens utvecklarverktygskonsol.

![Visa värden](./media/test-createuidefinition/show-json.png)

Du kan använda dessa värden som parameterfil för att testa distributionsmallen.

Om portalen hänger sig på sammanfattningsskärmen kan det finnas ett fel i utdataavsnittet. Du kan till exempel ha refererat till en kontroll som inte finns. Om en parameter i utdata är tom kan parametern referera till en egenskap som inte finns. Referensen till kontrollen är till exempel giltig, men egenskapsreferensen är ogiltig.

## <a name="test-your-solution-files"></a>Testa dina lösningsfiler

Nu när du har verifierat att portalgränssnittet fungerar som förväntat är det dags att verifiera att filen createUiDefinition är korrekt integrerad med filen mainTemplate.json. Du kan köra ett valideringsskripttest för att testa innehållet i dina lösningsfiler, inklusive filen createUiDefinition. Skriptet validerar JSON-syntaxen, söker efter regex-uttryck i textfält och ser till att utdatavärdena för portalgränssnittet matchar parametrarna för mallen. Information om hur du kör skriptet finns i [Köra statiska valideringskontroller för mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Nästa steg

När du har validerat portalgränssnittet kan du läsa om hur du gör ditt [Azure-hanterade program tillgängligt på Marketplace](publish-marketplace-app.md).
