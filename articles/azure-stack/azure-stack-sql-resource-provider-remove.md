---
title: Ta bort resursprovidern SQL Azure-stacken | Microsoft Docs
description: Lär dig hur du tar bort SQL-resursprovidern från Azure Stack-distribution.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294788"
---
# <a name="removing-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovidern  
Innan du tar bort SQL-resursprovidern är det viktigt att du först ta bort eventuella beroenden.

## <a name="remove-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovidern 

1. Kontrollera att du har tagit bort alla befintliga SQL-providern resursberoenden.

  > [!NOTE]
  > Avinstallera SQL resursprovidern fortsätter även om beroende resurser för närvarande använder resursprovidern. 
  
2. Se till att du har de ursprungliga distributionspaket som du hämtade för den här versionen av SQL resource provider nätverkskort.
3. Kör skriptet för distribution med följande parametrar:
    - Använd-avinstallera parametern
    - IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    - Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten.
    - Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken.

## <a name="next-steps"></a>Nästa steg
[Erbjuda Apptjänster som PaaS](azure-stack-app-service-overview.md)
