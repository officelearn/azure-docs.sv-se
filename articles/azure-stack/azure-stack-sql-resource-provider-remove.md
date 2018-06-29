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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b73deebb10d0c81a06df9cd192eaa2ef28de744d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083050"
---
# <a name="remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovidern

Innan du tar bort SQL-resursprovidern måste du ta bort alla beroenden för providern. Du måste även en kopia av de distributionspaket som användes för att installera resursprovidern.

Det finns flera rensningsuppgifter innan du kör den _DeploySqlProvider.ps1_ skript för att ta bort resursprovidern.
Innehavarna som ansvarar för rensning av följande:

* Ta bort alla databaser från resursprovidern. (Klient-databaserna inte bort data.)
* Avregistrera från resurs-providerns namnrymd.

Administratören är ansvarig för rensning av följande:

* Tar bort värdservrar från SQL-resursprovidern.
* Tar bort de scheman som refererar till SQL-resursprovidern.
* Tar bort kvoter som är associerade med SQL-resursprovidern.

## <a name="to-remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovidern

1. Kontrollera att du har tagit bort alla befintliga SQL resource provider-beroenden.

   > [!NOTE]
   > Avinstallera SQL resursprovidern fortsätter även om beroende resurser för närvarande använder resursprovidern.
  
2. Hämta en kopia av SQL-resursprovidern binära och kör sedan Self-Extractor extrahera innehållet till en tillfällig katalog.

3. Öppna ett nytt upphöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade de binära filerna för SQL resource provider.

4. Kör skriptet DeploySqlProvider.ps1 med följande parametrar:

    * **Avinstallera**. Tar bort resursprovidern och alla associerade resurser.
    * **PrivilegedEndpoint**. IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    * **CloudAdminCredential**. Autentiseringsuppgifter för molnadministratören krävs för att komma åt den privilegierade slutpunkten.
    * **AzCredential**. Autentiseringsuppgifterna för kontot Azure Stack-administratör. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure-stacken.

## <a name="next-steps"></a>Nästa steg

[Erbjuda Apptjänster som PaaS](azure-stack-app-service-overview.md)
