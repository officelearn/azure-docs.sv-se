---
title: Konfigurera tillgänglighetsgrupper för VIRTUELLA SQL Server på virtuella RHEL-datorer i Azure - Linux Virtual Machines | Microsoft-dokument
description: Lär dig mer om hur du konfigurerar hög tillgänglighet i en RHEL-klustermiljö och konfigurerar STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 02/27/2020
ms.openlocfilehash: 40c91f67231fb6a9d01191ee5215eae8d4dc045b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096694"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Självstudiekurs: Konfigurera tillgänglighetsgrupper för SQL Server på virtuella RHEL-datorer i Azure 

> [!NOTE]
> Handledningen presenteras är i **offentlig förhandsvisning**. 
>
> Vi använder SQL Server 2017 med RHEL 7.6 i den här självstudien, men det är möjligt att använda SQL Server 2019 i RHEL 7 eller RHEL 8 för att konfigurera HA. Kommandona för att konfigurera resurser för tillgänglighetsgrupper har ändrats i RHEL 8 och du bör titta på artikeln, [Skapa tillgänglighetsgruppresurs](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) och RHEL 8-resurser för mer information om rätt kommandon.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Skapa en ny resursgrupp, tillgänglighetsuppsättning och virtuella Azure Linux-datorer (VM)
> - Aktivera hög tillgänglighet (HA)
> - Skapa ett Pacemaker-kluster
> - Konfigurera ett fäktningsmedel genom att skapa en STONITH-enhet
> - Installera SQL Server och mssql-verktyg på RHEL
> - Konfigurera SQL Server alltid på tillgänglighetsgrupp
> - Konfigurera AG-resurser (Availability Group) i Pacemaker-klustret
> - Testa en redundans och stängselagenten

Den här självstudien använder AZURE command-line interface (CLI) för att distribuera resurser i Azure.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du föredrar att installera och använda CLI lokalt kräver den här självstudien Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du har mer än en prenumeration [anger du den prenumeration](/cli/azure/manage-azure-subscriptions-azure-cli) som du vill distribuera dessa resurser till.

