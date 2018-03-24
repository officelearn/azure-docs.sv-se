---
title: Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure | Microsoft Docs
description: Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure
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
ms.date: 02/05/2018
ms.author: sedusch
ms.openlocfilehash: 27fa58042b1d3dbed111d6ec7f3b3e96a9161180
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

![Pacemaker på SLES översikt](./media/high-availability-guide-suse-pacemaker/pacemaker.png)


Det finns två alternativ för att konfigurera ett Pacemaker kluster i Azure. Du kan antingen använda en avgränsningar-agent som tar hand om att starta om noden via Azure-API: erna eller du kan använda en enhet uppstår.

Uppstår enheten kräver en ytterligare virtuell dator som fungerar som en iSCSI-målservern och ger en uppstår enhet. ISCSI-målservern kan dock vara delas med andra Pacemaker kluster. Fördelen med att använda en uppstår-enhet är en snabbare redundans och om du använder uppstår enheter lokalt, kräver inte några ändringar på hur du använder pacemaker klustret. Uppstår avgränsningar kan fortfarande använda Azure avgränsningstecken agenten som en säkerhetskopia inhägnad mekanism om iSCSI-målservern inte är tillgänglig.

Du kan också använda Azure avgränsningstecken agenten om du inte vill att investera i ytterligare en virtuell dator. Nackdelen är att en växling vid fel kan ta mellan 10 – 15 minuter om en resurs stoppa misslyckas eller klusternoder kan inte kommunicera som varandra längre.

## <a name="sbd-fencing"></a>Uppstår avgränsningar

Följ dessa steg om du vill använda en uppstår enhet för avgränsningar.

### <a name="set-up-an-iscsi-target-server"></a>Ställ in ett iSCSI-målservern

Du måste först skapa en iSCSI-målet virtuell dator om du inte har något redan. iSCSI-målservrar kan delas med flera Pacemaker kluster.

1. Distribuera en ny SLES 12 SP1 eller högre virtuell dator och ansluta till datorn via ssh. Datorn behöver inte vara stora. Storlek på en virtuell dator som Standard_E2s_v3 eller Standard_D2s_v3 är tillräckligt.

1. Uppdatera SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. Installera iSCSI-mål-paket

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Aktivera iSCSI-Måltjänsten

   <pre><code>
   # This will make sure that targetcli was called at least once and the initial configuration was done.
   sudo targetcli --help
   
   sudo systemctl enable target
   sudo systemctl start target
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Skapa iSCSI-enhet på iSCSI-målservern

Koppla en ny datadisk till iSCSI-målet virtuell dator som kan användas för det här klustret. Datadisken kan vara så liten som 1 GB och måste placeras på ett Premiumlagringskonto eller en Premium hanteras Disk för att dra nytta av den [enkel VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Kör följande kommando på den **iSCSI-målet VM** att skapa en iSCSI-disk för det nya klustret. I följande exempel **cl1** används för att identifiera det nya klustret och **produktprenumeration-cl1-0** och **produktprenumeration-cl1-1** är värdnamn av klusternoderna. Ersätta dem med värdnamn för klusternoderna.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> /dev/disk/azure/scsi1/<b>lun0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# restart the iSCSI target service to persist the changes
sudo systemctl restart target
</code></pre>

### <a name="set-up-sbd-device"></a>Konfigurera uppstår enhet

Ansluta till iSCSI-enheten som har skapats i det sista steget från klustret.
Kör följande kommandon på noder i det nya klustret som du vill skapa.
Följande objekt är prefixet antingen **[A]** - gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Ansluta till iSCSI-enheter

   Först aktivera iSCSI och uppstår tjänster.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Ändra namnet på den första noden initieraren

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen så att den matchar ACL: er som du använde när du skapar en iSCSI-enhet på iSCSI-målservern

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Ändra namnet på den andra noden initieraren

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen så att den matchar ACL: er som du använde när du skapar en iSCSI-enhet på iSCSI-målservern

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]**  Starta om tjänsten iSCSI

   Starta om iSCSI-tjänsten för att tillämpa ändringen nu
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Anslut iSCSI-enheter. I exemplet nedan 10.0.0.17 är IP-adressen för iSCSI-målservern och 3260 är standardporten. <b>iqn.2006 04.cl1.local:cl1</b> är målnamnet som anges när du kör det första kommandot.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Kontrollera att iSCSI-enhet är tillgänglig och klar enhetsnamn (i följande exempel/dev/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   Nu kan hämta ID för iSCSI-enhet.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   Kommandot lista tre enhets-ID. Vi rekommenderar att använda det ID som börjar med scsi-3 i exemplet ovan detta är
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]**  Skapa uppstår-enhet

   Använda enhets-ID för iSCSI-enheten för att skapa en ny enhet uppstår på den första noden i klustret.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]**  Anpassa uppstår konfig

   Öppna konfigurationsfilen uppstår

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Ändra egenskapen på enheten som uppstår, aktivera pacemaker integrering och ändra startläget för uppstår.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Skapa softdog konfigurationsfil

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Läsa in modulen nu

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Klusterinstallation av

