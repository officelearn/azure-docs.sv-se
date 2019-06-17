---
title: Testa Användargränssnittet definitionen för Azure Managed Applications | Microsoft Docs
description: Beskriver hur du testar användarupplevelsen för att skapa din Azure-hanterade programmet via portalen.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257615"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testa din portalgränssnitt för Azure Managed Applications

Efter [skapar filen createUiDefinition.json](create-uidefinition-overview.md) för det hanterade programmet behöver du testa användarupplevelsen. Använd en testmiljö som läser in filen i portalen för att förenkla testning. Du behöver inte distribuera det hanterade programmet. Sandbox-miljön anger användargränssnittet i den aktuella, helskärmsläge portalmiljön. Eller du kan använda ett PowerShell-skript för testning gränssnittet, men den använder en äldre vy i portalen. Båda metoderna som visas i den här artikeln. Sandbox-miljön är det rekommenderade sättet att förhandsgranska gränssnittet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En **createUiDefinition.json** fil. Om du inte har den här filen kan kopiera den [exempelfilen](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="use-sandbox"></a>Använd begränsat läge

1. Öppna den [skapa Användargränssnittet Definition Sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Visa sandbox-miljön](./media/test-createuidefinition/show-sandbox.png)

1. Ersätt definitionen för tom med innehållet i filen createUiDefinition.json. Välj **förhandsversion**.

   ![Välj förhandsgranskning](./media/test-createuidefinition/select-preview.png)

1. Formuläret som du har skapat visas. Du kan gå igenom användarupplevelsen och Fyll i värden.

   ![Visa formulär](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Felsökning

Om ditt formulär inte visas när du har valt **förhandsversion**, du kan ha ett syntaxfel. Leta efter den röda indikatorn på den högra rullningslisten och navigera till den.

![Visa syntaxfel](./media/test-createuidefinition/show-syntax-error.png)

Om formuläret visar inte och i stället visas en ikon för ett moln med tear släpp, innehåller formuläret ett fel, till exempel en egenskap som saknas. Öppna Web Developer Tools i webbläsaren. Den **konsolen** visar viktiga meddelanden om gränssnittet.

![Visa fel](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Använd skript för testning

Testa ditt gränssnitt i portalen genom att kopiera en av följande skript till den lokala datorn:

* [PowerShell-skript för separat inläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skriptexempel för Azure CLI-separat inläsning](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

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

Skriptet öppnas en ny flik i webbläsaren. Visas på portalen med dina gränssnittet för att skapa det hanterade programmet.

![Visa portal](./media/test-createuidefinition/view-portal.png)

Ange värden för fälten. När du är klar kan du se de värden som skickas till mallen.

![Visa värden](./media/test-createuidefinition/show-json.png)

Du kan använda dessa värden som parameterfilen för att testa din Distributionsmall.

Om portalen låser sig i fönstret Sammanfattning, kan det finnas en bugg i utdata. Du kan exempelvis ha refererade en kontroll som inte finns. Om en parameter i utdata är tom parametern vara refererar till en egenskap som inte finns. Till exempel referensen till kontrollen är giltig, men Egenskapsreferensen är inte giltig.

## <a name="test-your-solution-files"></a>Testa din lösningsfiler

Nu när du har verifierat din portalgränssnitt fungerar som förväntat, är det dags att verifiera att filen createUiDefinition korrekt är integrerat med mainTemplate.json-fil. Du kan köra ett skript för verifiering test till test innehållet i din lösningsfiler, inklusive filen createUiDefinition. Skriptet validerar JSON-syntaxen, söker efter regex-uttryck på textfält och ser till utdata-värdena i portalgränssnittet matcha parametrarna i mallen. Information om hur du kör det här skriptet finns i [kör statiska verifieringskontroller för mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Nästa steg

När du har validerat din portalgränssnitt lär du dig om att din [Azure hanterade App som är tillgänglig i Marketplace](publish-marketplace-app.md).
