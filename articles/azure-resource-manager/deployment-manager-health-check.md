---
title: Introducera hälsotillstånd integration distribution till Azure Deployment Manager
description: Beskriver hur du distribuerar en tjänst via många regioner med Azure Deployment Manager. Den visar säker distribution metoder för att kontrollera stabiliteten för distributionen innan du distribuerar till alla regioner.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 1006cc902cf4b6f763d86165a039a1fec5dc97a1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467090"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introducera hälsotillstånd integration distribution till Azure Deployment Manager (förhandsversion)

[Azure Deployment Manager](./deployment-manager-overview.md) kan du utföra en mellanlagrad distributioner av Azure Resource Manager-resurser. Resurserna som har distribuerats region efter region på ett korrekt sätt. Integrerad hälsokontroll av Azure Deployment Manager kan övervaka distributioner och automatiskt stoppa problematiska distributioner, så att du kan felsöka och minska storleken på effekten. Den här funktionen kan minska tjänsten otillgänglig på grund av regression i uppdateringar.

## <a name="health-monitoring-providers"></a>Providers för hälsoövervakning

Microsoft har arbetat med några av företag så att du får en enkel kopiera och klistra in-lösning för att integrera hälsokontroller dina distributioner av övre service hälsoövervakning för att göra hälsotillstånd integration så enkel som möjligt. Om du inte redan använder en hälsoövervakare, är det här bra lösningar att börja med:

| ![Azure-distribution manager health monitor providern datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Azure-distribution manager health monitor providern site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure-distribution manager health monitor providern wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, den ledande övervakning och analytics-plattformen för moderna molnmiljöer. Se [hur Datadog kan integreras med Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, allt-i-ett privata och offentliga cloud services-lösningen för fjärrövervakning. Se [hur Site24x7 kan integreras med Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, övervakning och analys plattformen för med flera moln programmiljöer. Se [hur Wavefront kan integreras med Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Hur tjänstehälsa bestäms

[Providers för hälsoövervakning](#health-monitoring-providers) erbjuder flera metoder för att övervaka tjänster och varnar dig om eventuella problem för service health. [Azure Monitor](/services/monitor/) är ett exempel på ett erbjudande. Azure Monitor kan användas för att skapa aviseringar när vissa tröskelvärden överskrids. Till exempel ökar minnes- och CPU-belastningen utöver förväntade nivåer när du distribuerar en ny uppdatering till din tjänst. När ett meddelande om, kan du vidta åtgärder.

Dessa hälsovårdsleverantörer vanligen REST API: er så att statusen för din tjänst Övervakare kan undersökas programmässigt. REST-API: er kan antingen komma tillbaka med en enkel felfri/feltillstånd signal (bestäms av HTTP-svarskoden) och/eller med detaljerad information om signalerna som den tar emot.

Den nya *healthCheck* steg i Azure Deployment Manager kan du deklarera HTTP-koder som indikerar en felfri tjänst eller för mer komplexa REST resultat kan du även ange reguljära uttryck som om de matchar indikerar en felfri svar.

Flödet för att komma med hälsokontroller för Distributionshanteraren för Azure:

1. Skapa din hälsoövervakare via en health service provider valfri.
1. Skapa ett eller flera healthCheck steg som en del av Distributionshanteraren för Azure-distributionen. Fyll healthCheck steg med följande information:

    1. URI för REST-API för din health övervakar (som definieras av tjänstleverantören hälsotillstånd).
    1. Autentiseringsinformation. För närvarande stöds endast API-nyckel-autentisering.
    1. [HTTP-statuskoder](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) eller reguljära uttryck som definierar ett felfritt svar. Observera att du kan ange reguljära uttryck, som alla måste matchning för svaret för att anses vara felfria och du kan ge uttryck som alla måste matcha för att anses vara felfri svar. Båda metoderna stöds.

    Följande Json är ett exempel:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Anropa healthCheck stegen vid rätt tidpunkt i Distributionshanteraren för Azure-distribution. I följande exempel visas ett hälsotillstånd steg anropas i **postDeploymentSteps** av **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Om du vill gå igenom ett exempel, se [självstudien: Använda hälsokontroll i Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Faser i en hälsokontroll

Nu vet Azure Deployment Manager hur du frågar efter hälsotillståndet för din tjänst och vad faserna i distributionsprojektet för att göra detta. Distributionshanteraren för Azure kan dock också för djupgående konfigurationen tidsplan för dessa kontroller. Ett healthCheck steg körs i 3 sekventiella faser, som samtliga har konfigurerbara varaktigheter: 

1. Vänta

    1. När en Distributionsåtgärden har slutförts kan startas om virtuella datorer, konfigurera om baserat på nya data eller även startas för första gången. Det tar tid för tjänster för att börja avger hälsotillstånd signaler ska aggregeras av providern i ett beskrivande också. Under den här tumultuous processen kan det inte vara klokt att söka efter tjänstehälsa eftersom uppdateringen inte har ännu nå ett stabilt tillstånd. Verkligen, kan tjänsten vibrerande felfria och defekta emellan som resurserna reglera. 
    1. Under fasen för vänta övervakas inte tjänstehälsa. Det här används så att distribuerade resurserna tid att skapa innan du börjar kontrollera hälsa. 
1. Elastisk

    1. Eftersom det är omöjligt att veta i samtliga fall hur lång tid tar resurser för att skapa innan de blir stabila, elastisk fas möjliggör en flexibel tidsperioden mellan när resurserna är potentiellt instabil och när de inte behövs för att upprätthålla en felfri stabilt tillstånd.
    1. När den elastiska fasen inleds, börjar Azure Deployment Manager avsökning angivna REST-slutpunkten för tjänstehälsa med jämna mellanrum. Avsökningsintervallet kan konfigureras. 
    1. Om en hälsoövervakning kommer tillbaka med signaler som anger att tjänsten är i feltillstånd, dessa signaler ignoreras den elastiska fasen fortsätter och avsökning fortsätter. 
    1. När en hälsoövervakning kommer tillbaka med signaler som anger att tjänsten är felfri, elastisk fas slutar och fasen HealthyState börjar. 
    1. Därför är den tid som anges för den elastiska fasen längsta tid som kan användas avsökning för service health innan ett felfritt svar anses obligatorisk. 
1. HealthyState

    1. Under fasen för HealthyState avsöks kontinuerligt tjänstehälsa med samma intervall som elastiska fas. 
    1. Tjänsten förväntas att underhålla felfri signaler från övervakning Vårdgivare för hela den angivna tid. 
    1. Om ett feltillstånd svar identifieras när som helst, kommer Azure Deployment Manager stoppa hela distributionen och returnerar RESTEN svar överföra felaktig tjänst-signaler.
    1. När hela HealthyState har avslutats i healthCheck är klar och distributionen fortsätter till nästa steg.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hur du integrerar Azure Deployment Manager för övervakning av hälsotillstånd. Gå vidare till nästa artikel om du vill lära dig hur du distribuerar med Deployment Manager.

> [!div class="nextstepaction"]
> [Självstudie: integrera Azure Deployment Manager-hälsokontroll](./deployment-manager-tutorial-health-check.md)