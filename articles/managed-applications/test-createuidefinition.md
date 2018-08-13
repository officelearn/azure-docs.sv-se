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
ms.date: 08/10/2018
ms.author: tomfitz
ms.openlocfilehash: 2c313538e297c5781b48fcfe9d0d5390f94c97f5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043970"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testa Azure portalgränssnitt för det hanterade programmet
Efter [skapar filen createUiDefinition.json](create-uidefinition-overview.md) för ditt Azure hanterade program, måste du testar användarupplevelsen. För att förenkla testning kan du använda ett skript som läser in filen i portalen. Du behöver inte distribuera det hanterade programmet.

## <a name="prerequisites"></a>Förutsättningar

* En **createUiDefinition.json** fil. Om du inte har den här filen kan kopiera den [exempelfilen](https://github.com/Azure/azure-quickstart-templates/blob/master/test/template-validation-tests/sample-template/createUIDefinition.json) och spara den lokalt.

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="download-test-script"></a>Ladda ned skriptet för enhetstest

Testa ditt gränssnitt i portalen genom att kopiera en av följande skript till den lokala datorn:

* [PowerShell-skript för separat inläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skriptexempel för Azure CLI-separat inläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Kör skript

Om du vill se filen gränssnitt i portalen, kör du det nedladdade skriptet. Skriptet skapar ett lagringskonto i Azure-prenumerationen och överför din createUiDefinition.json-fil till lagringskontot. Sedan öppnar portalen och läser in filen från lagringskontot.

Ange en plats för storage-kontot och ange den mapp som innehåller createUiDefinition.json-fil. Du behöver bara ange storage-konto plats först gången du kör skriptet eller om lagringskontot har tagits bort.

Om du använder PowerShell använder du:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory <path-to-folder-with-createuidefinition>
```

Om du använder Azure CLI använder du:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a <path-to-folder-with-createuidefinition>
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

## <a name="test-your-solution-files"></a>Testa din lösningsfiler

Nu när du har verifierat din portalgränssnitt fungerar som förväntat, är det dags att verifiera att filen createUiDefinition korrekt är integrerat med mainTemplate.json-fil. Du kan köra ett skript för verifiering test till test innehållet i din lösningsfiler, inklusive filen createUiDefinition. Skriptet validerar JSON-syntaxen, söker efter regex-uttryck på textfält och ser till utdata-värdena i portalgränssnittet matcha parametrarna i mallen. Information om hur du kör det här skriptet finns i [kör statiska verifieringskontroller för mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Nästa steg

När du har validerat din portalgränssnitt lär du dig om att din [Azure hanterade App som är tillgänglig i Marketplace](publish-marketplace-app.md).
