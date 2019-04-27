---
title: Testa Användargränssnittet definitionen för Azure Managed Applications | Microsoft Docs
description: Beskriver hur du testar användarupplevelsen för att skapa din Azure-hanterade programmet via portalen.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: b1392c29881a9077e26baafc8972148800d03d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746331"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testa Azure portalgränssnitt för det hanterade programmet
Efter [skapar filen createUiDefinition.json](create-uidefinition-overview.md) för ditt Azure hanterade program, måste du testar användarupplevelsen. För att förenkla testning kan du använda ett skript som läser in filen i portalen. Du behöver inte distribuera det hanterade programmet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En **createUiDefinition.json** fil. Om du inte har den här filen kan kopiera den [exempelfilen](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json) och spara den lokalt.

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="download-test-script"></a>Ladda ned skriptet för enhetstest

Testa ditt gränssnitt i portalen genom att kopiera en av följande skript till den lokala datorn:

* [PowerShell-skript för separat inläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skriptexempel för Azure CLI-separat inläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Kör skript

Om du vill se filen gränssnitt i portalen, kör du det nedladdade skriptet. Skriptet skapar ett lagringskonto i Azure-prenumerationen och överför din createUiDefinition.json-fil till lagringskontot. Storage-konto skapas första gången du kör skriptet eller om lagringskontot har tagits bort. Om lagringskontot finns redan i din Azure-prenumeration, återanvänds det av skriptet. Skriptet öppnas portalen och läser in filen från lagringskontot.

Ange en plats för storage-kontot och ange den mapp som innehåller createUiDefinition.json-fil.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Om du använder Azure CLI använder du:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Om du redan har skapat lagringskontot createUiDefinition.json filen är i samma mapp som skriptet, behöver du inte ange dessa parametrar.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Om du använder Azure CLI använder du:

```azurecli
./sideload-createuidef.sh
```

## <a name="test-your-interface"></a>Testa ditt gränssnitt

Skriptet öppnas en ny flik i webbläsaren. Visas på portalen med dina gränssnittet för att skapa det hanterade programmet.

![Visa portal](./media/test-createuidefinition/view-portal.png)

Innan du fyller i fälten, öppna Web Developer Tools i webbläsaren. Den **konsolen** visar viktiga meddelanden om gränssnittet.

![Välj konsolen](./media/test-createuidefinition/select-console.png)

Om din gränssnittsdefinitionen innehåller ett fel kan se du beskrivningen i konsolen.

![Visa fel](./media/test-createuidefinition/show-error.png)

Ange värden för fälten. När du är klar kan du se de värden som skickas till mallen.

![Visa värden](./media/test-createuidefinition/show-json.png)

Du kan använda dessa värden som parameterfilen för att testa din Distributionsmall.

## <a name="troubleshooting-the-interface"></a>Felsökning av gränssnittet

Några vanliga fel som visas är:

* Portalen inte att läsa in ditt gränssnitt. I stället visas en ikon för ett moln med tear släpp. Den här ikonen visas vanligtvis när det finns ett syntaxfel i filen. Öppna filen i VS Code (eller andra JSON-redigerare som har schemavalideringen) och leta efter syntaxfel.

* Det låser sig i fönstret Sammanfattning på portalen. Den här avbrott sker vanligtvis när det finns en bugg i utdata. Du kan exempelvis ha refererade en kontroll som inte finns.

* En parameter i utdata är tom. Parametern kan referera till en egenskap som inte finns. Till exempel referensen till kontrollen är giltig, men Egenskapsreferensen är inte giltig.

## <a name="test-your-solution-files"></a>Testa din lösningsfiler

Nu när du har verifierat din portalgränssnitt fungerar som förväntat, är det dags att verifiera att filen createUiDefinition korrekt är integrerat med mainTemplate.json-fil. Du kan köra ett skript för verifiering test till test innehållet i din lösningsfiler, inklusive filen createUiDefinition. Skriptet validerar JSON-syntaxen, söker efter regex-uttryck på textfält och ser till utdata-värdena i portalgränssnittet matcha parametrarna i mallen. Information om hur du kör det här skriptet finns i [kör statiska verifieringskontroller för mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Nästa steg

När du har validerat din portalgränssnitt lär du dig om att din [Azure hanterade App som är tillgänglig i Marketplace](publish-marketplace-app.md).