Följande objekt är prefixet antingen **[A]** - gäller för alla noder, **[1]** – gäller endast för nod 1 eller **[2]** – gäller endast för nod 2.

1. **[A]**  Uppdatera SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]**  Aktivera ssh-åtkomst

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Aktivera ssh-åtkomst

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Installera HA filnamnstillägget
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]**  Installationsprogrammet värdnamnsmatchning   

   Du kan använda en DNS-server, eller så kan du ändra de/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adressen och värdnamnet i följande kommandon

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Infoga följande rader till/etc/hosts. Ändra IP-adressen och värdnamnet som matchar din miljö   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Installera kluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]**  Lägg till nod i klustret
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]**  Ändra hacluster lösenord till samma lösenord

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]**  Konfigurera corosync om du vill använda andra transport och lägga till nodelist. Klustret fungerar inte på annat sätt.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Lägg till följande fetstil innehåll i filen.
   
   <pre><code> 
   [...]
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Starta om tjänsten corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]**  Ändra standardinställningarna pacemaker

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Skapa STONITH enhet

STONITH enheten använder ett huvudnamn för tjänsten för att godkänna mot Microsoft Azure. Följ dessa steg om du vill skapa ett huvudnamn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaperna och Skriv ned Directory-ID. Det här är den **klient-ID**.
1. Klicka på appen registreringar
1. Klicka på Lägg till
1. Ange ett namn, Välj typ av program ”Web app/API”, ange en inloggnings-URL (till exempel http://localhost) och klicka på Skapa
1. URL för inloggning används inte och kan vara en giltig URL
1. Välj den nya appen och klicka på nycklar på fliken Inställningar
1. Ange en beskrivning för en ny nyckel, Välj ”upphör aldrig att gälla” och klicka på Spara
1. Anteckna värdet. Den används som den **lösenord** för tjänstens huvudnamn
1. Skriv ned program-ID. Den används som användarnamnet (**inloggnings-ID** i stegen nedan) för tjänstens huvudnamn

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Skapa en anpassad roll för agenten avgränsningstecken

Tjänstens huvudnamn har inte behörighet att komma åt Azure-resurser som standard. Du behöver ge behörighet att starta och stoppa tjänstens huvudnamn (frigöra) alla virtuella datorer i klustret. Om du inte redan har skapat den anpassade rollen, kan du skapa den med hjälp av [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell#create-a-custom-role) eller [Azure CLI](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli#create-a-custom-role)

Använd följande innehåll för indatafilen. Du måste anpassa innehållet till dina prenumerationer genom att ersätta c276fc76-9cd4-44c9-99a7-4fd71546436e och e91d47c4-76f3-4271-a796-21b4ecfe3624 med ID: N för din prenumeration. Om du bara har en prenumeration kan du ta bort den andra posten i AssignableScopes.

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

Tilldela den anpassade rollen som ”Linux avgränsningstecken Agent roll” som har skapats i det senaste kapitlet till tjänstens huvudnamn. Använd inte rollen ägare längre!

1. Gå till https://portal.azure.com
1. Öppna bladet alla resurser
1. Välj den virtuella datorn från den första noden i klustret
1. Klicka på åtkomstkontroll (IAM)
1. Klicka på Lägg till
1. Välj rollen ”Linux avgränsningstecken Agent roll”
1. Ange namnet på programmet som du skapade ovan
1. Klicka på OK

Upprepa stegen ovan för den andra noden i klustret.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Skapa STONITH-enheter

När du har redigerat behörigheter för de virtuella datorerna kan du konfigurera STONITH-enheter i klustret.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Skapa avgränsningstecken topologi för uppstår avgränsningar

Om du vill använda en uppstår enhet fortfarande bör du använda en Azure avgränsningstecken agent som en säkerhetskopia om iSCSI-målservern inte är tillgänglig.

<pre><code>
fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1] ** Aktivera användning av en STONITH-enhet

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Nästa steg
* [Azure virtuella datorer planering och implementering för SAP][planning-guide]
* [Distribution av Azure virtuella datorer för SAP][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP][dbms-guide]
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA på virtuella Azure-datorer med hög tillgänglighet finns [hög tillgänglighet för SAP HANA på Azure Virtual Machines (virtuella datorer)][sap-hana-ha]