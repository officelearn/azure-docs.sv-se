---
title: "Ställ in en processerver i Azure för VM VMware och fysiska servrar återställning med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställer in en processerver i Azure, återställa virtuella Azure-datorer för VMware."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 9d9270d8c6d2ffc5e42dfc6f94818fdace89bfb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurera en processerver i Azure för återställning efter fel

När du växlar över virtuella VMware-datorer eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md), du kan växla tillbaka dem till den lokala platsen när den är igång igen. Du måste konfigurera en tillfällig processerver i Azure, för att hantera replikering från Azure till lokala för att växla tillbaka. Du kan ta bort den här virtuella datorn när återställning har slutförts.

## <a name="before-you-start"></a>Innan du börjar

Lär dig mer om den [återaktivera skydd](vmware-azure-reprotect.md) och [återställning](vmware-azure-failback.md) process.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

1. I valvet > **Site Recovery-infrastruktur**> **Mnaage** > **Konfigurationsservrar**, Välj konfigurationsservern.
2. Klicka på sidan **+ processervern**
3. I **Lägg till processerver** sida och välj om du vill distribuera processerver i Azure.
4. Ange inställningarna för Azure, inklusive prenumerationen används för växling vid fel, en resursgrupp, Azure-regionen används för växling vid fel och det virtuella nätverk som virtuella Azure-datorer finns. Om du använder flera Azure-nätverk, måste en processerver i var och en.
5. I **servernamn**, **användarnamn**, och **lösenord**, ange ett namn för processervern och autentiseringsuppgifter som ska tilldelas administratörsbehörigheter på servern.
6. Ange ett lagringskonto som ska användas för server Virtuella diskar och undernätet processervern VM kommer att finnas serverns IP-adress som ska tilldelas när den virtuella datorn startar.
7. Klicka på **OK** knappen för att börja distribuera processervern VM.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrera processervern (som körs i Azure) till en Server för konfiguration (som körs lokalt)

När processervern VM är aktiv och körs, måste du registrera den med konfigurationsservern lokalt på följande sätt:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


