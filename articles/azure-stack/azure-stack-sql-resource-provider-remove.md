---
title: Ta bort SQL-resursprovider i Azure Stack | Microsoft Docs
description: Lär dig hur du kan ta bort SQL-resursprovider från Azure Stack-distributionen.
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
ms.openlocfilehash: b79d64cc063105cb8ecce537a09a7f39a78eef4c
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275035"
---
# <a name="remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovider

Innan du tar bort SQL-resursprovider måste du ta bort alla beroenden för providern. Du måste också en kopia av de distributionspaket som användes för att installera resursprovidern.

> [!NOTE]
> Du kan hitta länkarna för resursen providern installationsprogram i [distribuera resource provider krav](.\azure-stack-sql-resource-provider-deploy.md#prerequisites).

SQL-resursprovider bort inte klientdatabaser från som är värd för servrar.

## <a name="dependency-cleanup"></a>Beroende rensning

Det finns flera rensningsuppgifter innan du kör skriptet DeploySqlProvider.ps1 för att ta bort resursprovidern.

Azure Stack-operatör ansvarar för att rensa följande uppgifter:

* Ta bort eventuella planer som refererar till SQL-Adapter.
* Ta bort kvoter som är associerade med SQL-Adapter.

## <a name="to-remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovider

1. Kontrollera att du har tagit bort alla befintliga SQL resource provider-beroenden.

   > [!NOTE]
   > Avinstallera SQL-resursprovider fortsätter även om beroende resurser använder resursprovidern.
  
2. Hämta en kopia av installationspaketet för SQL resource provider och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog.

3. Öppna en ny förhöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade installationsfilerna för SQL resource provider.

4. Kör skriptet DeploySqlProvider.ps1 med följande parametrar:

    * **Avinstallera**. Tar bort resursprovidern och alla associerade resurser.
    * **PrivilegedEndpoint**. IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    * **AzureEnvironment**. Azure-miljön används för att distribuera Azure Stack. Krävs endast för Azure AD-distributioner.
    * **CloudAdminCredential**. Autentiseringsuppgifter för molnadministratören krävs för att få åtkomst till privilegierad slutpunkt.
    * **AzCredential**. Autentiseringsuppgifter för Azure Stack-tjänstadministratörskonto. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure Stack.

## <a name="next-steps"></a>Nästa steg

[Erbjudande om Apptjänster som PaaS](azure-stack-app-service-overview.md)
