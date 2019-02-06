---
title: Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure | Microsoft Docs
description: Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure
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
ms.openlocfilehash: 6001681376011d03f7417d2099c0765e1a6cb5ae
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750126"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance

> [!NOTE]
> Pacemaker på Red Hat Enterprise Linux använder Azure avgränsningstecken agenten för att fence en klusternod om det behövs. En redundansväxling kan ta upp till 15 minuter om det inte går att stoppa en resurs eller noderna i klustret inte kan kommunicera som varandra längre. Mer information finns [virtuell Azure-dator som körs som en hög tillgänglighet för RHEL-klustermedlem ta mycket lång tid att vara inhägnade eller hägna in misslyckas / timeout innan den virtuella datorn stängs av](https://access.redhat.com/solutions/3408711)

Läs följande SAP Notes och papers först:

* SAP-kommentar [1928533], som har:
  * Lista över Azure VM-storlekar som stöds för distribution av SAP-program.
  * Viktiga kapacitetsinformation för Azure VM-storlekar.
  * SAP-program som stöds, och operativsystem (OS) och kombinationer av databasen.
  * Den obligatoriska SAP kernel-versionen för Windows och Linux på Microsoft Azure.
* SAP-kommentar [2015553] visar en lista över kraven för distribution av SAP-stöd för SAP-programvara i Azure.
* SAP-kommentar [2002167] rekommenderar OS-inställningar för Red Hat Enterprise Linux
* SAP-kommentar [2009879] har SAP HANA riktlinjer för Red Hat Enterprise Linux
* SAP-kommentar [2178632] mer information om all övervakning mått som rapporterats för SAP i Azure.
* SAP-kommentar [2191498] har Värdagenten för SAP-version som krävs för Linux i Azure.
* SAP-kommentar [2243692] har licensieringsinformation SAP på Linux i Azure.
* SAP-kommentar [1999351] innehåller ytterligare felsökningsinformation för Azure förbättrad övervakning av tillägget för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Azure virtuella datorer, planering och implementering av SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP HANA-systemreplikering på pacemaker kluster](https://access.redhat.com/articles/3004101)
* Allmänna RHEL-dokumentation
  * [Översikt över tillägg för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Hög tillgänglighet tillägg Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referens för tillägg för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure specifika RHEL-dokumentationen:
  * [Stöd för principer för RHEL-kluster för hög tillgänglighet – Microsoft Azure-datorer som medlemmar i ett kluster](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera en Red Hat Enterprise Linux 7.4 (och senare) hög tillgänglighet-kluster på Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="cluster-installation"></a>Klusterinstallationen

![Pacemaker på RHEL-översikt](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Följande objekt har prefixet antingen **[A]** – gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Registrera

   Registrera dina virtuella datorer och koppla den till en pool som innehåller databaser för RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]**  Aktivera RHEL for SAP-lagringsplatser

   Aktivera följande databaser för att installera de nödvändiga paketen.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable="rhel-sap-for-rhel-7-server-rpms"
   </code></pre>

1. **[A]**  Installera RHEL-tillägg för hög tillgänglighet

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

1. **[A]**  Konfigurera matcha värdnamn

   Du kan använda en DNS-server, eller så kan du ändra i/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon. Fördelen med att använda/etc/hosts är att klustret blir oberoende av DNS som kan vara en enda åtkomstpunkt för fel för.

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

1. **[A]**  Lägga till brandväggsregler för pacemaker

   Lägg till följande brandväggsregler för alla klusterkommunikation mellan klusternoderna.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]**  Aktivera grundläggande klustertjänster

   Kör följande kommandon för att aktivera tjänsten Pacemaker och starta den.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]**  Skapa Pacemaker kluster

   Kör följande kommandon för att autentisera noderna och skapa klustret. Ange token 30000 att tillåta minne bevarande underhåll. Mer information finns i [i den här artikeln för Linux][virtual-machines-linux-maintenance].

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

1. **[A]**  Ange förväntade röster

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Skapa STONITH enhet

STONITH enheten använder ett huvudnamn för tjänsten för att godkänna mot Microsoft Azure. Följ dessa steg om du vill skapa ett huvudnamn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaper och anteckna Directory-ID. Det här är den **klient-ID**.
1. Klicka på App-registreringar
1. Klicka på Lägg till
1. Ange ett namn, väljer typen ”Web app/API”, ange en inloggnings-URL (till exempel http://localhost) och klicka på Skapa
1. Inloggnings-URL: en används inte och kan vara vilken giltig URL
1. Välj den nya appen och klicka på nycklar på fliken Inställningar
1. Ange en beskrivning för en ny nyckel, Välj ”upphör aldrig att gälla” och klicka på Spara
1. Anteckna värdet. Den används som den **lösenord** för tjänstens huvudnamn
1. Anteckna programmets ID. Den används som användarnamnet (**inloggnings-ID** i stegen nedan) för tjänstens huvudnamn

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Skapa en anpassad roll för agenten avgränsningstecken

Tjänstens huvudnamn har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvudnamn behörigheter att starta och stoppa (frigöra) alla virtuella datorer i klustret. Om du inte redan har skapat den anpassade rollen, kan du skapa den med hjälp av [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) eller [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Använd följande innehåll för indatafilen. Du måste anpassa innehåll till dina prenumerationer som är, Ersätt c276fc76-9cd4-44c9-99a7-4fd71546436e och e91d47c4-76f3-4271-a796-21b4ecfe3624 med ID: N för din prenumeration. Om du bara har en prenumeration kan du ta bort den andra posten i AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
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

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Och tilldela den anpassade rollen till tjänstens huvudnamn

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

Använd följande kommando för att konfigurera avgränsningstecken enheten.

> [!NOTE]
> Alternativet ”pcmk_host_map' krävs endast i det här kommandot om RHEL-värdnamn och Azure-nod-namn inte är identiska. Referera till avsnittet fet stil i kommandot.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Aktivera användning av en enhet med STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Nästa steg

* [Azure virtuella datorer, planering och implementering av SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure Virtual Machines i [hög tillgänglighet för SAP HANA på Azure Virtual Machines (VM)][sap-hana-ha]