Använd följande kommando för att `<resourceGroupName>` skapa en resursgrupp i en region. Ersätt `<resourceGroupName>` med ett namn som du väljer. Vi använder `East US 2` för den här guiden. Mer information finns i följande [Snabbstart](../quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Nästa steg är att skapa en tillgänglighetsuppsättning. Kör följande kommando i Azure Cloud `<resourceGroupName>` Shell och ersätt med ditt resursgruppsnamn. Välj ett `<availabilitySetName>`namn för .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Du bör få följande resultat när kommandot är klart:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Skapa virtuella RHEL-datorer i tillgänglighetsuppsättningen

> [!WARNING]
> Om du väljer en PAY-AS-As-You-Go (PAYG) RHEL-avbildning och konfigurerar HA (Hög tillgänglighet) kan du behöva registrera din prenumeration. Detta kan leda till att du betalar två gånger för prenumerationen, eftersom du debiteras för Microsoft Azure RHEL-prenumerationen för den virtuella datorn och en prenumeration på Red Hat. Mer information finns i https://access.redhat.com/solutions/2458541.
>
> Använd en RHEL HA-avbildning när du skapar Den virtuella Azure-datorn för att undvika att bli "dubbel fakturerad". Bilder som erbjuds som RHEL-HA bilder är också PAYG bilder med HA repo pre-aktiverad.

1. Hämta en lista över virtuella datorer (VM) som erbjuder RHEL med HA:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Du bör se följande resultat:

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    För den här guiden väljer `RedHat:RHEL-HA:7.6:7.6.2019062019`vi bilden .

    > [!IMPORTANT]
    > Datornamn måste vara mindre än 15 tecken för att ställa in tillgänglighetsgrupp. Användarnamnet får inte innehålla versaler och lösenord måste innehålla fler än 12 tecken.

1. Vi vill skapa 3 virtuella datorer i tillgänglighetsuppsättningen. Ersätt följande i kommandot nedan:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`- Ett exempel skulle vara "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Kommandot ovan skapar de virtuella datorerna och skapar ett standardvnät för dessa virtuella datorer. Mer information om de olika konfigurationerna finns i artikeln [om att skapa az vm.](https://docs.microsoft.com/cli/azure/vm)

Du bör få resultat som liknar följande när kommandot är klart för varje virtuell dator:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Standardbilden som skapas med kommandot ovan skapar som standard en 32 GB OS-disk. Du kan eventuellt få på utrymme med den här standardinstallationen. Du kan använda följande parameter `az vm create` som lagts till i kommandot ovan för att `--os-disk-size-gb 128`skapa en OS-disk med 128 GB som exempel: .
>
> Du kan sedan [konfigurera LVM (Logical Volume Manager)](../../../virtual-machines/linux/configure-lvm.md) om du behöver utöka lämpliga mappvolymer för att passa installationen.

### <a name="test-connection-to-the-created-vms"></a>Testa anslutningen till de skapade virtuella datorerna

Anslut till VM1 eller andra virtuella datorer med följande kommando i Azure Cloud Shell. Om du inte kan hitta vm-IPs följer du den här [snabbstarten på Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Om anslutningen lyckas bör du se följande utgång som representerar Linux-terminalen:

```output
[<username>@<VM1> ~]$
```

Skriv `exit` för att lämna SSH-sessionen.

## <a name="enable-high-availability"></a>Aktivera hög tillgänglighet

> [!IMPORTANT]
> För att slutföra den här delen av självstudien måste du ha en prenumeration för RHEL och tillägget Hög tillgänglighet. Om du använder en bild som rekommenderas i föregående avsnitt behöver du inte registrera en annan prenumeration.
 
Anslut till varje VM-nod och följ guiden nedan för att aktivera HA. Mer information finns i [Aktivera prenumeration med hög tillgänglighet för RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Det blir enklare om du öppnar en SSH-session för var och en av de virtuella datorerna samtidigt som samma kommandon måste köras på varje virtuell dator i hela artikeln.
>
> Om du kopierar och `sudo` klistrar in flera kommandon och uppmanas att ange ett lösenord körs inte de ytterligare kommandona. Kör varje kommando separat.


1. Kör följande kommandon på varje virtuell dator för att öppna Pacemaker-brandväggsportarna:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Uppdatera och installera Pacemaker-paket på alla noder med följande kommandon:

    > [!NOTE]
    > **nmap** installeras som en del av det här kommandoblocket som ett verktyg för att hitta tillgängliga IP-adresser i nätverket. Du behöver inte installera **nmap**, men det kommer att vara användbart senare i den här guiden.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Ange lösenordet för standardanvändaren som skapas när pacemakerpaket installeras. Använd samma lösenord på alla noder.

    ```bash
    sudo passwd hacluster
    ```

1. Använd följande kommando för att öppna hosts-filen och konfigurera värdnamnsmatchning. Mer information finns i [Konfigurera AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) när du konfigurerar hosts-filen.

    ```
    sudo vi /etc/hosts
    ```

    I **vi** vi-redigeraren `i` anger du för att infoga text och på en tom rad lägger du till den **privata IP-adressen** för motsvarande virtuella dator. Lägg sedan till vm-namnet efter ett blanksteg bredvid IP-adressen. Varje rad ska ha en separat post.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Vi rekommenderar att du använder din **privata IP-adress** ovan. Om du använder den offentliga IP-adressen i den här konfigurationen misslyckas installationen och vi rekommenderar inte att du exponerar den virtuella datorn för externa nätverk.

    Om du vill avsluta vi-redigeraren trycker du `:wq` först på **Esc-tangenten** och anger sedan kommandot för att skriva filen och avsluta. **vi**

## <a name="create-the-pacemaker-cluster"></a>Skapa pacemakerklustret

I det här avsnittet aktiverar och startar vi pcsd-tjänsten och konfigurerar sedan klustret. För SQL Server på Linux skapas inte klusterresurserna automatiskt. Vi måste aktivera och skapa pacemakerresurserna manuellt. Mer information finns i artikeln om [hur du konfigurerar en redundansklusterinstans för RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Aktivera och starta pcsd-tjänst och Pacemaker

1. Kör kommandona på alla noder. Dessa kommandon gör det möjligt för noderna att ansluta till klustret efter omstart.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Ta bort en befintlig klusterkonfiguration från alla noder. Kör följande kommando:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. På den primära noden kör du följande kommandon för att konfigurera klustret.

    - När du `pcs cluster auth` kör kommandot för att autentisera klusternoderna uppmanas du att ange ett lösenord. Ange lösenordet för **hacluster-användaren** som skapats tidigare.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Kör följande kommando för att kontrollera att alla noder är online.

    ```bash
    sudo pcs status
    ```

    Om alla noder är online visas en utdata som liknar följande:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. Ange förväntade röster i live-klustret till 3. Det här kommandot påverkar bara det aktiva klustret och ändrar inte konfigurationsfilerna.

    På alla noder anger du de förväntade rösterna med följande kommando:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Konfigurera fäktningsagenten

En STONITH-enhet tillhandahåller ett stängselmedel. Nedanstående instruktioner ändras för den här självstudien. Mer information finns i [skapa en STONITH-enhet](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Kontrollera versionen av Azure Fence Agent för att se till att den uppdateras](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Ange följande kommando:

```bash
sudo yum info fence-agents-azure-arm
```

Du bör se en liknande utdata till exemplet nedan.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Registrera ett nytt program i Azure Active Directory
 
 1. Gå till https://portal.azure.com
 2. Öppna [Azure Active Directory-bladet](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Gå till Egenskaper och skriv ned katalog-ID. Detta är`tenant ID`
 3. Klicka på [ **Appregistreringar**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Klicka på **Ny registrering**
 5. Ange **Name** ett `<resourceGroupName>-app`namn som väljer du **Konton i den här organisationskatalogen**
 6. Välj **Programtypswebbplats**, ange en inloggnings-URL (till exempel http://localhost) och klicka på Lägg till. Inloggnings-URL:en används inte och kan vara en giltig URL. När du är klar klickar du **på Registrera dig**
 7. Välj **Certifikat och hemligheter** för din nya appregistrering och klicka sedan på Ny **klienthemlighet**
 8. Ange en beskrivning av en ny nyckel (klienthemlighet), välj **Aldrig upphör att gälla** och klicka på Lägg **till**
 9. Skriv ner värdet på hemligheten. Den används som lösenord för servicehuvudmannen
10. Välj **Översikt**. Skriv ner program-ID: t. Det används som användarnamn (inloggnings-ID i stegen nedan) av Service Principal
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Skapa en anpassad roll för stängslet agent

Följ självstudien för att [skapa en anpassad roll för Azure-resurser med Azure CLI](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Filen json ska se ut ungefär så här:

- Ersätt `<username>` med ett valfritt namn. Detta för att undvika dubbelarbete när du skapar den här rolldefinitionen.
- Ersätt `<subscriptionId>` med ditt Azure-prenumerations-ID.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
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
    "/subscriptions/<subscriptionId>"
  ]
}
```

Om du vill lägga till rollen kör du följande kommando:

- Ersätt `<filename>` med namnet på filen.
- Om du kör kommandot från en annan sökväg än mappen som filen sparas i, inkludera mappsökvägen för filen i kommandot.

```bash
az role definition create --role-definition "<filename>.json"
```

Du bör se följande utdata:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Tilldela den anpassade rollen till tjänsthuvudhuvudmannen

Tilldela den `Linux Fence Agent Role-<username>` anpassade roll som skapades i det sista steget till huvudmannen för Tjänsten. Använd inte ägarrollen längre!
 
1. Gå till https://portal.azure.com
2. Öppna [bladet Alla resurser](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Välj den virtuella datorn för den första klusternoden
4. Klicka på **Åtkomstkontroll (IAM)**
5. Klicka på **Lägg till en rolltilldelning**
6. Välj rollen `Linux Fence Agent Role-<username>` i **rolllistan**
7. I listan **Välj** anger du namnet på det program som du skapade ovan,`<resourceGroupName>-app`
8. Klicka på **Spara**
9. Upprepa stegen ovan för alla klusternoder.

### <a name="create-the-stonith-devices"></a>Skapa STONITH-enheterna

Kör följande kommandon på nod 1:

- Ersätt `<ApplicationID>` värdet med ID från din programregistrering.
- Ersätt `<servicePrincipalPassword>` värdet från klienthemligheten.
- Ersätt `<resourceGroupName>` med resursgruppen från din prenumeration som används för den här självstudien.
- Ersätt `<tenantID>` och `<subscriptionId>` från din Azure-prenumeration.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Eftersom vi redan har lagt till en regel`--add-service=high-availability`i vår brandvägg för att tillåta HA-tjänsten ( ) finns det ingen anledning att öppna följande brandväggsportar på alla noder: 2224, 3121, 21064, 5405. Men om du upplever någon typ av anslutningsproblem med HA använder du följande kommando för att öppna dessa portar som är associerade med HA.

> [!TIP]
> Du kan också lägga till alla portar i den här självstudien samtidigt för att spara lite tid. De hamnar som behöver öppnas förklaras i de relativa avsnitten nedan. Om du vill lägga till alla portar nu lägger du till ytterligare portar: 1433 och 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Installera SQL Server och mssql-verktyg
 
Använd avsnittet nedan för att installera SQL Server och mssql-verktyg på de virtuella datorerna. Utför var och en av dessa åtgärder på alla noder. Mer information finns i installera en virtuell dator för [SQL Server i Red Hat](/sql/linux/quickstart-install-connect-red-hat).

### <a name="installing-sql-server-on-the-vms"></a>Installera SQL Server på de virtuella datorerna

Följande kommandon används för att installera SQL Server:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Öppna brandväggsport 1433 för fjärranslutningar

Du måste öppna port 1433 på den virtuella datorn för att kunna fjärransluta. Använd följande kommandon för att öppna port 1433 i brandväggen för varje virtuell dator:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Installera kommandoradsverktyg för SQL Server

Följande kommandon används för att installera kommandoradsverktyg för SQL Server. Mer information finns [i installera kommandoradsverktygen för SQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> För enkelhetens skull, lägg till /opt/mssql-tools/bin/ till din PATH-miljövariabel. På så sätt kan du köra verktygen utan att ange den fullständiga sökvägen. Kör följande kommandon för att ändra PATH för både inloggningssessioner och interaktiva/icke-inloggningssessioner:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Kontrollera status för SQL Server

När du är klar med konfigurationen kan du kontrollera status för SQL Server och kontrollera att den körs:

```bash
systemctl status mssql-server --no-pager
```

Du bör se följande utdata:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>Konfigurera SQL Server alltid på tillgänglighetsgrupp

Använd följande steg för att konfigurera SQL Server Always On Availability Group för dina virtuella datorer. Mer information finns i [konfigurera SQL Server Always On Availability Group för hög tillgänglighet på Linux](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>Aktivera AlwaysOn-tillgänglighetsgrupper och starta om mssql-server

Aktivera AlwaysOn-tillgänglighetsgrupper på varje nod som är värd för en SQL Server-instans. Starta sedan om mssql-servern. Kör följande skript:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Skapa ett certifikat

Vi stöder för närvarande inte AD-autentisering till AG-slutpunkten. Därför måste vi använda ett certifikat för AG-slutpunktskryptering.

1. Anslut till **alla noder** med SQL Server Management Studio (SSMS) eller SQL CMD. Kör följande kommandon för att aktivera AlwaysOn_health session och skapa en huvudnyckel:

    > [!IMPORTANT]
    > Om du fjärransluter till SQL Server-instansen måste du ha port 1433 öppen i brandväggen. Du måste också tillåta inkommande anslutningar till port 1433 i NSG för varje virtuell dator. Mer information finns i [Skapa en säkerhetsregel](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) för att skapa en inkommande säkerhetsregel.

    - Ersätt `<Master_Key_Password>` med ditt eget lösenord.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Anslut till den primära repliken med SSMS eller SQL CMD. Nedanstående kommandon skapar ett `/var/opt/mssql/data/dbm_certificate.cer` certifikat på och `var/opt/mssql/data/dbm_certificate.pvk` en privat nyckel på din primära SQL Server-replik:

    - Ersätt `<Private_Key_Password>` med ditt eget lösenord.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO

BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
GO
```

Avsluta SQL CMD-sessionen `exit` genom att köra kommandot och återgå till SSH-sessionen.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Kopiera certifikatet till de sekundära replikerna och skapa certifikaten på servern

1. Kopiera de två filer som har skapats till samma plats på alla servrar som ska vara värd för tillgänglighetsrepliker.
 
    På den primära servern `scp` kör du följande kommando för att kopiera certifikatet till målservrarna:

    - Ersätt `<username>` `<VM2>` och med användarnamnet och mål-VM-namnet som du använder.
    - Kör det här kommandot för alla sekundära repliker.

    > [!NOTE]
    > Du behöver inte köra `sudo -i`, vilket ger dig rotmiljön. Du kan bara `sudo` köra kommandot framför varje kommando som vi tidigare gjorde i den här guiden.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Kör följande kommando på målservern:

    - Ersätt `<username>` med ditt användarnamn.
    - Kommandot `mv` flyttar filerna eller katalogen från en plats till en annan.
    - Kommandot `chown` används för att ändra ägare och grupp av filer, kataloger eller länkar.
    - Kör dessa kommandon för alla sekundära repliker.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Följande Transact-SQL-skript skapar ett certifikat från säkerhetskopian som du skapade på den primära SQL Server-repliken. Uppdatera skriptet med starka lösenord. Dekrypteringslösenordet är samma lösenord som du använde för att skapa pvk-filen i föregående steg. Om du vill skapa certifikatet kör du följande skript med SQL CMD eller SSMS på alla sekundära servrar:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Skapa databasspeglingslutpunkter på alla repliker

Kör följande skript på alla SQL-instanser med SQL CMD eller SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>Skapa tillgänglighetsgruppen

Anslut till SQL Server-instansen som är värd för den primära repliken med SQL CMD eller SSMS. Kör följande kommando för att skapa tillgänglighetsgruppen:

- Ersätt `ag1` med önskat tillgänglighetsgruppnamn.
- Ersätt `<VM1>`, `<VM2>`och `<VM3>` värdena med namnen på SQL Server-instanserna som är värdar för replikerna.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Skapa en SQL Server-inloggning för Pacemaker

Skapa en SQL-inloggning för Pacemaker på alla SQL-servrar. Följande Transact-SQL skapar en inloggning.

- Ersätt `<password>` med ditt eget komplexa lösenord.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Spara de autentiseringsuppgifter som används för SQL Server-inloggningen på alla SQL-servrar. 

1. Skapa filen:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Lägg till följande två rader i filen:

    ```bash
    pacemakerLogin
    <password>
    ```

    Om du vill avsluta vi-redigeraren trycker du `:wq` först på **Esc-tangenten** och anger sedan kommandot för att skriva filen och avsluta. **vi**

1. Gör filen endast läsbar med root:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Ansluta sekundära repliker till tillgänglighetsgruppen

1. För att kunna ansluta de sekundära replikerna till AG måste du öppna port 5022 i brandväggen för alla servrar. Kör följande kommando i SSH-sessionen:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. På dina sekundära repliker kör du följande kommandon för att ansluta dem till AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Kör följande Transact-SQL-skript på den primära repliken och varje sekundär replik:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. När de sekundära replikerna har anslutits kan du se dem i SSMS Object Explorer genom att expandera noden **Alltid vid hög tillgänglighet:**

    ![tillgänglighet-grupp-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Lägga till en databas i tillgänglighetsgruppen

Vi kommer att följa [artikeln konfigurera tillgänglighetsgrupp om hur du lägger till en databas](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

Följande Transact-SQL-kommandon används i det här steget. Kör dessa kommandon på den primära repliken:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Kontrollera att databasen har skapats på de sekundära servrarna

På varje sekundär SQL Server-replik kör du följande fråga för att se om db1-databasen skapades och är i synkroniserat tillstånd:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Om `synchronization_state_desc` listan SYNKRONISERAS `db1`för betyder det att replikerna är synkroniserade. Sekundärerna visas `db1` i den primära repliken.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Skapa resurser för tillgänglighetsgrupp i pacemakerklustret

Vi kommer att följa guiden för att [skapa resurser för tillgänglighetsgruppen i Pacemaker-klustret](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

### <a name="create-the-ag-cluster-resource"></a>Skapa AG-klusterresursen

1. Använd följande kommando för `ag_cluster` att skapa resursen i tillgänglighetsgruppen `ag1`.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Kontrollera din resurs och se till att de är online innan du fortsätter med följande kommando:

    ```bash
    sudo pcs resource
    ```

    Du bör se följande utdata:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```

### <a name="create-a-virtual-ip-resource"></a>Skapa en virtuell IP-resurs

1. Använd en tillgänglig statisk IP-adress från nätverket för att skapa en virtuell IP-resurs. Du kan hitta en `nmap`med kommandoverktyget .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Ange egenskapen **stonith-aktiverad** till false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Skapa den virtuella IP-resursen med hjälp av följande kommando:

    - Ersätt `<availableIP>` värdet nedan med en oanvänd IP-adress.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Lägg till begränsningar

1. För att säkerställa att IP-adressen och AG-resursen körs på samma nod måste ett samlokaliseringsvillkor konfigureras. Kör följande kommando:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Skapa ett ordervillkor för att säkerställa att AG-resursen är igång före IP-adressen. Även om samlokaliseringsbegränsningen innebär ett beställningsvillkor, verkställer detta det.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Om du vill kontrollera begränsningarna kör du följande kommando:

    ```bash
    sudo pcs constraint list --full
    ```

    Du bör se följande utdata:

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Återaktivera stonith

Vi är redo för testning. Aktivera stonith i klustret genom att köra följande kommando på nod 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Kontrollera klusterstatus

Du kan kontrollera status för klusterresurserna med följande kommando:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Redundanstest

För att säkerställa att konfigurationen har lyckats hittills kommer vi att testa en redundans. Mer information finns i [Alltid på tillgänglighet grupp redundans på Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Kör följande kommando för att manuellt redundans den primära repliken till `<VM2>`. Ersätt `<VM2>` med värdet på servernamnet.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Om du kontrollerar dina begränsningar igen ser du att ett annat villkor har lagts till på grund av den manuella redundansen:

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. Ta bort villkoret `cli-prefer-ag_cluster-master` med ID med följande kommando:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Kontrollera klusterresurserna med `sudo pcs resource`kommandot och du bör se `<VM2>`att den primära instansen är nu .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Testa stängsel

Du kan testa STONITH genom att köra följande kommando. Prova att köra `<VM1>` kommandot `<VM3>`nedan från för .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Som standard inaktiverar stängselåtgärden noden och aktiveras sedan. Om du bara vill koppla från noden `--off` använder du alternativet i kommandot.

Du bör få följande utdata:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Mer information om hur du testar en stängselenhet finns i följande [Red Hat-artikel.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar)

## <a name="next-steps"></a>Nästa steg

För att kunna använda en tillgänglighetsgrupplyssnare för dina SQL-servrar måste du skapa och konfigurera en belastningsutjämnare.

> [!div class="nextstepaction"]
> [Självstudiekurs: Konfigurera tillgänglighetsgruppavlyssnare för SQL Server på virtuella RHEL-datorer i Azure](sql-server-linux-rhel-ha-listener-tutorial.md)
