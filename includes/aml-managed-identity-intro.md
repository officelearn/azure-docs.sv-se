---
title: inkludera fil
description: inkludera fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147604"
---
 Azure Machine Learning beräknings kluster stöder även [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för att autentisera åtkomsten till Azure-resurser utan att inkludera autentiseringsuppgifter i din kod. Det finns två typer av hanterade identiteter:

* En **systemtilldelad hanterad identitet** aktive ras direkt i Azure Machine Learning beräknings kluster. Livs cykeln för en tilldelad identitet är direkt knuten till beräknings klustret. Om beräknings klustret tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD.
* En **användare som tilldelats en hanterad identitet** är en fristående Azure-resurs som tillhandahålls via Azure Managed Identity service. Du kan tilldela en användardefinierad hanterad identitet till flera resurser och den finns kvar så länge du vill.