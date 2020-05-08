---
title: Felsök vanliga distributionsfel
titleSuffix: Azure Load Balancer
description: Beskriver hur du löser vanliga fel när du distribuerar Azure Load Balancer
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 26b4232832589827a09774eb0106c25e5c446eb1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791093"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Felsök vanliga problem med Azure-distribution med Azure Load Balancer

I den här artikeln beskrivs några vanliga Azure Load Balancer distributions fel och information om hur du kan lösa felen. Om du letar efter information om en felkod och informationen inte finns i den här artikeln kan du berätta för oss. Längst ned på den här sidan kan du lämna feedback. Feedbacken spåras med GitHub-problem.

## <a name="error-codes"></a>Felkoder

| Felkod | Information och minskning |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Både offentlig IP SKU och Load Balancer SKU måste matcha. Se till att Azure Load Balancer och offentliga IP-SKU: er matchar. Standard-SKU rekommenderas för produktions arbets belastningar. Läs mer om [skillnaderna i SKU: er](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Skalnings uppsättningar för virtuella datorer är standard till grundläggande belastningsutjämnare när SKU: n har angetts eller distribuerats utan offentliga standard-IP-adresser. Distribuera om skalnings uppsättningen för virtuella datorer med standard offentliga IP: er på de enskilda instanserna för att säkerställa att Standard Load Balancer är markerat eller Välj en standard LB när du distribuerar den virtuella datorns skalnings uppsättning från Azure Portal. |
|MaxAvailabilitySetsInLoadBalancerReached | Backend-poolen för en Load Balancer kan innehålla högst 150 tillgänglighets uppsättningar. Om du inte har de tillgänglighets uppsättningar som uttryckligen definierats för dina virtuella datorer i backend-poolen, hamnar varje enskild virtuell dator i sin egen tillgänglighets uppsättning. Därför skulle distribution av 150 fristående virtuella datorer innebära att den skulle ha 150 tillgänglighets uppsättningar, så att gränsen uppnåddes. Du kan distribuera en tillgänglighets uppsättning och lägga till ytterligare virtuella datorer i den som en lösning. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Du kan inte ha mer än en regel för en angiven belastningsutjämnare (intern, offentlig) med samma server dels port och protokoll som refereras till av samma skalnings uppsättning för den virtuella datorn. Uppdatera regeln om du vill ändra den här regeln för att skapa dubbletter. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Du kan inte ha mer än en regel för en angiven belastningsutjämnare (intern, offentlig) med samma server dels port och protokoll som refereras till av samma skalnings uppsättning för den virtuella datorn. Uppdatera dina regel parametrar om du vill ändra den här dubbletten av regeln. |
|AnotherInternalLoadBalancerExists| Du kan bara ha en Load Balancer av typen intern referens till samma uppsättning virtuella datorer/nätverks gränssnitt i Server delen för Load Balancer. Uppdatera din distribution så att du bara skapar en Load Balancer av samma typ. |
|CannotUseInactiveHealthProbe| Du kan inte ha en avsökning som inte används av någon regel som kon figurer ATS för skalnings uppsättnings hälsa för virtuella datorer. Se till att avsökningen som har kon figurer ATS används aktivt. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Du kan inte ha flera belastningsutjämnare av samma typ (intern, offentlig). Du kan ha högst en intern och en offentlig Load Balancer. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Basic-Load Balancer stöds inte för grupper av virtuella datorer med flera placerings grupper eller skalnings uppsättning för virtuella datorer i olika zoner. Använd Standard Load Balancer i stället. |
|ResourceDeploymentFailure| Om belastningsutjämnaren är i fel tillstånd följer du de här stegen för att ta tillbaka den från det felaktiga läget:<ol><li>Gå till https://resources.azure.comoch logga in med dina Azure Portal autentiseringsuppgifter.</li><li>Välj **Läs/skriv**.</li><li>Till vänster expanderar du **prenumerationer**och expanderar sedan prenumerationen med Load Balancer att uppdatera.</li><li>Expandera **ResourceGroups**och expandera sedan resurs gruppen med Load Balancer att uppdatera.</li><li>Välj **Microsoft. Network** > **belastningsutjämnare**och välj sedan Load Balancer att uppdatera **LoadBalancer_1**.</li><li>På visnings sidan för **LoadBalancer_1**väljer du **Hämta** > **redigering**.</li><li>Det **gick inte** att uppdatera **ProvisioningState** - **värdet.**</li><li>Välj **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Nästa steg

* Titta igenom [jämförelse tabellen](./skus.md) för Azure Load Balancer SKU
* Läs mer om [Azure Load Balancer gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
