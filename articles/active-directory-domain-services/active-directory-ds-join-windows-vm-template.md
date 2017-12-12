---
title: Anslut en Windows Server-dator till Azure Active Directory Domain Services | Microsoft Docs
description: "Anslut en virtuell dator med Windows Server till en hanterad domän med hjälp av Azure Resource Manager-mallar."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: b90198696b7bdb9277fd2f2b8e8e727af42c5cfa
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Anslut en virtuell dator med Windows Server till en hanterad domän med en Resource Manager-mall
Den här artikeln visar hur du kopplar en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän med hjälp av Resource Manager-mallar.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [hur du uppdaterar DNS-inställningarna för virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)
5. Slutför steg som krävs för att [synkronisera lösenord till din Azure AD Domain Services-hanterad domän](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Installera och konfigurera verktyg som krävs
Du kan använda något av följande alternativ för att utföra stegen som beskrivs i det här dokumentet:
* **Azure PowerShell**: [installera och konfigurera](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure plattformsoberoende kommandoradsgränssnittet**: [installera och konfigurera](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Alternativ 1: Etablera en ny Windows virtuell dator och ansluta den till en hanterad domän
**Snabbstart mallnamn**: [201-vm-domänanslutning](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Att distribuera en virtuell dator med Windows Server och ansluta den till en hanterad domän, utför följande steg:
1. Navigera till den [Snabbstart mallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Klicka på **Distribuera till Azure**.
3. I den **anpassad distribution** Tillhandahåll informationen som krävs för att etablera den virtuella datorn.
4. Välj den **Azure-prenumeration** att etablera den virtuella datorn. Välj samma Azure-prenumerationen som du har aktiverat Azure AD Domain Services.
5. Välj en befintlig **resursgruppen** eller skapa en ny.
6. Välj en **plats** där du vill distribuera den nya virtuella datorn.
7. I **befintliga VNET namnet**, ange det virtuella nätverket som du har distribuerat din Azure AD Domain Services-hanterad domän.
8. I **befintliga Undernätsnamn**, ange undernätet i det virtuella nätverket där du vill distribuera den virtuella datorn. Välj inte gateway-undernätet i det virtuella nätverket. Välj också inte dedikerade undernätet där din hanterade domän har distribuerats.
9. I **DNS-Prefix för etiketten**, ange värdnamnet för den virtuella datorn håller på att etableras. Till exempel ”contoso-win'.
10. Välj lämpliga **VM-storlek** för den virtuella datorn.
11. I **domänanslutning till**, ange DNS-domännamnet för din hanterade domän.
12. I **domänanvändarnamn**, ange namnet på användarkontot på hanterade domänen som ska användas för att ansluta den virtuella datorn till den hanterade domänen.
13. I **domänlösenord**, ange lösenordet för det domänanvändarkonto som anges av parametern 'domainUsername'.
14. Valfritt: Du kan ange en **OU sökvägen** till en anpassad Organisationsenhet där du vill lägga till den virtuella datorn. Om du inte anger ett värde för den här parametern den virtuella datorn har lagts till i standard **AAD DC datorer** OU på den hanterade domänen.
15. I den **VM administratörsanvändarnamnet** anger en lokal administratörskontonamnet för den virtuella datorn.
16. I den **VM adminlösenord** anger ett lösenord för lokal administratör för den virtuella datorn. Ange ett starkt lokala administratörslösenordet för den virtuella datorn att skydda mot lösenord brute force-attacker.
17. Klicka på **jag samtycker till villkoren som anges ovan**.
18. Klicka på **inköp** att etablera den virtuella datorn.

> [!WARNING]
> **Hantera lösenord med försiktighet.**
> Parametern mallfilen innehåller lösenord för domänkonton samt lokala administratörslösenord för den virtuella datorn. Se till att du lämnar den här filen ligger runt på filresurser eller andra delade platser. Vi rekommenderar att du ta bort den här filen när du är klar distribuera virtuella datorer.
>

När distributionen är klar är din nyetablerade virtuella Windows-dator ansluten till den hanterade domänen.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Alternativ 2: Anslut en befintlig Windows virtuell dator till en hanterad domän
**Snabbstart mallen**: [201 vm-domain-join-finns](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Utför följande steg för att ansluta till en befintlig Windows Server virtuell dator till en hanterad domän:
1. Navigera till den [Snabbstart mallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Klicka på **Distribuera till Azure**.
3. I den **anpassad distribution** Tillhandahåll informationen som krävs för att etablera den virtuella datorn.
4. Välj den **Azure-prenumeration** att etablera den virtuella datorn. Välj samma Azure-prenumerationen som du har aktiverat Azure AD Domain Services.
5. Välj en befintlig **resursgruppen** eller skapa en ny.
6. Välj en **plats** där du vill distribuera den nya virtuella datorn.
7. I den **VM listan** anger du namnen på de befintliga virtuella datorerna kan anslutas till den hanterade domänen. Använd kommatecken för att avgränsa enskilda VM-namn. Till exempel **contoso-webbserver, contoso-api**.
8. I **domänanvändarnamn för anslutning till**, ange namnet på användarkontot på hanterade domänen som ska användas för att ansluta den virtuella datorn till den hanterade domänen.
9. I **ansluta Domänanvändarlösenord**, ange lösenordet för det domänanvändarkonto som anges av parametern 'domainUsername'.
10. I **domännamn FQDN**, ange DNS-domännamnet för din hanterade domän.
11. Valfritt: Du kan ange en **OU sökvägen** till en anpassad Organisationsenhet där du vill lägga till den virtuella datorn. Om du inte anger ett värde för den här parametern den virtuella datorn har lagts till i standard **AAD DC datorer** OU på den hanterade domänen.
12. Klicka på **jag samtycker till villkoren som anges ovan**.
13. Klicka på **inköp** att etablera den virtuella datorn.

> [!WARNING]
> **Hantera lösenord med försiktighet.**
> Parametern mallfilen innehåller lösenord för domänkonton samt lokala administratörslösenord för den virtuella datorn. Se till att du lämnar den här filen ligger runt på filresurser eller andra delade platser. Vi rekommenderar att du ta bort den här filen när du är klar distribuera virtuella datorer.
>

När distributionen är klar är de angivna virtuella Windows-datorerna anslutna till den hanterade domänen.


## <a name="related-content"></a>Relaterat innehåll
* [Översikt över Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Azure Snabbkurs mall - domänanslutning en ny virtuell dator](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure Snabbkurs mall - domänanslutning befintliga virtuella datorer](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
