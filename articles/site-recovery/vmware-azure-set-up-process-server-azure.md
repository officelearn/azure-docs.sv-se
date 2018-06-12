---
title: Ställ in en processerver i Azure för VM VMware och fysiska servrar återställning med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in en processerver i Azure, återställa virtuella Azure-datorer för VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 1a6a1276d1a63971fba68dc868aa56ce687a6cc2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300930"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Konfigurera en processerver i Azure för återställning efter fel

När du växlar över virtuella VMware-datorer eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md), du kan växla tillbaka dem till den lokala platsen när den är igång igen. Du måste konfigurera en tillfällig processerver i Azure, för att hantera replikering från Azure till lokala för att växla tillbaka. Du kan ta bort den här virtuella datorn när återställning har slutförts.

## <a name="before-you-start"></a>Innan du börjar

Lär dig mer om den [återaktivera skydd](vmware-azure-reprotect.md) och [återställning](vmware-azure-failback.md) process.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

1. I valvet > **Site Recovery-infrastruktur**> **hantera** > **Konfigurationsservrar**, Välj konfigurationsservern.
2. Klicka på sidan **+ processervern**
3. I **Lägg till processerver** sida och välj om du vill distribuera processerver i Azure.
4. Ange inställningarna för Azure, inklusive prenumerationen används för växling vid fel, en resursgrupp, Azure-regionen används för växling vid fel och det virtuella nätverk som virtuella Azure-datorer finns. Om du använder flera Azure-nätverk, måste en processerver i var och en.

  ![Lägg till galleriobjektet för processervern](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. I **servernamn**, **användarnamn**, och **lösenord**, ange ett namn för processervern och autentiseringsuppgifter som ska tilldelas administratörsbehörigheter på servern.
5. Ange ett lagringskonto som ska användas för server Virtuella diskar och undernätet processervern VM kommer att finnas serverns IP-adress som ska tilldelas när den virtuella datorn startar.
6. Klicka på **OK** knappen för att börja distribuera processervern VM.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrera processervern (som körs i Azure) till en Server för konfiguration (som körs lokalt)

När processervern VM är aktiv och körs, måste du registrera den med konfigurationsservern lokalt på följande sätt:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


