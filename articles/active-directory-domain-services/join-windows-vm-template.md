---
title: Anslut en virtuell Windows Server-dator till Azure Active Directory Domain Services | Microsoft Docs
description: Anslut en virtuell Windows Server-dator till en hanterad domän med hjälp av Azure Resource Manager mallar.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 599d474b7c45274c87878c622149a86bc93af318
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612268"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Ansluta en virtuell Windows Server-dator till en hanterad domän med hjälp av en Resource Manager-mall
Den här artikeln visar hur du ansluter en virtuell Windows Server-dator till en Azure AD Domain Services hanterad domän med hjälp av Resource Manager-mallar.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
För att utföra de uppgifter som anges i den här artikeln behöver du:
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** – antingen synkroniserad med en lokal katalog eller en katalog som endast är molnad.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalogen. Om du inte har gjort det följer du alla uppgifter som beskrivs i Komma igångs [guiden](tutorial-create-instance.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [så här uppdaterar du DNS-inställningar för Azure Virtual Network](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Slutför de steg som krävs för att [Synkronisera lösen ord till din Azure AD Domain Services hanterade domänen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="install-and-configure-required-tools"></a>Installera och konfigurera verktyg som krävs
Du kan använda något av följande alternativ för att utföra stegen som beskrivs i det här dokumentet:
* **Azure PowerShell**: [Installera och konfigurera](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**: [Installera och konfigurera](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Alternativ 1: Etablera en ny virtuell Windows Server-dator och koppla den till en hanterad domän
**namn på snabb start mal len**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Utför följande steg för att distribuera en virtuell Windows Server-dator och ansluta den till en hanterad domän:
1. Navigera till [snabb starts mal len](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Klicka på **Distribuera till Azure**.
3. På sidan **Anpassad distribution** anger du den information som krävs för att etablera den virtuella datorn.
4. Välj den **Azure-prenumeration** som den virtuella datorn ska etableras i. Välj samma Azure-prenumeration där du har aktiverat Azure AD Domain Services.
5. Välj en befintlig **resurs grupp** eller skapa en ny.
6. Välj en **plats** där den nya virtuella datorn ska distribueras.
7. I **befintligt VNet-namn**anger du det virtuella nätverk där du har distribuerat din Azure AD Domain Services hanterade domän.
8. I **befintligt under näts namn**anger du under nätet i det virtuella nätverk som du vill distribuera den virtuella datorn till. Välj inte Gateway-undernätet i det virtuella nätverket. Välj inte heller det dedikerade undernät som din hanterade domän är distribuerad i.
9. I **DNS-etikett-prefixet**anger du värd namnet för den virtuella dator som ska tillhandahållas. Till exempel "contoso-Win".
10. Välj lämplig **VM-storlek** för den virtuella datorn.
11. Ange DNS-domännamnet för den hanterade domänen i **domänen som ska anslutas**.
12. I **domän användar namn**anger du det användar konto namn på din hanterade domän som ska användas för att ansluta den virtuella datorn till den hanterade domänen.
13. I **domän lösen ord**anger du lösen ordet för det domän användar konto som refereras till av parametern "domainUsername".
14. Valfritt: Du kan ange en **OU-sökväg** till en anpassad Organisationsenhet där du kan lägga till den virtuella datorn. Om du inte anger något värde för den här parametern läggs den virtuella datorn till i ou-standardenheten för **AAD** -domänkontrollanten på den hanterade domänen.
15. I fältet **användar namn för virtuell dator administratör** anger du ett lokalt administratörs konto namn för den virtuella datorn.
16. I fältet **lösen ord för administratör för virtuell** dator anger du ett lokalt administratörs lösen ord för den virtuella datorn. Ange ett starkt lokalt administratörs lösen ord för den virtuella datorn för att skydda mot lösen ords intrång.
17. Klicka på **Jag accepterar villkoren som anges ovan**.
18. Klicka på **köp** för att etablera den virtuella datorn.

> [!WARNING]
> **Hantera lösen ord med försiktighet.**
> Mallens parameter fil innehåller lösen ord för domän konton och lösen ord för lokal administratör för den virtuella datorn. Se till att du inte lämnar filen som ligger runt om fil resurser eller andra delade platser. Vi rekommenderar att du avyttrar den här filen när du har distribuerat dina virtuella datorer.
>

När distributionen har slutförts är den nyligen etablerade virtuella Windows-datorn ansluten till den hanterade domänen.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Alternativ 2: Anslut en befintlig virtuell Windows Server-dator till en hanterad domän
**snabb starts mall**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Utför följande steg för att ansluta en befintlig virtuell Windows Server-dator till en hanterad domän:
1. Navigera till [snabb starts mal len](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Klicka på **Distribuera till Azure**.
3. På sidan **Anpassad distribution** anger du den information som krävs för att etablera den virtuella datorn.
4. Välj den **Azure-prenumeration** som den virtuella datorn ska etableras i. Välj samma Azure-prenumeration där du har aktiverat Azure AD Domain Services.
5. Välj en befintlig **resurs grupp** eller skapa en ny.
6. Välj en **plats** där den nya virtuella datorn ska distribueras.
7. I fältet **VM-lista** anger du namnen på de befintliga virtuella datorerna som ska anslutas till den hanterade domänen. Använd ett kommatecken för att avgränsa enskilda VM-namn. Till exempel **contoso-Web, contoso-API**.
8. I **domän anslutningens användar namn**anger du det användar konto namn på din hanterade domän som ska användas för att ansluta den virtuella datorn till den hanterade domänen.
9. I **domän Anslut användar lösen ord**anger du lösen ordet för det domän användar konto som refereras till av parametern "domainUsername".
10. I **domän-FQDN**anger du DNS-domännamnet för din hanterade domän.
11. Valfritt: Du kan ange en **OU-sökväg** till en anpassad Organisationsenhet där du kan lägga till den virtuella datorn. Om du inte anger något värde för den här parametern läggs den virtuella datorn till i ou-standardenheten för **AAD** -domänkontrollanten på den hanterade domänen.
12. Klicka på **Jag accepterar villkoren som anges ovan**.
13. Klicka på **köp** för att etablera den virtuella datorn.

> [!WARNING]
> **Hantera lösen ord med försiktighet.**
> Mallens parameter fil innehåller lösen ord för domän konton och lösen ord för lokal administratör för den virtuella datorn. Se till att du inte lämnar filen som ligger runt om fil resurser eller andra delade platser. Vi rekommenderar att du avyttrar den här filen när du har distribuerat dina virtuella datorer.
>

När distributionen har slutförts är de angivna virtuella Windows-datorerna anslutna till den hanterade domänen.


## <a name="related-content"></a>Relaterat innehåll
* [Översikt över Azure PowerShell](/powershell/azure/overview)
* [Azure snabb starts mall – domän Anslut till en ny virtuell dator](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure snabb starts mall – domän anslutning till befintliga virtuella datorer](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
