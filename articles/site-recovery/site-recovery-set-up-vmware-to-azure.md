---
title: "Konfigurera källmiljön (VMware till Azure) | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställer in din lokala miljö för att starta replikera virtuella VMware-datorer till Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 32a3f7498d3c8891178818436e33221f91ae2f8f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Konfigurera källmiljön (VMware till Azure)
> [!div class="op_single_selector"]
> * [VMware till Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fysiska till Azure](./site-recovery-set-up-physical-to-azure.md)

Den här artikeln beskriver hur du ställer in din lokala miljö för att starta replikering av virtuella datorer som körs på VMware till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter att du redan har skapat:
- Recovery Services-ventilen i den [Azure-portalen](http://portal.azure.com "Azure-portalen").
- Ett särskilt konto i din VMware vCenter som kan användas för [automatisk identifiering](./site-recovery-vmware-to-azure.md).
- En virtuell dator som du vill installera konfigurationsservern.

## <a name="configuration-server-minimum-requirements"></a>Minimikrav för konfiguration av servern
I följande tabell visar minimikrav för maskinvara, programvara och nätverkskraven för en konfigurationsserver.

> [!IMPORTANT]
> När du distribuerar en konfigurationsserver för att skydda virtuella VMware-datorer, rekommenderar vi att du distribuerar den som en **hög tillgänglighet (HA)** virtuella datorn.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-baserade proxyservrar stöds inte av konfigurationsservern.

## <a name="choose-your-protection-goals"></a>Välja skyddsmål

1. I Azure-portalen går du till den **återställningstjänster** valvet bladet och välj ditt valv.
2. På menyn resurs på valvet, gå till **komma igång** > **Site Recovery** > **steg 1: Förbered infrastrukturen**  >  **Skyddsmål**.

    ![Välja mål](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. I **skyddsmål**väljer **till Azure**, och välj **Ja, med VMware vSphere-Hypervisor**. Klicka sedan på **OK**.

    ![Välja mål](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön
Konfigurera källmiljön omfattar två huvudsakliga aktiviteter:

- Installera och registrera en konfigurationsserver med Site Recovery.
- Identifiera dina lokala virtuella datorer genom att ansluta Site Recovery till din lokala VMware vCenter eller vSphere EXSi värdar.

### <a name="step-1-install-and-register-a-configuration-server"></a>Steg 1: Installera och registrera en konfigurationsserver

1. Klicka på **steg 1: förbereda infrastrukturen** > **källa**. I **Förbered källa**, om du inte har en konfigurationsservern och klicka på **+ konfigurationsservern** att lägga till ett.

    ![Konfigurera källan](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. På den **Lägg till Server** bladet, kontrollera att **konfigurationsservern** visas i **servertyp**.
4. Hämta installationsfilen för enhetlig installationsprogram för Site Recovery.
5. Ladda ned valvregistreringsnyckeln. När du kör installationsprogrammet för enhetlig måste nyckeln för tjänstregistrering. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. På den dator som du använder som konfigurationsservern kör **Unified installationsprogram för Azure Site Recovery** att installera konfigurationsservern, processervern och huvudmålservern.

#### <a name="run-azure-site-recovery-unified-setup"></a>Kör Azure Site Recovery enhetlig installation

> [!TIP]
> Registrera för konfiguration av servern misslyckas om datorns system klocka skiljer sig från lokal tid med fler än fem minuter. Synkronisera systemklockan med en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) innan du påbörjar installationen.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurationsservern kan installeras via kommandoraden. Mer information finns i [installera konfigurationsservern med hjälp av kommandoradsverktyg](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Lägg till VMware-konto för automatisk identifiering

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>Steg 2: Lägg till en vCenter
Du måste ansluta VMware vCenter-servern eller vSphere ESXi-värdar med Site Recovery för att tillåta Azure Site Recovery att identifiera virtuella datorer som körs i din lokala miljö.

Välj **+ vCenter** att starta ansluter en VMware vCenter-server eller en VMware vSphere ESXi-värd.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg
[Konfigurera målmiljön](./site-recovery-prepare-target-vmware-to-azure.md) i Azure.
