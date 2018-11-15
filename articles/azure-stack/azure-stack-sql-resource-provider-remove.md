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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b7af23ccdd379aac9959bb9993fc1781a44e705e
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684034"
---
# <a name="remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovider

Innan du tar bort SQL-resursprovider måste du ta bort alla beroenden för providern. Du måste också en kopia av de distributionspaket som användes för att installera resursprovidern.

  |Lägsta version av Azure Stack|SQL RP-version|
  |-----|-----|
  |Version 1808 (1.1808.0.97)|[SQL RP version 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |Version 1804 (1.0.180513.1)|[SQL RP version 1.1.24.0](https://aka.ms/azurestacksqlrp11240)
  |     |     |

## <a name="dependency-cleanup"></a>Beroende rensning

Det finns flera rensningsuppgifter innan du kör skriptet DeploySqlProvider.ps1 för att ta bort resursprovidern.

Azure Stack-klientanvändare ansvarar för att rensa följande uppgifter:

* Ta bort alla sina databaser från resursprovidern. (Klientdatabaserna inte bort data.)
* Avregistrera från leverantörens namnområde.

Azure Stack-operatör ansvarar för att rensa följande uppgifter:

* Tar bort värdservrar från MySQL-kortet.
* Tar bort eventuella planer som refererar till MySQL-nätverkskort.
* Tar bort alla kvoter som är associerade med MySQL-kort.

## <a name="to-remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovider

1. Kontrollera att du har tagit bort alla befintliga SQL resource provider-beroenden.

   > [!NOTE]
   > Avinstallera SQL-resursprovider fortsätter även om beroende resurser använder resursprovidern.
  
2. Hämta en kopia av SQL-resursprovider binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog.

3. Öppna en ny förhöjd PowerShell-konsolfönster och ändra till den katalog där du extraherade de binära filerna för SQL resource provider.

4. Kör skriptet DeploySqlProvider.ps1 med följande parametrar:

    * **Avinstallera**. Tar bort resursprovidern och alla associerade resurser.
    * **PrivilegedEndpoint**. IP-adressen eller DNS-namnet på den privilegierade slutpunkten.
    * **AzureEnvironment**. Azure-miljön används för att distribuera Azure Stack. Krävs endast för Azure AD-distributioner.
    * **CloudAdminCredential**. Autentiseringsuppgifter för molnadministratören krävs för att få åtkomst till privilegierad slutpunkt.
    * **AzCredential**. Autentiseringsuppgifter för Azure Stack-tjänstadministratörskonto. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure Stack.

## <a name="next-steps"></a>Nästa steg

[Erbjudande om Apptjänster som PaaS](azure-stack-app-service-overview.md)
