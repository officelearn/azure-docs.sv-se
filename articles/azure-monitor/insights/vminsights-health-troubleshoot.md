---
title: Felsöka Azure Monitor for VMs gäst hälsa (för hands version)
description: Beskriver fel söknings steg som du kan vidta när du har problem med Azure Monitor for VMs hälsa.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 2aa83a861697882127c75eb696807f604c3061f0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184560"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Felsöka Azure Monitor for VMs gäst hälsa (för hands version)
I den här artikeln beskrivs fel söknings steg som du kan vidta när du har problem med Azure Monitor for VMs hälsan.

## <a name="error-message-that-no-data-is-available"></a>Fel meddelande om att inga data är tillgängliga 

![Inga data](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Kontrol lera att den virtuella datorn uppfyller konfigurations kraven

1. Kontrol lera att den virtuella datorn är en virtuell Azure-dator. Azure-bågen för servrar stöds inte för närvarande.
2. Kontrol lera att den virtuella datorn kör ett [operativ system som stöds](vminsights-health-enable.md?current-limitations.md).
3. Kontrol lera att den virtuella datorn är installerad i en [region som stöds](vminsights-health-enable.md?current-limitations.md).
4. Kontrol lera att arbets ytan Log Analytics är installerad i en [region som stöds](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Kontrol lera att den virtuella datorn har registrerats korrekt
Verifiera att Azure Monitor Agent-tillägget och den virtuella gäst datorns hälso agent har allokerats på den virtuella datorn. Välj **tillägg** på den virtuella datorns meny i Azure Portal. Om de två agenterna visas i listan, se []() .

![VM-tillägg](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Kontrol lera att den tilldelade system identiteten är aktive rad på den virtuella datorn
Kontrol lera att systemets tilldelade identitet är aktive rad på den virtuella datorn. Välj **identitet** från den virtuella datorns meny i Azure Portal. Om de två agenterna visas i listan, se []() .

![Tilldelad identitet](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verifiera data insamlings regel
Kontrol lera att data insamlings regeln som anger hälso tillägg som data källa är associerad med den virtuella datorn.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Fel meddelande för felaktig begäran på grund av otillräcklig behörighet
Det här felet indikerar att **Microsoft. WorkloadMonitor** Resource Provider inte registrerades i prenumerationen. Se [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) för information om hur du registrerar resurs leverantören. 

![Felaktig begäran](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Nästa steg

- [Få en översikt över gäst hälso funktionen i Azure Monitor for VMs](vminsights-health-overview.md)