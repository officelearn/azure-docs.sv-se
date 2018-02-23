---
title: "Utveckla appar för Azure-Stack | Microsoft Docs"
description: "Läs development considerations i prototyper program på Azure-stacken"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 4b9e0f5a264296e59579539dd929f5d75a54b62a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="develop-for-azure-stack"></a>Utveckla för Azure Stack
Du kan börja utveckla program idag, även om du inte har åtkomst till en Azure-Stack-miljö. Eftersom Azure Stack ger Microsoft Azure-tjänster som körs i ditt datacenter, kan du använda liknande verktyg och processer för att utveckla mot stacken Azure precis som med Azure.  Du kan använda Azure för att emulera en Azure-Stack-miljö med en bit i förberedelser och vägledning från följande avsnitt:

* I Azure, kan du skapa Azure Resource Manager-mallar som är också distribueras till Azure-stacken.  Se [överväganden](azure-stack-develop-templates.md) anvisningar om hur du utvecklar dina mallar för att säkerställa överföring.
* Det finns en lista i tjänstetillgänglighet och versionhantering mellan Azure och Azure-stacken. Du kan använda den [Azure Stack principmodulen](azure-stack-policy-module.md) att begränsa Azure tjänsttyper för tillgänglighet och resursen till vad som är tillgängligt i Azure-stacken. Begränsa tillgängliga tjänster hjälper ditt program som förlitar sig på tjänster som är tillgängliga för Azure-stacken.
* Den [Azure Stack-Snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates) är vanliga Scenarioexempel på hur du utvecklar dina mallar så att de kan distribueras till både Azure och Azure-stacken.


