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
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 2740da5a51e95a327a868734a7f009dddf40219a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964942"
---
# <a name="remove-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovider

Innan du tar bort MySQL-resursprovider måste du ta bort alla beroenden för providern. Du måste också en kopia av de distributionspaket som användes för att installera resursprovidern.

> [!NOTE]
> Du kan hitta länkarna för resursen providern installationsprogram i [distribuera resource provider krav](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Ta bort MySQL-resursprovider tar inte bort klientdatabaser från som är värd för servrar.

## <a name="dependency-cleanup"></a>Beroende rensning

Det finns flera rensningsuppgifter innan du kör skriptet DeployMySqlProvider.ps1 för att ta bort resursprovidern.

Azure Stack-operatör ansvarar för att rensa följande uppgifter:

* Ta bort eventuella planer som refererar till MySQL-nätverkskort.
* Ta bort kvoter som är associerade med MySQL-kort.

## <a name="to-remove-the-mysql-resource-provider"></a>Ta bort MySQL-resursprovider

1. Kontrollera att du har tagit bort alla befintliga MySQL resource provider beroenden.

   > [!NOTE]
   > Avinstallera MySQL-resursprovider fortsätter även om beroende resurser använder resursprovidern.
  
2. Hämta en kopia av installationspaketet för MySQL resource provider och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog.
3. Öppna en ny förhöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade MySQL resource provider-installationsfilerna.
4. Kör skriptet DeployMySqlProvider.ps1 med följande parametrar:
    - **Avinstallera**. Tar bort resursprovidern och alla associerade resurser.
    - **PrivilegedEndpoint**. IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    - **AzureEnvironment**. Azure-miljön används för att distribuera Azure Stack. Krävs endast för Azure AD-distributioner.
    - **CloudAdminCredential**. Autentiseringsuppgifter för molnadministratören krävs för att få åtkomst till privilegierad slutpunkt.
    - **DirectoryTenantID**
    - **AzCredential**. Autentiseringsuppgifter för Azure Stack-tjänstadministratörskonto. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure Stack.

## <a name="next-steps"></a>Nästa steg

[Erbjudande om Apptjänster som PaaS](azure-stack-app-service-overview.md)
