---
title: Utveckla appar för Azure-Stack | Microsoft Docs
description: Utveckling överväganden för prototyper program på Azure-stacken
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="develop-for-azure-stack"></a>Utveckla för Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan börja utveckla program idag, även om du inte har åtkomst till en Azure-Stack-miljö. Eftersom Azure Stack ger Microsoft Azure-tjänster som körs i ditt datacenter, kan du använda liknande verktyg och processer för att utveckla mot stacken Azure precis som med Azure. Med vissa förberedelser och med hjälp av vägledningen i följande avsnitt kan använda du Azure för att emulera en Azure-Stack-miljö.

* I Azure, kan du skapa Azure Resource Manager-mallar som distribueras till Azure-stacken. Se [överväganden](azure-stack-develop-templates.md) anvisningar om hur du utvecklar mallar för att kontrollera överföring.
* Det finns skillnader i tjänstetillgänglighet och versionhantering mellan Azure och Azure-stacken. Du kan använda den [Azure Stack principmodulen](azure-stack-policy-module.md) att begränsa Azure tjänsttyper för tillgänglighet och resursen till vad som är tillgängligt i Azure-stacken. Begränsa tjänster garanterar att dina program som förlitar sig på tjänster som är tillgängliga för Azure-stacken.
* Den [Azure Stack-Snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates) är vanliga Scenarioexempel som visar hur du utvecklar mallar som kan distribueras till Azure och Azure-stacken.
