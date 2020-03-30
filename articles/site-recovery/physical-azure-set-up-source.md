---
title: Konfigurera konfigurationsservern för haveriberedskap av fysiska servrar till Azure med Azure Site Recovery | Microsoft Dokument"
description: I den här artikeln beskrivs hur du konfigurerar den lokala konfigurationsservern för haveriberedskap av lokala fysiska servrar till Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257880"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Konfigurera konfigurationsservern för haveriberedskap av fysiska servrar till Azure

I den här artikeln beskrivs hur du konfigurerar din lokala miljö för att börja replikera fysiska servrar som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Krav

Artikeln förutsätter att du redan har:
- Ett Recovery Services-valv i [Azure-portalen](https://portal.azure.com "Azure Portal").
- En fysisk dator där konfigurationsservern kan installeras.
- Om du har inaktiverat TLS 1.0 på datorn där du installerar konfigurationsservern kontrollerar du att TLs 1.2 är aktiverat och att .NET Framework version 4.6 eller senare är installerad på datorn (med stark kryptografi aktiverad). [Läs mer](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimikrav på konfigurationsserver
I följande tabell visas minimikraven för maskinvara, programvara och nätverk för en konfigurationsserver.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-baserade proxyservrar stöds inte av konfigurationsservern.

## <a name="choose-your-protection-goals"></a>Välj dina skyddsmål

1. Gå till valvbladet **för Återställningstjänster** i Azure-portalen och välj ditt valv.
2. Klicka på **Komma igång** > **site recovery** > **steg 1** > på resursmenyn i valvet. **Resource** **Protection goal**

    ![Välja mål](./media/physical-azure-set-up-source/choose-goals.png)
3. I **Skyddsmål**väljer du **Till Azure** och inte **virtualiserad/Annan**och klickar sedan på **OK**.

    ![Välja mål](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

1. Om du inte har en konfigurationsserver i **Förbered källa**klickar du på **+Konfigurationsserver** för att lägga till en.

   ![Konfigurera källan](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Kontrollera att **Configuration Server** visas i **servertypen**i bladet Lägg **till server.**
4. Hämta installationsfilen för installationsprogrammet för enhetlig installation för webbplatsåterställning.
5. Ladda ned valvregistreringsnyckeln. Du behöver registreringsnyckeln när du kör Enhetlig installation. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/physical-azure-set-up-source/set-source2.png)
6. På den dator som du använder som konfigurationsserver kör du **Azure Site Recovery Unified Setup** för att installera konfigurationsservern, processservern och huvudmålservern.

#### <a name="run-azure-site-recovery-unified-setup"></a>Köra enhetlig installation för enhetlig Azure-webbplatsåterställning

> [!TIP]
> Registrering av konfigurationsserver misslyckas om tiden på datorns systemklocka är mer än fem minuter bort från lokal tid. Synkronisera systemklockan med en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) innan du startar installationen.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurationsservern kan installeras via en kommandorad. [Läs mer](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Vanliga problem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nästa steg

Nästa steg innebär [att konfigurera din målmiljö](physical-azure-set-up-target.md) i Azure.
