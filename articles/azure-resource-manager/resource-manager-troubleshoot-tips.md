---
title: "Förstå Azure distributionsfel | Microsoft Docs"
description: "Beskriver hur du lär dig mer om Azure distributionsfel."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "distributionsfel för azure-distribution distribuera till azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="understand-azure-deployment-errors"></a>Förstå Azure distributionsfel
Det här avsnittet beskriver distributionsfel och hur du kan hitta mer information om ett fel. Lösningar på vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="two-types-of-errors"></a>Två typer av fel
Det finns två typer av fel som du kan ta emot:

* Verifieringsfel
* Distributionsfel

Följande bild visar aktivitetsloggen för en prenumeration. Den representerar två distributioner. I en distribution av mallen gick inte att validera (**verifiera**) och fortsatte inte. I den andra distributionen mallen valideras men det gick inte att skapa resurser (**skriva distributioner**). 

![Visa felkod](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

Verifieringsfel uppstår scenarier som kan fastställas innan distribution. De omfattar syntaxfel i mallen, eller försök att distribuera resurser som skulle överskrida kvoter för din prenumeration. Distributionsfel uppkommer villkor som kan inträffa under distributionsprocessen. De omfattar försöker komma åt en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som används för att felsöka distributionen. Båda typerna av fel visas i den [aktivitetsloggen](resource-group-audit.md). Dock visas inte valideringsfel i distributionshistoriken eftersom distributionen har startat.

## <a name="determine-error-code"></a>Fastställa felkod:

Du kan lära dig om ett fel genom att titta på felmeddelandet och felkoden. Den [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md) artikeln visar lösningar av felkod. Det här avsnittet visar hur du använder Azure-portalen för att identifiera felkoden.

### <a name="validation-errors"></a>Verifieringsfel

När du distribuerar via portalen finns ett verifieringsfel efter att ha skickat värdena.

![Visa portal verifieringsfel](./media/resource-manager-troubleshoot-tips/validation-error.png)

Välj meddelandet för mer information. I följande bild visas en **InvalidTemplateDeployment** fel och ett meddelande som anger en princip blockeras distributionen.

![Visa valideringsinformation](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>Distributionsfel

När åtgärden har klarat valideringen men misslyckas under distributionen, visas fel i meddelanden. Markera meddelandet.

![meddelandet-fel](./media/resource-manager-troubleshoot-tips/notification.png)

Du kan visa mer information om hur du distribuerar. Välj alternativet för att få mer information om felet.

![distributionen misslyckades](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

Du ser felmeddelandet och felkoder. Observera att det finns två felkoder. Första felkoden (**DeploymentFailed**) är ett allmänt fel som inte innehåller de information du behöver för att lösa felet. Andra felkoden (**StorageAccountNotFound**) innehåller de information du behöver. 

![information om fel](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>Aktivera felsökningsloggning
Ibland behöver mer information om begäran och svar för att identifiera vad som gick fel. Du kan begära att ytterligare information loggas under en distribution med hjälp av PowerShell eller Azure CLI.

- PowerShell

   I PowerShell, ange den **DeploymentDebugLogLevel** parameter till alla, ResponseContent eller RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Kontrollera begäran innehåll med följande cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Eller innehåll med svaret:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Den här informationen kan hjälpa dig att avgöra om ett värde i mallen felaktigt anges.

- Azure CLI

   Undersök distributionsåtgärder med följande kommando:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Kapslad mall

   Logga felsökningsinformation för en kapslad mall med det **debugSetting** element.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>Skapa en mall för felsökning
I vissa fall är det enklaste sättet att felsöka mallen för att testa delar av den. Du kan skapa en förenklad mall som gör det möjligt att fokusera på den del som du tror orsakar felet. Anta att du får ett fel när du refererar till en resurs. Skapa en mall som returnerar den del som orsakar problemet i stället för hantering av en hel mall. Det kan hjälpa dig att avgöra om du skickar in rätt parametrar med hjälp av Mallfunktioner korrekt, och hämtar resursen du förväntar dig.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Eller anta att det uppstår distributionsfel som du tror är relaterade till felaktigt angiven beroenden. Testa din mall genom att dela upp den i förenklad mallar. Först skapa en mall som distribuerar en enskild resurs (till exempel en SQL Server). Lägga till en resurs som är beroende av den (till exempel en SQL-databas) när du är säker på att du har den här resursen korrekt definierad. Lägg till andra beroende resurser (till exempel granskningsprinciper) när du har de två resurser korrekt definierad. Ta bort resursgruppen för att se till att du testar rätt beroenden mellan varje test-distribution. 

## <a name="check-deployment-sequence"></a>Kontrollera distributionen sekvens

Många distributionsfel inträffa när resurser har distribuerats i ett oväntat sekvens. Dessa fel uppstår när beroenden inte är korrekt inställda. När du saknar ett beroende som krävs en resurs som försöker använda ett värde för en annan resurs, men den andra ännu finns inte. Du får ett felmeddelande om att en resurs inte hittas. Du kan stöta på den här typen av fel periodvis eftersom tidpunkten för distribution för varje resurs kan variera. Till exempel lyckas din första försöket att distribuera dina resurser eftersom en nödvändig resurs slumpmässigt har slutförts i tid. Däremot misslyckas din andra försöket eftersom den begärda resursen inte slutfördes i tid. 

Men om du vill undvika ange beroenden som inte behövs. När du har onödiga beroenden förlänga varaktigheten för distributionen genom att förhindra att resurser som inte är beroende av varandra från att distribueras parallellt. Dessutom kan du skapa Cirkelberoenden som blockerar distributionen. Den [referens](resource-group-template-functions-resource.md#reference) funktionen skapas en implicit beroende på den refererade resursen när resursen distribueras i samma mall. Du kan därför ha flera beroenden än beroenden i den **dependsOn** egenskapen. Den [resourceId](resource-group-template-functions-resource.md#resourceid) funktionen inte skapa en implicit beroende eller verifiera att resursen finns.

När du har beroende problem, som du behöver få insyn i ordningen för distribution av resursen. Visa ordningen på distributionsåtgärder:

1. Välj distributionshistoriken för resursgruppen.

   ![Välj distributionshistoriken](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. Välj en distribution från historiken och välj **händelser**.

   ![Välj distributionen händelser](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. Granska sekvens av händelser för varje resurs. Vara uppmärksam på status för varje åtgärd. Följande bild visar exempelvis tre storage-konton som har distribuerats parallellt. Observera att tre storage-konton har startats på samma gång.

   ![Parallell distribution](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   Nästa bild visar tre storage-konton som inte har distribuerats parallellt. Andra lagringskontot beror på det första storage-kontot och tredje lagringskontot är beroende av andra lagringskontot. Därför första lagringskontot startas, accepteras och slutförs innan nästa startas.

   ![sekventiella distribution](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

Du kan använda samma teknik även för mer komplicerade scenarier för att identifiera när distributionen är igång och klar för varje resurs. Titta igenom din distribution händelser att se om sekvensen är annorlunda än förväntat. I så fall, omvärdera beroenden för den här resursen.

Resource Manager identifierar cirkulärt tjänstberoende vid verifiering av mallen. Den returnerar ett felmeddelande som anger ett cirkelberoende finns. Du löser ett cirkulärt beroende:

1. Hitta den resurs som identifieras i cirkulärt beroende i mallen. 
2. Resursen måste undersöka den **dependsOn** egenskap och några användningsområden för den **referens** funktion för att se vilka resurser som den är beroende av. 
3. Granska dessa resurser om du vill se vilka resurser de beroende av. Följ beroenden tills du ser en resurs som är beroende av den ursprungliga resursen.
5. För resurserna som ingår i cirkulärt beroende noggrant undersöka all användning av den **dependsOn** egenskapen för att identifiera eventuella beroenden som inte behövs. Ta bort dessa beroenden. Om du inte vet att ett beroende behövs tar du bort den. 
6. Distribuera mallen.

Ta bort värden från den **dependsOn** egenskapen kan orsaka fel när du distribuerar mallen. Om det uppstår ett fel, lägger du till beroende tillbaka till mallen. 

Överväg att flytta en del av logik för distribution till underordnade resurser (till exempel tillägg eller konfigurationsinställningar) om den metoden inte löser cirkulärt beroende. Konfigurera de underordnade resurserna att distribuera efter resurser som är inblandade i cirkulärt beroende. Anta exempelvis att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. vm2
3. Tillägg på vm1 beror på vm1 och vm2. Tillägget anger värden för vm1 får från vm2.
4. Tillägg på vm2 beror på vm1 och vm2. Tillägget anger värden för vm2 får från vm1.

Samma metod fungerar för Apptjänst-appar. Överväg att flytta konfigurationsvärden till en underordnad resurs för app-resurs. Du kan distribuera två web apps i följande ordning:

1. webapp1
2. webapp2
3. konfigurationen för webapp1 beror på webapp1 och webapp2. Den innehåller app-inställningar med värden från webapp2.
4. konfigurationen för webapp2 beror på webapp1 och webapp2. Den innehåller app-inställningar med värden från webapp1.


## <a name="next-steps"></a>Nästa steg
* Lösningar på vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](resource-group-audit.md).
* Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](resource-manager-deployment-operations.md).
