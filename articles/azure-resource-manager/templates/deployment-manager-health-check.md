---
title: Utbyggnad av hälsointegrering – Azure Deployment Manager
description: Beskriver hur du distribuerar en tjänst över många regioner med Azure Deployment Manager. Den visar säkra distributionsmetoder för att verifiera stabiliteten i distributionen innan du distribuerar till alla regioner.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273805"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Införa distribution av hälsointegrering i Azure Deployment Manager (offentlig förhandsversion)

[Med Azure Deployment Manager](./deployment-manager-overview.md) kan du utföra stegvisa distributioner av Azure Resource Manager-resurser. Resurserna distribueras region efter region på ett ordnat sätt. Den integrerade hälsokontrollen av Azure Deployment Manager kan övervaka distributioner och automatiskt stoppa problematiska distributioner, så att du kan felsöka och minska effektens omfattning. Den här funktionen kan minska otillgängligheten för tjänsten som orsakas av regressioner i uppdateringar.

## <a name="health-monitoring-providers"></a>Vårdgivare för hälsoövervakning

För att göra hälsointegrering så enkelt som möjligt har Microsoft arbetat med några av de bästa servicehälsoövervakningsföretagen för att ge dig en enkel lösning för att kopiera/klistra in för att integrera hälsokontroller med dina distributioner. Om du inte redan använder en hälsoövervakare är det här bra lösningar som du kan börja med:

| ![azure deployment manager hälsa övervaka provider datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![hälsoövervakare av azure deployment manager 24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure deployment manager hälsa övervaka provider wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, den ledande övervaknings- och analysplattformen för moderna molnmiljöer. Se [hur Datadog integreras med Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, allt-i-ett privata och offentliga molntjänster övervakningslösning. Se [hur Site24x7 integreras med Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, övervaknings- och analysplattformen för programmiljöer med flera moln. Se [hur Wavefront integreras med Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Hur tjänstens hälsa bestäms

[Hälsoövervakningsleverantörer](#health-monitoring-providers) erbjuder flera mekanismer för att övervaka tjänster och varna dig för eventuella hälsofrågor för tjänster. [Azure Monitor](../../azure-monitor/overview.md) är ett exempel på ett sådant erbjudande. Azure Monitor kan användas för att skapa aviseringar när vissa tröskelvärden överskrids. Till exempel, ditt minne och CPU-användning spik utöver förväntade nivåer när du distribuerar en ny uppdatering till din tjänst. När du meddelas kan du vidta korrigerande åtgärder.

Dessa vårdgivare erbjuder vanligtvis REST API:er så att statusen för tjänstens övervakare kan undersökas programmässigt. REST-API:erna kan antingen komma tillbaka med en enkel felfri/ohälsosam signal (bestämd av HTTP-svarskoden) och/eller med detaljerad information om de signaler den tar emot.

Med det nya *healthCheck-steget* i Azure Deployment Manager kan du deklarera HTTP-koder som anger en felfri tjänst, eller, för mer komplexa REST-resultat, kan du till och med ange reguljära uttryck som, om de matchar, indikerar ett felfritt svar.

Flödet för att få installation med hälsokontroller av Azure Deployment Manager:

1. Skapa din hälsa monitorer via en vårdgivare som du väljer.
1. Skapa en eller flera healthCheck-steg som en del av distributionsdistributionshanterarens distributionsdistribution. Fyll i hälsokontrollera stegen med följande information:

    1. Uri för REST API för dina hälsoövervakare (enligt definitionen av din vårdgivare).
    1. Autentiseringsinformation. För närvarande stöds endast API-nyckelformatautentisering.
    1. [HTTP-statuskoder](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) eller reguljära uttryck som definierar ett felfritt svar. Observera att du kan ge reguljära uttryck, som ALLA måste matcha för att svaret ska betraktas som felfritt, eller så kan du ge uttryck som ANY måste matcha för att svaret ska betraktas som felfritt. Båda metoderna stöds.

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

1. Anropa healthCheck-stegen vid lämplig tidpunkt i distributionsdistributionshanterarens distributionshanterare. I följande exempel anropas ett hälsokontrollsteg i **postDeploymentSteps** i **stepGroup2**.

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

Information om hur du går igenom ett exempel finns [i Självstudiekurs: Använd hälsokontroll i Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Faser av en hälsokontroll

Nu vet Azure Deployment Manager hur man frågar efter hälsotillståndet för din tjänst och vid vilka faser i distributionen för att göra det. Azure Deployment Manager möjliggör dock också djup konfiguration av tidpunkten för dessa kontroller. Ett healthCheck-steg körs i 3 sekventiella faser, som alla har konfigurerbara varaktigheter: 

1. Vänta

    1. När en distributionsåtgärd har slutförts kan virtuella datorer startas om, konfigureras om baserat på nya data eller till och med startas för första gången. Det tar också tid för tjänster att börja avge hälsosignaler som ska aggregeras av vårdgivaren till något användbart. Under denna tumultartade process, Det kanske inte är meningsfullt att kontrollera om tjänsten hälsa eftersom uppdateringen ännu inte har nått ett stabilt tillstånd. I själva verket kan tjänsten pendla mellan friska och ohälsosamma tillstånd som resurserna lösa. 
    1. Under väntefasen övervakas inte tjänstens hälsotillstånd. Detta används för att ge de distribuerade resurserna tid att baka innan hälsokontrollen påbörjas. 
1. Elastisk

    1. Eftersom det är omöjligt att veta i alla fall hur lång tid det tar att baka innan de blir stabila, möjliggör den elastiska fasen en flexibel tidsperiod mellan när resurserna är potentiellt instabila och när de krävs för att upprätthålla en hälsosam stadig Statligt.
    1. När den elastiska fasen börjar börjar Azure Deployment Manager avsökning av den angivna REST-slutpunkten för tjänstens hälsotillstånd med jämna mellanrum. Avsökningsintervallet kan konfigureras. 
    1. Om hälsoövervakaren kommer tillbaka med signaler som indikerar att tjänsten är ohälsosam ignoreras dessa signaler, elasticfasen fortsätter och avsökningen fortsätter. 
    1. Så snart hälsoövervakaren kommer tillbaka med signaler som indikerar att tjänsten är felfri, slutar elasticfasen och HealthyState-fasen börjar. 
    1. Den angivna varaktigheten för fasen Elastisk är därför den maximala tid som kan spenderas avsökning för tjänstens hälsotillstånd innan ett hälsosamt svar anses obligatoriskt. 
1. HealthyState (Hälsosamtfritt)

    1. Under healthyState-fasen genomsöks tjänstens hälsotillstånd kontinuerligt med samma intervall som elasticfasen. 
    1. Tjänsten förväntas upprätthålla felfria signaler från hälsoövervakningsprovidern under hela den angivna varaktigheten. 
    1. Om ett felfritt svar upptäcks vid något tillfälle stoppar Azure Deployment Manager hela distributionsdistributionen och returnerar REST-svaret som bär de felaktiga tjänstsignalerna.
    1. När varaktigheten för HealthyState har avslutats är healthCheck klar och distributionen fortsätter till nästa steg.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du integrerar hälsoövervakning i Azure Deployment Manager. Gå vidare till nästa artikel och lär dig hur du distribuerar med Deployment Manager.

> [!div class="nextstepaction"]
> [Självstudiekurs: integrera hälsokontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)