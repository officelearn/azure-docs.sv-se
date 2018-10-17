---
title: Ta bort MySQL-resursprovider i Azure Stack | Microsoft Docs
description: Lär dig hur du tar bort MySQL-resursprovider i Azure Stack-distributioner.
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: dcd1c40717cb35fe4daa9ab9e2c66f334ffff5fe
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361506"
---
# <a name="remove-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovider

Innan du tar bort MySQL-resursprovider måste du ta bort alla beroenden för providern. Du måste också en kopia av de distributionspaket som användes för att installera resursprovidern.

## <a name="dependency-cleanup"></a>Beroende rensning

Det finns flera rensningsuppgifter innan du kör skriptet DeployMySqlProvider.ps1 för att ta bort resursprovidern.

Innehavarna som ansvarar för att rensa följande uppgifter:

* Ta bort alla sina databaser från resursprovidern. (Klientdatabaserna inte bort data.)
* Avregistrera från leverantörens namnområde.

Administratören ansvarar för att rensa följande uppgifter:

* Tar bort värdservrar från MySQL-kortet.
* Tar bort eventuella planer som refererar till MySQL-nätverkskort.
* Tar bort alla kvoter som är associerade med MySQL-kort.

## <a name="to-remove-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovider

1. Kontrollera att du har tagit bort alla befintliga MySQL resource provider beroenden.

   >[!NOTE]
   >Avinstallera MySQL-resursprovider fortsätter även om beroende resurser använder resursprovidern.
  
2. Hämta en kopia av MySQL-resursprovider binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog.
3. Hämta en kopia av SQL-resursprovider binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog.
4. Öppna en ny förhöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade de binära filerna för MySQL resource provider.
5. Kör skriptet DeployMySqlProvider.ps1 med följande parametrar:
    - **Avinstallera**. Tar bort resursprovidern och alla associerade resurser.
    - **PrivilegedEndpoint**. IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    - **AzureEnvironment**. Azure-miljön används för att distribuera Azure Stack. Krävs endast för Azure AD-distributioner.
    - **CloudAdminCredential**. Autentiseringsuppgifter för molnadministratören krävs för att få åtkomst till privilegierad slutpunkt.
    - **DirectoryTenantID**
    - **AzCredential**. Autentiseringsuppgifter för Azure Stack-tjänstadministratörskonto. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure Stack.

## <a name="next-steps"></a>Nästa steg

[Erbjudande om Apptjänster som PaaS](azure-stack-app-service-overview.md)
