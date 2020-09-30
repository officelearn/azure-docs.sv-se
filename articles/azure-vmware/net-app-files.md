---
title: NetApp-filer för Azure VMware-lösning
description: Använd Azure NetApp Files med virtuella Azure VMware-datorer för att migrera och synkronisera data på lokala servrar, virtuella Azure VMware-lösningar och moln infrastrukturer.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573930"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>NetApp-filer för Azure VMware-lösning

I den här artikeln går vi igenom stegen för att integrera Azure NetApp Files med Azure VMware Solution-baserade arbets belastningar. [Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) är en Azure-tjänst för att migrera och köra företags fil arbets belastningar i molnet utan kod ändringar. Det möjliggör enkel migrering av data i lokala och molnbaserade infrastrukturer. Snabb och säkerhetsförbättrad datasynkronisering fören klar migrerings-och DevOps-scenarier med funktioner som omedelbar ögonblicks bild, återställning och Active Directory-integrering.

## <a name="topology"></a>Topologi

I det här scenariot för att testa och verifiera Azure NetApp Files pool-delningen, Azure NetApp Files konfigureras på Azure med kapacitets pool, volymtyp och undernät. Vi har använt NFS-protokollet. Både Azure NetApp Files och Azure VMware-lösningen skapas i samma Azure-region, USA, östra. Anslutning till Azure VMware-lösningen är via Azure ExpressRoute.

