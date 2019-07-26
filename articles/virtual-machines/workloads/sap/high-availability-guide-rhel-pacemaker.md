---
title: Konfigurera pacemaker på Red Hat Enterprise Linux i Azure | Microsoft Docs
description: Konfigurera pacemaker på Red Hat Enterprise Linux i Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: cd56df8e88a84f52933e5ee43fecd598252ba6c0
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479760"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Konfigurera pacemaker på Red Hat Enterprise Linux i Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot

> [!TIP]
> Pacemaker på Red Hat Enterprise Linux använder Azure-stängsel-agenten för att stängsela en klusternod vid behov. En ny version av Azure-stängsel-agenten är tillgänglig och redundansväxlingen tar inte längre tid, om en resurs slutar fungera eller om klusternoderna inte kan kommunicera med varandra längre. Mer information finns i den [virtuella Azure-datorn som körs som en RHEL hög tillgänglighets kluster medlem tar lång tid att begränsas, eller staket/timeout innan den virtuella datorn stängs av](https://access.redhat.com/solutions/3408711)

Läs följande SAP-anteckningar och dokument först:

* SAP anmärkning [1928533], som har:
  * Listan med virtuella Azure-storlekar som stöds för distribution av SAP-program.
  * Viktig kapacitets information för storlekar på virtuella Azure-datorer.
  * De SAP-program och-operativ system och databas kombinationer som stöds.
  * Den nödvändiga SAP kernel-versionen för Windows och Linux på Microsoft Azure.
* SAP NOTE [2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2002167] har rekommenderade OS-inställningar för Red Hat Enterprise Linux
* SAP NOTE [2009879] har SAP HANA rikt linjer för Red Hat Enterprise Linux
* SAP NOTE [2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP NOTE [2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP anmärkning [1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP HANA system replikering i pacemaker-kluster](https://access.redhat.com/articles/3004101)
* Allmän dokumentation om RHEL
  * [Översikt över hög tillgänglighets tillägg](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administrations tillägg med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referens för hög tillgänglighets tillägg](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure-speciell RHEL-dokumentation:
  * [Support principer för RHEL-kluster med hög tillgänglighet – Microsoft Azure Virtual Machines som kluster medlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera ett kluster med hög tillgänglighet för Red Hat Enterprise Linux 7,4 (och senare) på Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Konfigurera SAP S/4HANA ASCS/ERS med fristående server 2 (ENSA2) i pacemaker på RHEL 7,6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Klusterinstallationen

![Pacemaker på RHEL-översikt](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Följande objekt har prefixet antingen **[A]** – gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]** -register

   Registrera dina virtuella datorer och koppla dem till en pool som innehåller databaser för RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Observera att genom att koppla en pool till en Azure Marketplace PAYG RHEL-avbildning, kommer du i praktiken att faktureras för din RHEL-användning: en gång för PAYG-avbildningen och en gång för RHEL-rättigheterna i poolen som du ansluter. För att minimera detta tillhandahåller Azure nu BYOS RHEL-avbildningar. Mer information finns [här](https://aka.ms/rhel-byos).

1. **[A]** aktivera RHEL för SAP databaser

   Aktivera följande databaser för att installera de nödvändiga paketen.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** installera RHEL ha-tillägg

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Vi rekommenderar följande versioner av Azure stängsel-agenten (eller senare) för att kunderna ska kunna dra nytta av en snabbare redundansväxling, om en resurs slutar att fungera eller om klusternoderna inte kan kommunicera med varandra längre:  
   > RHEL 7,6: Fence-agents-4.2.1 -11. el7 _ 6,8  
   > RHEL 7,5: Fence-agents-4.0.11 -86. el7 _ 5,8 tum  
   > RHEL 7,4: Fence-agents-4.0.11 -66. el7 _ 4.12  
   > Mer information finns i den [virtuella Azure-datorn som körs som en RHEL hög tillgänglighets kluster medlem tar lång tid att begränsas, eller staket/timeout innan den virtuella datorn stängs av](https://access.redhat.com/solutions/3408711)

   Kontrol lera versionen av Azure-stängsel-agenten. Vid behov kan du uppdatera den till en version som är lika med eller senare än den som anges ovan.
   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

1. **[A]**  Konfigurera matcha värdnamn

   Du kan använda en DNS-server, eller så kan du ändra i/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon. Fördelen med att använda/etc/hosts är att klustret blir oberoende av DNS, vilket kan vara en enda åtkomstpunkt för fel för.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet till matchar din miljö

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Ändra hacluster lösenord till samma lösenord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Lägg till brand Väggs regler för pacemaker

   Lägg till följande brand Väggs regler till all kluster kommunikation mellan klusternoderna.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** aktivera grundläggande kluster tjänster

   Kör följande kommandon för att aktivera pacemaker-tjänsten och starta den.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** skapa pacemaker-kluster

   Kör följande kommandon för att autentisera noderna och skapa klustret. Ange token till 30000 för att tillåta underhåll av minnes bevaran. Mer information finns i [den här artikeln för Linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** ange förväntade röster

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Skapa STONITH-enhet

STONITH enheten använder ett huvudnamn för tjänsten för att godkänna mot Microsoft Azure. Följ dessa steg om du vill skapa ett huvudnamn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaper och anteckna Directory-ID. Det här är den **klient-ID**.
1. Klicka på App-registreringar
1. Klicka på ny registrering
1. Ange ett namn, välj "konton endast i den här organisations katalogen" 
2. Välj program typ "Web", ange en inloggnings-URL (till exempel http:\//localhost) och klicka på Lägg till  
   Inloggnings-URL: en används inte och kan vara vilken giltig URL
1. Välj certifikat och hemligheter och klicka sedan på ny klient hemlighet
1. Ange en beskrivning för en ny nyckel, välj "upphör aldrig" och klicka på Lägg till
1. Anteckna värdet. Den används som den **lösenord** för tjänstens huvudnamn
1. Välj översikt. Anteckna programmets ID. Den används som användarnamnet (**inloggnings-ID** i stegen nedan) för tjänstens huvudnamn

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Skapa en anpassad roll för agenten avgränsningstecken

Tjänstens huvudnamn har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvud namn behörighet att starta och stoppa (stänga av) alla virtuella datorer i klustret. Om du inte redan har skapat den anpassade rollen, kan du skapa den med hjälp av [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) eller [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Använd följande innehåll för indatafilen. Du måste anpassa innehåll till dina prenumerationer som är, Ersätt c276fc76-9cd4-44c9-99a7-4fd71546436e och e91d47c4-76f3-4271-a796-21b4ecfe3624 med ID: N för din prenumeration. Om du bara har en prenumeration kan du ta bort den andra posten i AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** tilldela den anpassade rollen till tjänstens huvud namn

Tilldela den anpassade rollen ”Linux avgränsningstecken agenten roll” som har skapats i det senaste kapitlet till tjänstens huvudnamn. Använd inte ägarrollen längre!

1. Gå till https://portal.azure.com
1. Öppna bladet alla resurser
1. Välj den virtuella datorn från den första noden i klustret
1. Klicka på åtkomstkontroll (IAM)
1. Klicka på Lägg till rolltilldelning
1. Välj roll ”Linux avgränsningstecken agenten roll”
1. Ange namnet på programmet som du skapade ovan
1. Klicka på Spara

Upprepa stegen ovan för den andra noden i klustret.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Skapa STONITH-enheter

När du har redigerat behörigheterna för de virtuella datorerna kan du konfigurera STONITH-enheter i klustret.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Använd följande kommando för att konfigurera avgränsnings enheten.

> [!NOTE]
> Alternativet pcmk_host_map krävs endast i kommandot om RHEL-värdnamn och Azure-nodnamn inte är identiska. Se avsnittet fet i kommandot.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Aktivera användning av en STONITH-enhet

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]
