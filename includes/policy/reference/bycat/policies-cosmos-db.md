---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f108d688c7060c2ae110544ac65c4f27359be242
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347128"
---
|Namn<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Cosmos DB konto ska använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |Använd Kundhanterade nycklar för att kontrol lera krypteringen vid resten av de data som lagras i Azure Cosmos DB när detta är ett krav för regler eller efterlevnad. Kundhanterade nycklar levererar också Double Encryption genom att lägga till ett andra lager av kryptering ovanpå det som är standard med tjänst hanterade nycklar. Kontakta [https://aka.ms/cosmosdb-cmk](https://aka.ms/cosmosdb-cmk) |granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |
|[Azure Cosmos DB konton måste ha brand Väggs regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |Granska eller neka resurser som inte har några konfigurerade IP-regler och som standard tillåter alla nätverk. Konton som har minst en IP-regel som definierats med det virtuella nätverks filtret aktiverat anses vara kompatibla. Konton som inaktiverar offentlig åtkomst anses också uppfylla kraven. |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Cosmos DB tillåtna platser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |Med den här principen kan du begränsa vilka platser din organisation kan ange när du distribuerar Azure Cosmos DB-resurser. Den används för att genomdriva kraven på geo-efterlevnad. |[parameters (' policyEffect ')] |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Skriv åtkomst för Azure Cosmos DB nyckelbaserade metadata måste inaktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5) |Med den här principen kan du se till att alla Azure Cosmos DB konton inaktiverar nyckelbaserade metadata skriv åtkomst. |slå |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableMetadata_Append.json) |
|[Azure Cosmos DB data flödet bör begränsas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b7ef78e-a035-4f23-b9bd-aff122a1b1cf) |Med den här principen kan du begränsa det maximala data flöde som din organisation kan ange när du skapar Azure Cosmos DB databaser och behållare via resurs leverantören. Den blockerar skapandet av autoskalning-resurser. |granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json) |
|[Distribuera Avancerat skydd för Cosmos DB-konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |Den här principen aktiverar Avancerat skydd mellan Cosmos DB-konton. |DeployIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
