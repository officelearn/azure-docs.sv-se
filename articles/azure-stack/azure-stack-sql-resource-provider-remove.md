---
title: Ta bort resursprovidern SQL Azure-stacken | Microsoft Docs
description: Lär dig hur du kan ta bort SQL-resursprovidern från Azure Stack-distribution.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301906"
---
# <a name="remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovidern

Innan du tar bort SQL-resursprovidern måste du ta bort alla beroenden för providern. Du måste även en kopia av de distributionspaket som användes för att installera resursprovidern.

## <a name="to-remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovidern

1. Kontrollera att du har tagit bort alla befintliga SQL resource provider-beroenden.

   > [!NOTE]
   > Avinstallera SQL resursprovidern fortsätter även om beroende resurser för närvarande använder resursprovidern.
  
2. Hämta en kopia av SQL-resursprovidern binära och kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.

3. Öppna ett nytt upphöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade de binära filerna för SQL resource provider.

4. Kör skriptet DeploySqlProvider.ps1 med följande parametrar:

    - **Avinstallera**. Tar bort resursprovidern och alla associerade resurser.
    - **PrivilegedEndpoint**. IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    - **CloudAdminCredential**. Autentiseringsuppgifter för molnadministratören krävs för att komma åt den privilegierade slutpunkten.
    - **AzCredential**. Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken.

## <a name="next-steps"></a>Nästa steg

[Erbjuda Apptjänster som PaaS](azure-stack-app-service-overview.md)
