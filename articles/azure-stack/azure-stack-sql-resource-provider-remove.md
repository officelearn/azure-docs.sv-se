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
ms.date: 09/13/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 42d8a5a8073d2650b9e023305472f28d4f1c738f
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580107"
---
# <a name="remove-the-sql-resource-provider"></a>Ta bort SQL-resursprovider

Innan du tar bort SQL-resursprovider måste du ta bort alla beroenden för providern. Du måste också en kopia av de distributionspaket som användes för att installera resursprovidern.

Det finns flera rensningsuppgifter göra innan du kör den _DeploySqlProvider.ps1_ skript för att ta bort resursprovidern.
Innehavarna som ansvarar för att rensa följande uppgifter:

* Ta bort alla sina databaser från resursprovidern. (Klientdatabaserna inte bort data.)
* Avregistrera från resursproviderns namnområde.

Administratören ansvarar för att rensa följande uppgifter:

* Tar bort värdservrar från SQL-resursprovider.
* Tar bort eventuella planer som refererar till SQL-resursprovider.
* Tar bort alla kvoter som är associerade med SQL-resursprovider.

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
