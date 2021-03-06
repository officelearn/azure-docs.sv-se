---
title: Återställa från ett Azure Stack Edge Pro-enhets fel
description: Beskriver hur du återställer från en Azure Stack Edge Pro-enhet som misslyckades.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: bf4d0a845b7f26c82ba3940d6613a33bcacf9187
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448335"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Återställa från en misslyckad Azure Stack Edge Pro GPU-enhet 

Den här artikeln beskriver hur du återställer från ett icke-allvarligt fel på din Azure Stack Edge Pro GPU-enhet. Ett icke-kritiskt problem på Azure Stack Edge-GPU-enhet kräver en enhets ersättning.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har:

- Kontaktade Microsoft Support om enhets felen och att de har rekommenderat en enhets ersättning. 
- Säkerhetskopierade enhets konfigurationen enligt beskrivningen i [förbereda för ett enhets haveri](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Konfigurera ersättnings enhet

När enheten stöter på ett icke-värde som inte är tillåtna måste du beställa en ersättnings enhet. Konfigurations stegen för ersättnings enheten förblir desamma. 

Hämta enhetens konfigurations information som du säkerhetskopierade från enheten som misslyckades. Den här informationen används för att konfigurera ersättnings enheten.  

Följ dessa steg för att konfigurera ersättnings enheten:

1. Samla in den information som krävs i [Check listan för distribution](azure-stack-edge-gpu-deploy-checklist.md). Du bör använda den information som du sparade från den tidigare enhets konfigurationen. 
1. Beställ en ny enhet med samma konfiguration som den som misslyckades.  Om du vill placera en order [skapar du en ny Azure Stack Edge-resurs](azure-stack-edge-gpu-deploy-prep.md#) i Azure Portal.
1. [Packa upp](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [rack montering](azure-stack-edge-gpu-deploy-install.md#rack-the-device) och [kabel enheten](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Anslut till enhetens lokala användar gränssnitt](azure-stack-edge-gpu-deploy-connect.md).
1. Konfigurera nätverket med samma IP-adresser som du använde för din gamla enhet. Detta minimerar påverkan på alla klient datorer som används i din miljö. Se [Konfigurera nätverks inställningar](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Tilldela samma enhets namn och DNS-domän som din gamla enhet. Detta säkerställer att klienterna kan använda samma enhets namn för att kommunicera med den nya enheten. Se [Konfigurera enhets inställningar](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Konfigurera certifikat på den nya enheten på samma sätt som du gjorde för den gamla enheten. Tänk på att den nya enheten har ett nytt serie nummer för noden. Om du använde dina egna certifikat på den gamla enheten måste du skaffa ett nytt nodnamn. Se så här [konfigurerar du certifikat](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Hämta aktiverings nyckeln från Azure Portal och aktivera den nya enheten. Se hur du [aktiverar enheten](azure-stack-edge-gpu-deploy-activate.md).

Du är nu redo att distribuera de arbets belastningar som du körde på den gamla enheten.

## <a name="restore-edge-cloud-shares"></a>Återställa gräns moln resurser

Följ dessa steg om du vill återställa data på gräns moln resurser på din enhet:

1. [Lägg till resurser](azure-stack-edge-j-series-manage-shares.md#add-a-share) med samma resurs namn som skapades tidigare på den felande enheten. Se till att när du skapar resurser, **väljer du Blob container** är inställt på **Använd befintligt** och väljer sedan den behållare som användes med den tidigare enheten.
1. [Lägg till användare](azure-stack-edge-j-series-manage-users.md#add-a-user) som hade åtkomst till den tidigare enheten.
1. [Lägg till lagrings konton](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account) som är kopplade till resurserna som tidigare fanns på enheten. När du skapar gräns lagrings konton väljer du från en befintlig behållare och pekar på den behållare som har mappats till det Azure Storage konto som har mappats till den tidigare enheten. Alla data från enheten som skrevs till gräns lagrings kontot på den tidigare enheten laddades upp till den valda lagrings behållaren i det mappade Azure Storage kontot.
1. [Uppdatera dela](azure-stack-edge-j-series-manage-shares.md#refresh-shares) data från Azure. Detta skulle hämta alla moln data från den befintliga behållaren till resurserna.

## <a name="restore-edge-local-shares"></a>Återställa Edge-lokala resurser

För att förbereda för ett potentiellt enhets haveri kan du ha distribuerat följande säkerhets kopierings lösningar för att skydda lokala resurser data från dina Kubernetes-eller IoT-arbetsbelastningar:

| Tredjepartsprogram           | Referens till lösningen                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Kontakta Cohesity om du vill ha mer information.          |
| CommVault                      | https://www.commvault.com/azure <br> Kontakta CommVault om du vill ha mer information. |
| Veritas                        | http://veritas.com/azure <br> Kontakta Veritas om du vill ha mer information.   |

När ersättnings enheten är helt konfigurerad aktiverar du enheten för lokal lagring. 

Följ dessa steg om du vill återställa data från lokala resurser: 

1. [Konfigurera beräkning på enheten](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Lägg till en lokal resurs](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) tillbaka.
1. Kör återställnings proceduren som tillhandahålls av den data skydds lösning som du väljer. Se referenser från föregående tabell.

## <a name="restore-vm-files-and-folders"></a>Återställa VM-filer och mappar

För att förbereda för ett potentiellt enhets haveri kan du ha distribuerat någon av följande säkerhets kopierings lösningar för att skydda data på virtuella datorer:



| Säkerhets kopierings lösningar        | Operativ system som stöds   | Referens                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services MARS-agenten för Azure Backup | Windows        | [Om MARS-agent](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Microsoft Azure integrering, lösning för säkerhets kopiering och återställning kort](https://www.cohesity.com/solution/cloud/azure) <br>Kontakta Cohesity om du vill ha mer information.                          |
| CommVault               | Windows, Linux | https://www.commvault.com/azure <br> Kontakta CommVault om du vill ha mer information.
| Veritas                 | Windows, Linux | http://veritas.com/azure <br> Kontakta Veritas om du vill ha mer information.                    |

När den nya enheten har kon figurer ATS fullständigt kan du distribuera om de virtuella datorerna med den virtuella dator avbildningen som användes tidigare. 

Följ de här stegen för att återställa data i de virtuella datorerna:
 
1. [Distribuera en virtuell dator från en VM-avbildning](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) på enheten. 
1. Installera data skydds lösningen som du väljer på den virtuella datorn.
1. Kör återställnings proceduren som tillhandahålls av den data skydds lösning som du väljer. Se referenser från föregående tabell.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [returnerar en Azure Stack Edge Pro-enhet](azure-stack-edge-return-device.md).