![NetApp-filer för Azure VMware Solution Topology.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Förutsättningar 

> [!div class="checklist"]
> * Azure-prenumeration med Azure NetApp Files
> * Undernät för Azure NetApp-fil
> * Virtuell Linux-dator i Azure VMware-lösning
> * Virtuell Windows-dator på Azure VMware-lösning

## <a name="verify-configuration-of-azure-netapp-files"></a>Verifiera konfigurationen av Azure NetApp Files 

Först kontrollerar du konfigurationen av Azure NetApp Files som skapats i Azure på en Premium-disk.

1. I Azure Portal under **lagring**väljer du **Azure NetApp Files**. En lista över dina konfigurerade Azure NetApp Files visas.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Lista över Azure NetApp Files."::: 

2. Om du väljer ett NetApp-konto kan du visa olika inställningar. Om du till exempel väljer **contoso-anf2**visas dess inställningar. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Lista över Azure NetApp Files."::: 

3. Välj **kapacitets grupper** för att verifiera den konfigurerade poolen. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Lista över Azure NetApp Files.":::

    Du kan se att lagringspoolen har kon figurer ATS som 4 TiB med Premium disk.

4. Välj **volymer** (se skärm bild från steg 2) för att Visa volymer som skapats under kapacitets gruppen.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Lista över Azure NetApp Files.":::

5. Välj en volym för att visa dess konfiguration.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Lista över Azure NetApp Files.":::

    Du kan se att anfpool, med 200 GiB, skapades som en resurs för NetApp-filer. Ett privat virtuellt IP-nätverk har skapats för Azure NetApp Files som ger NFS-sökvägen till montering på virtuella datorer i Azure VMware-lösningen. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Protokoll som stöds av Azure NetApp Files  

Azure NetApp Files stöder resurs mappning för Server Message Block (SMB) och Network File System (NFS). 

- **SMB-resurs**: om du vill distribuera en SMB-volym måste du först skapa en Active Directory anslutning. De angivna domän kontrol Lanterna måste vara tillgängliga för det delegerade under nätet för Azure NetApp Files för att anslutningen ska lyckas. När Active Directory har kon figurer ATS i Azure NetApp Files kontot visas det som ett valbart objekt när du skapar SMB-volymer. 

- **NFS-resurs**: Azure NetApp Files bidrar till att skapa volymer med hjälp av NFS (NFSv3 och nfsv 4.1), SMBv3 eller ett dubbel protokoll (NFSV3 och SMB). En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. NFS kan monteras på en Linux-server med hjälp av en kommando rad.

## <a name="create-azure-netapp-files"></a>Skapa Azure NetApp Files 

1. Logga in på [Azure-portalen](https://rc.portal.azure.com/#home). Under Azure-tjänster väljer du **Azure NetApp Files**. 

2. Välj **+ Lägg** till för att skapa en ny Azure NetApp Files volym. 

3. Fyll i de obligatoriska fälten (namn, prenumeration, resurs grupp och plats) och välj **skapa**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Lägg till kapacitets grupper i Azure NetApp Files 

1. I Azure Portal väljer du **Azure NetApp Files**, väljer **kapacitets grupper** och **+ Lägg till pool**. 

2. Ange nödvändig information för volymens poolnamn, service nivå och disk storlek (FQDN) eller IP och vikt. Välj **Lägg till**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Lista över Azure NetApp Files.":::

3. Om du vill skapa volymer under poolen kapacitet väljer du **volymer** i sökrutan och väljer **+ Lägg till volym**. 
 
4. Fyll i de obligatoriska fälten så som visas på följande skärm bild.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Lista över Azure NetApp Files.":::

5. Välj resurs för protokoll typ på nästa sida. Välj de versioner om det är en NFS-resurs och välj Active Directory domän om det är en SMB-resurs.  

6. Om det är en NFS-resurs lägger du till käll-IP-adressen från där protokoll typ resursen kommer att få åtkomst. Välj **Granska + skapa**. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Lista över Azure NetApp Files.":::
 
    Under Azure NetApp Files i Azure Portal är din NFS-resurs redo att användas.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Lista över Azure NetApp Files.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Montera en NFS-filresurs på dina virtuella datorer i Azure VMware-lösningen

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Montera en NFS-filresurs på en virtuell Azure VMware-lösning Windows-dator

- Öppna en kommando tolk och kör kommandot för att mappa NFS-filresursen. Följande skärm bilder visar resurs enheten som är kopplad till en virtuell Windows-dator i Azure VMware-lösningen.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Lista över Azure NetApp Files.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Lista över Azure NetApp Files.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Montera en NFS-filresurs på en virtuell Azure VMware-lösning Linux-dator

#### <a name="setting-up-your-azure-instance"></a>Konfigurera din Azure-instans

1. I din Azure Portal associerar du Azure-instansen med ett undernät definierat i samma virtuella nätverk som din volym.

    > [!NOTE]
    > Under nätet behöver en regel för nätverks säkerhets grupp som tillåter trafik på NFS-portarna (2049, 111), UDP och TCP.

2. Öppna en SSH-klient och Anslut till din Azure-instans. Mer information finns i [så här använder du SSH-nycklar med Windows på Azure](../virtual-machines/linux/ssh-from-windows.md).

3. Installera NFS-klienten på Azure-instansen:
   - På Red Hat Enterprise Linux-eller SUSE Linux-instans: `sudo yum install -y nfs-utils`
   - På en Ubuntu-eller Debian-instans: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Montera fil systemet

1. Skapa en ny katalog på din Azure-instans: `sudo mkdir ANFPOOL`

2. Välj en IP-adress för IP-adress för montering.

3. Montera fil systemet: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Lista över Azure NetApp Files.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Skapa en Active Directory anslutning för en SMB-resurs

1. Välj ett NetApp-konto i Azure Portal.

2. Under Azure NetApp Files väljer du **Active Directory anslutningar**.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Lista över Azure NetApp Files."::: 

3. Välj **Anslut** för att ansluta SMB-resursen till Active Directory. Följande skärm bild visar domän information om SMB-resursen.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Lista över Azure NetApp Files."::: 

    Din Azure SMB-filresurs är klar att användas.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Lista över Azure NetApp Files."::: 

4. Mappa SMB-resursen till din virtuella Windows Azure VMware-lösning. Använd den SMB-monterings Sök väg som visas i föregående skärm bild. Mer information finns i [Mappa en nätverks enhet i Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [lagrings-hierarkin för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Se [livs cykel hantering av virtuella datorer i Azure VMware-lösningen](lifecycle-management-of-azure-vmware-solution-vms.md)
- Se [integrera Azure VMware-lösning i en hubb och eker-arkitektur](concepts-hub-and-spoke.md)
