---
title: Konfigurera Pacemaker på RHEL i Azure | Microsoft-dokument
description: Konfigurera Pacemaker på Red Hat Enterprise Linux i Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: radeltch
ms.openlocfilehash: 21c551721815847eea4cb1435298ea6f7bf37966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264484"
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

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Läs följande SAP Notes och papers först:

* SAP Note [1928533], som har:
  * Listan över Azure VM-storlekar som stöds för distribution av SAP-programvara.
  * Viktig kapacitetsinformation för Azure VM-storlekar.
  * Sap-programvara och operativsystem (OPERATIVSYSTEM) och databaskombinationer som stöds.
  * Den nödvändiga SAP-kärnversionen för Windows och Linux på Microsoft Azure.
* SAP Note [2015553] listar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure.
* SAP Note [2002167] har rekommenderat OS-inställningar för Red Hat Enterprise Linux
* SAP Note [2009879] har SAP HANA Riktlinjer för Red Hat Enterprise Linux
* SAP Note [2178632] har detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [2191498] har den nödvändiga SAP Host Agent-versionen för Linux i Azure.
* SAP Note [2243692] har information om SAP-licensiering på Linux i Azure.
* SAP Note [1999351] har ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP Notes för Linux.
* [Planering och implementering av Virtuella Azure-datorer för SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [SAP HANA-systemreplikering i pacemakerkluster](https://access.redhat.com/articles/3004101)
* Allmän RHEL-dokumentation
  * [Tilläggsöversikt för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration av tillägg med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Tilläggsreferens för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Supportprinciper för RHEL-kluster med hög tillgänglighet – sbd och fence_sbd](https://access.redhat.com/articles/2800691)
* Azure-specifik RHEL-dokumentation:
  * [Supportprinciper för RHEL-kluster med hög tillgänglighet – Virtuella Microsoft Azure-datorer som klustermedlemmar](https://access.redhat.com/articles/3131341)
  * [Installera och konfigurera ett Red Hat Enterprise Linux 7.4 -kluster med hög tillgänglighet på Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Konfigurera SAP S/4HANA ASCS/ERS med fristående enqueue Server 2 (ENSA2) i Pacemaker på RHEL 7.6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Klusterinstallation

![Översikt över Pacemaker på RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat stöder inte programvara emulerade vakthund. Red Hat stöder inte SBD på molnplattformar. Mer information finns i [Supportprinciper för RHEL-kluster med hög tillgänglighet – sbd och fence_sbd](https://access.redhat.com/articles/2800691).
> Den enda stängselmekanism som stöds för Pacemaker Red Hat Enterprise Linux-kluster på Azure är Azure-stängselagent.  

Följande objekt föregås av antingen **[A]** - som gäller för alla noder, **[1]** - endast gäller för nod 1 eller **[2]** - endast gäller för nod 2.

1. **[A]** Registrera dig

   Registrera dina virtuella datorer och bifoga den till en pool som innehåller databaser för RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Observera att genom att koppla en pool till en Azure Marketplace PAYG RHEL-avbildning kommer du att dubbelknätas för din RHEL-användning: en gång för PAYG-avbildningen och en gång för RHEL-berättigandet i poolen som du ansluter. För att minska detta tillhandahåller Azure nu BYOS RHEL-avbildningar. Mer information finns [här](../redhat/byos.md).

1. **[A]** Aktivera RHEL för SAP-repos

   Aktivera följande databaser för att installera de nödvändiga paketen.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Installera RHEL HA-tillägg

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Vi rekommenderar följande versioner av Azure Fence-agent (eller senare) för kunder att dra nytta av en snabbare redundanstid, om ett resursstopp misslyckas eller klusternoderna inte längre kan kommunicera vilka varandra:  
   > RHEL 7.6: stängsel-agenter-4.2.1-11.el7_6.8  
   > RHEL 7.5: stängsel-agenter-4.0.11-86.el7_5.8  
   > RHEL 7.4: stängsel-agenter-4.0.11-66.el7_4.12  
   > Mer information finns i [Azure VM som körs som en RHEL-klustermedlem med hög tillgänglighet tar mycket lång tid att vara inhägnad, eller stängsel misslyckas / time-out innan den virtuella datorn stängs av](https://access.redhat.com/solutions/3408711).

   Kontrollera versionen av Azure-stängselagenten. Om det behövs uppdaterar du den till en version som är lika med eller senare än vad som anges ovan.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Om du behöver uppdatera Azure Fence-agenten och om du använder anpassad roll, se till att uppdatera den anpassade rollen så att den inkluderar åtgärd **powerOff**. Mer information finns i [Skapa en anpassad roll för stängslet.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent)  

1. **[A]** Namnmatchning för installationsprogrammet

   Du kan antingen använda en DNS-server eller ändra /etc/hosts på alla noder. Det här exemplet visar hur du använder filen /etc/hosts.
   Ersätt IP-adressen och värdnamnet i följande kommandon. Fördelen med att använda /etc/hosts är att klustret blir oberoende av DNS, vilket kan vara en enda felpunkt också.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader till /etc/hosts. Ändra IP-adressen och värdnamnet så att de matchar din miljö

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Ändra hacluster-lösenord till samma lösenord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Lägga till brandväggsregler för pacemaker

   Lägg till följande brandväggsregler i all klusterkommunikation mellan klusternoderna.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Aktivera grundläggande klustertjänster

   Kör följande kommandon för att aktivera pacemakertjänsten och starta den.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Skapa pacemakerkluster

   Kör följande kommandon för att autentisera noderna och skapa klustret. Ställ in token på 30000 så att minne bevarar underhåll. Mer information finns i [den här artikeln för Linux][virtual-machines-linux-maintenance].

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

1. **[A]** Ange förväntade röster

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Skapa STONITH-enhet

STONITH-enheten använder en tjänstansvarig för att auktorisera mot Microsoft Azure. Följ dessa steg för att skapa en tjänsthuvudnamn.

1. Gå till <https://portal.azure.com>
1. Öppna Azure Active Directory-bladet  
   Gå till Egenskaper och skriv ned katalog-ID. Det här är **klient-ID**.
1. Klicka på Appregistreringar
1. Klicka på Ny registrering
1. Ange ett namn, välj "Konton i den här organisationskatalogen" 
2. Välj Programtyp "Webb", ange en inloggnings-URL\/(till exempel http: /localhost) och klicka på Lägg till  
   Inloggnings-URL:en används inte och kan vara en giltig WEBBADRESS
1. Välj Certifikat och hemligheter och klicka sedan på Ny klienthemlighet
1. Ange en beskrivning för en ny nyckel, välj "Aldrig upphör att gälla" och klicka på Lägg till
1. Skriv ned värdet. Den används som **lösenord** för servicehuvudmannen
1. Välj Översikt. Skriv ner program-ID: t. Det används som användarnamn **(inloggnings-ID** i stegen nedan) av Service Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Skapa en anpassad roll för stängslet agent

Tjänsthuvudgivaren har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvudnamn behörighet att starta och stoppa (stänga av) alla virtuella datorer i klustret. Om du inte redan har skapat den anpassade rollen kan du skapa den med [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) eller [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Använd följande innehåll för indatafilen. Du måste anpassa innehållet till dina prenumerationer som är, ersätta c276fc76-9cd4-44c9-99a7-4fd71546436e och e91d47c4-76f3-4271-a796-21b4ecfe3624 med IDs av din prenumeration. Om du bara har en prenumeration tar du bort den andra posten i AssignableScopes.

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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Tilldela den anpassade rollen till tjänsthuvudgivaren

Tilldela den anpassade rollen "Linux Fence Agent Role" som skapades i det sista kapitlet till tjänsthuvudstadshuvudmannen. Använd inte ägarrollen längre!

1. Gå till https://portal.azure.com
1. Öppna bladet Alla resurser
1. Välj den virtuella datorn för den första klusternoden
1. Klicka på Åtkomstkontroll (IAM)
1. Klicka på Lägg till rolltilldelning
1. Välj rollen "Linux Fence Agent Role"
1. Ange namnet på programmet som du skapade ovan
1. Klicka på Spara

Upprepa stegen ovan för den andra klusternoden.

### <a name="1-create-the-stonith-devices"></a>**[1]** Skapa STONITH-enheterna

När du har redigerat behörigheterna för de virtuella datorerna kan du konfigurera STONITH-enheterna i klustret.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Använd följande kommando för att konfigurera stängselenheten.

> [!NOTE]
> Alternativet "pcmk_host_map" krävs ENDAST i kommandot, om RHEL-värdnamnen och Azure-nodnamnen INTE är identiska. Se det fetstilt avsnittet i kommandot.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Aktivera användning av en STONITH-enhet

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Nästa steg

* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (virtuella datorer)][sap-hana-ha]
