---
title: Med hjälp av SQL-databaser på Azure-stacken | Microsoft Docs
description: Lär dig hur du kan distribuera SQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera SQL Server resource provider nätverkskort.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovidern

Om du vill ta bort SQL-resursprovidern, är det viktigt att du först ta bort eventuella beroenden:

1. Se till att du har de ursprungliga distributionspaket som du hämtade för den här versionen av SQL resource provider nätverkskort.

2. Alla användardatabaser som måste tas bort från resursprovidern. (Databaserna inte bort data.) Den här uppgiften ska utföras av användarna själva.

3. Administratören måste ta bort värdservrar från SQL resource provider nätverkskort.

4. Administratören måste ta bort de scheman som refererar till SQL resource provider nätverkskort.

5. Administratören måste ta bort alla SKU: er och kvoter som är associerade med SQL resource provider-kort.

6. Kör skriptet för distribution med följande element:
    - -Parametern avinstallera
    - Azure Resource Manager-slutpunkter
    - DirectoryTenantID
    - Autentiseringsuppgifterna för tjänstkontot för administratör

