---
title: Ta bort resursprovidern MySQL på Azure-stacken | Microsoft Docs
description: Lär dig hur du tar bort MySQL-resursprovidern från Azure Stack-distribution.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: d3a615e3b92a62709a787d0463dfa3148f14d07e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085806"
---
# <a name="remove-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovidern

Innan du tar bort MySQL-resursprovidern måste du ta bort alla beroenden för providern. Du måste även en kopia av de distributionspaket som användes för att installera resursprovidern.

## <a name="dependency-cleanup"></a>Beroende rensning

Det finns flera rensningsuppgifter att göra innan du kör skriptet DeployMySqlProvider.ps1 för att ta bort resursprovidern.

Innehavarna som ansvarar för rensning av följande:

* Ta bort alla databaser från resursprovidern. (Klient-databaserna inte bort data.)
* Avregistrera från leverantörens namnrymd.

Administratören är ansvarig för rensning av följande:

* Tar bort värdservrar från MySQL-kort.
* Tar bort de scheman som refererar till MySQL-kort.
* Tar bort kvoter som är associerade med MySQL-kort.

## <a name="to-remove-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovidern

1. Kontrollera att du har tagit bort alla befintliga MySQL resource provider beroenden.

   >[!NOTE]
   >Avinstallera resursprovidern MySQL fortsätter även om beroende resurser för närvarande använder resursprovidern.
  
2. Hämta en kopia av resursprovidern MySQL binära och kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.
3. Hämta en kopia av SQL-resursprovidern binära och kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.
4. Öppna ett nytt upphöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade de binära filerna för MySQL resource provider.
5. Kör skriptet DeployMySqlProvider.ps1 med följande parametrar:
    - **Avinstallera**. Tar bort resursprovidern och alla associerade resurser.
    - **PrivilegedEndpoint**. IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    - **CloudAdminCredential**. Autentiseringsuppgifter för molnadministratören krävs för att komma åt den privilegierade slutpunkten.
    - **DirectoryTenantID**
    - **AzCredential**. Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken.

## <a name="next-steps"></a>Nästa steg

[Erbjuda Apptjänster som PaaS](azure-stack-app-service-overview.md)
