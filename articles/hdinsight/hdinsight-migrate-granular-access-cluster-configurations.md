---
title: Ändringar av konfigurationen åtkomst – Azure HDInsight-kluster
description: Läs mer om att ändringarna ska komma åt känsliga kluster konfigurationsfält.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610249"
---
# <a name="changes-to-cluster-configuration-access"></a>Ändringar i konfigurationen Klusteråtkomst

Den senaste versionen av SDK: N för Azure HDInsight ger dig några viktiga förändringar för mer detaljerad rollbaserad åtkomst för att få känslig information. Som en del av dessa ändras, vissa **åtgärd kan krävas** om du använder en av metoderna som påverkas.

## <a name="sdk-for-net-500"></a>SDK för .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) kommer **inte längre returneras känsliga parametrar** som lagringsnycklar (core-plats) eller HTTP-autentiseringsuppgifter (gateway).
    - Använd för att hämta alla inställningar, till exempel känsliga parametrar `ConfigurationOperationsExtensions.List` framöver.  Observera att användare med rollen ”läsare” inte kommer att kunna använda den här metoden. Det möjliggör detaljerad kontroll över vilka användare kan komma åt känslig information för ett kluster. 
    - Använd för att hämta bara HTTP-gateway-autentiseringsuppgifter, `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) Nu är föråldrade och har ersatts av `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) och [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) nu är föråldrade. HTTP är nu alltid aktiverad, så dessa metoder är inte längre behövs.