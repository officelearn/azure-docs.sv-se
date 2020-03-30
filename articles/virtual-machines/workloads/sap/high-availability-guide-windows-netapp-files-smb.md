---
title: Azure VMs HA för SAP NW i Windows med Azure NetApp Files (SMB)| Microsoft-dokument
description: Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files (SMB) för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591361"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i Windows med Azure NetApp Files(SMB) för SAP-program

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

I den hÃ¤r artikeln beskrivs hur du distribuerar, konfigurerar virtuella datorer, installerar klusterramverket och installerar ett högnÃ¤ndevÃ¤ljt AVSÃ¤ndervÃ¤ndevÃ¤ndevÃ¤kt på Windows VMs med [SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) on [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

Databaslagret beskrivs inte i detalj i den här artikeln. Vi antar att det [virtuella Azure-nätverket](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) redan har skapats.  

Läs följande SAP Notes och papers först:

* [Dokumentation om Azure NetApp Files][anf-azure-doc] 
* SAP Note [1928533][1928533], som innehåller:  
  * En lista över Azure VM-storlekar som stöds för distribution av SAP-programvara
  * Viktig kapacitetsinformation för Azure VM-storlekar
  * Sap-programvara och kombinationer av operativsystem (operativsystem) och databas
  * Nödvändig SAP-kärnversion för Windows på Microsoft Azure
* SAP Note [2015553][2015553] listar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure.
* SAP Note [2178632][2178632] har detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure.
* SAP Note [1999351][1999351] har ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP.
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) listar förutsättningar för SAP-stödd CA-funktion i SMB 3.x-protokollet.
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) har felsökningsinformation för den långsamma SAP-transaktionen AL11 på Windows 2012 och 2016.
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) har information om transparent redundansfunktion för en filresurs på Windows Server med SMB 3.0-protokollet.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) har rekommendation (inaktivera 8.3 namn generation) för att åtgärda Dålig filsystem prestanda / fel under dataåtkomster.
* [Installera SAP NetWeaver hög tillgänglighet på ett Windows redundanskluster och filresurs för SAP ASCS/SCS-instanser på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Azure Virtual Machines arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Lägga till avsökningsport i ASCS-klusterkonfiguration](sap-high-availability-installation-wsfc-file-share.md)
* [Installation av en (A)SCS-instans i ett redundanskluster](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Skapa en SMB-volym för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp-filer][anf-sap-applications-azure]

## <a name="overview"></a>Översikt

SAP utvecklade en ny metod och ett alternativ till klusterdelade diskar, för klustring av en SAP ASCS/SCS-instans i ett Windows redundanskluster. I stället för att använda klusterdelade diskar kan man använda en SMB-filresurs för att distribuera GLOBALA SAP-värdfiler. Azure NetApp Files stöder SMBv3 (tillsammans med NFS) med NTFS ACL med Active Directory. Azure NetApp-filer är automatiskt mycket tillgängliga (eftersom det är en PaaS-tjänst). Dessa funktioner gör Azure NetApp Files bra alternativ för att vara värd för SMB-filresursen för SAP global.  
Både [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) och Active Directory Domain Services [(AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) stöds. Du kan använda befintliga Active Directory-domänkontrollanter med Azure NetApp-filer. Domänkontrollanter kan finnas i Azure som virtuella datorer eller lokalt via ExpressRoute eller S2S VPN. In this article, we will use Domain controller in an Azure VM.  
Hög tillgänglighet (HA) för SAP Netweaver centrala tjänster kräver delad lagring. För att uppnå detta på Windows, hittills var det nödvändigt att bygga antingen SOFS kluster eller använda kluster delad disk s / w som SIOS. Nu är det möjligt att uppnå SAP Netweaver HA med hjälp av delad lagring, distribueras på Azure NetApp-filer. Om du använder Azure NetApp-filer för delad lagring elimineras behovet av antingen SOFS eller SIOS.  

> [!NOTE]
> Klustring av SAP ASCS/SCS-instanser med hjälp av en filresurs stöds för SAP NetWeaver 7.40 (och senare), med SAP Kernel 7.49 (och senare).  

![SAP ASCS/SCS HA-arkitektur med SMB-resurs](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Förutsättningarna för en SMB-filresurs är:
* SMB 3.0-protokollet (eller senare).
* Möjlighet att ange Active Directory-åtkomstkontrollistor (ACL: er) för Active Directory-användargrupper och datorobjektet computern.
* Filresursen måste vara HA-aktiverad.

Resursen för SAP Central-tjänsterna i den här referensarkitekturen erbjuds av Azure NetApp Files:

![SAP ASCS/SCS HA-arkitektur med SMB-resurs](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Skapa och montera SMB-volym för Azure NetApp-filer

Utför följande steg som förberedelse för att använda Azure NetApp-filer.  

1. Följ stegen för att [registrera dig för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Skapa Azure NetApp-konto, enligt stegen som beskrivs i [Skapa ett NetApp-konto](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Ställ in kapacitetspool, enligt instruktionerna i [Konfigurera en kapacitetspool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Azure NetApp-filresurser måste finnas i delegerat undernät. Följ instruktionerna i [Delegera ett undernät till Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) för att skapa delegerat undernät.  

> [!IMPORTANT]
> Du måste skapa Active Directory-anslutningar innan du skapar en SMB-volym. Granska [kraven för Active Directory-anslutningar](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections).  

5. Skapa Active Directory-anslutning enligt beskrivningen i [Skapa en Active Directory-anslutning](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. Skapa SMB Azure NetApp Files SMB-volym, enligt instruktionerna i [Lägg till en SMB-volym](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)  
7. Montera SMB-volymen på din virtuella Windows-dator.

> [!TIP]
> Du hittar instruktionerna för hur du monterar Azure NetApp Files-volymen, om du navigerar i [Azure Portal](https://portal.azure.com/#home) till Azure NetApp Files-objektet, klickar på **bladet Volymer** och sedan montera **instruktioner**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Förbereda infrastrukturen för SAP HA med hjälp av ett Windows-redundanskluster 

1. [Ange de DNS-IP-adresser som krävs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Ange statiska IP-adresser för virtuella SAP-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30).
3. [Ange en statisk IP-adress för Azures interna belastningsutjämnare](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e).
4. [Ange standardregler för BELASTNINGSUTJÄMNING AV ASCS/SCS för Azures interna belastningsutjämnare](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c).
5. [Ändra standardregler för belastningsutjämning för ASCS/SCS för azure-belastningsutjämnaren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716).
6. [Lägg till virtuella Windows-datorer i domänen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Lägga till registerposter i båda klusternoderna i SAP ASCS/SCS-instansen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Konfigurera ett Windows Server-redundanskluster för en SAP ASCS/SCS-instans](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Om du använder Windows Server 2016 rekommenderar vi att du konfigurerar [Azure Cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Installera SAP ASCS-instans på båda noderna

Du behöver följande programvara från SAP:
   * INSTALLATIONSVERKTYGET FÖR SAP Software Provisioning Manager (SWPM) SPS25 eller senare.
   * SAP Kernel 7.49 eller senare
   * Skapa ett virtuellt värdnamn (klusternätverksnamn) för den klustrade SAP ASCS/SCS-instansen, enligt beskrivningen i [Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Klustring av SAP ASCS/SCS-instanser med hjälp av en filresurs stöds för SAP NetWeaver 7.40 (och senare), med SAP Kernel 7.49 (och senare).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Installera en ASCS/SCS-instans på den första ASCS/SCS-klusternoden

1. Installera en SAP ASCS/SCS-instans på den första klusternoden. Starta SAP SWPM-installationsverktyget och navigera sedan till: **Produkt** > **DBMS** > Installation > Application Server ABAP (eller Java) > System med hög tillgänglighet > ASCS/SCS-instans > Första klusternoden.  

2. Välj **Filresurskluster** som klusterresurskonfiguration i SWPM.  
3. När du **SAP System Cluster Parameters**uppmanas att ange värdnamnet för Azure NetApp Files SMB-resurs som du redan har skapat som värdnamn för **filresurs.**  I det här exemplet är SMB-resursvärden **anfsmb-9562**. 

> [!IMPORTANT]
> Om Föreblivna kontrollresultat i SWPM visar villkoret för funktionen Kontinuerlig tillgänglighet inte uppfyllt, kan den åtgärdas genom att följa instruktionerna i [fördröjd felmeddelande när du försöker komma åt en delad mapp som inte längre finns i Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

> [!TIP]
> Om Förevändningskontroll Resultat i SWPM visar villkoret Växlingsstorlek inte uppfyllt kan du justera SWAP-storleken genom att navigera till Den här datorn>systemegenskaper>prestandainställningar> Avancerat> virtuellt minne> ändras.  

4. Konfigurera en SAP-klusterresurs, avsökningsporten, `SAP-SID-IP` med hjälp av PowerShell. Kör den här konfigurationen på en av SAP ASCS/SCS-klusternoderna enligt beskrivningen i [Konfigurera avsökningsport .](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Installera en ASCS/SCS-instans på den andra ASCS/SCS-klusternoden

1. Installera en SAP ASCS/SCS-instans på den andra klusternoden. Starta installationsverktyget för SAP SWPM och navigera sedan till **Product** > **DBMS** > Installation > Application Server ABAP (eller Java) > System med hög tillgänglighet > ASCS/SCS-instans > Ytterligare klusternod.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Installera en DBMS-instans och SAP-programservrar

Slutför SAP-installationen genom att installera:

   * En DBMS-instans  
   * En primär SAP-programserver  
   * Ytterligare en SAP-programserver  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testa redundansen för SAP ASCS/SCS-instansen 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Redundans från klusternod A till klusternoden B och tillbaka
I det här testscenariot refererar vi till klusternoden sapascs1 som nod A och till klusternoden sapascs2 som nod B.

1. Kontrollera att klusterresurserna körs på ![noden A. Bild 1: Klusterresurser för Windows Server som körs på nod A före redundanstestet](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Starta om klusternod A. SAP-klusterresurserna flyttas till klusternoden B. ![Bild 2: Klusterresurser för Windows Server som körs på nod B efter redundanstestet](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Lås inträdestest

1.Kontrollera att SAP Enqueue Replication Server (ERS) är aktiv  
2. Logga in på SAP-systemet, kör transaktionen SU01 och öppna ett användar-ID i ändringsläge. Det kommer att generera SAP-låspost.  
3. När du är inloggad i SAP-systemet visar du låsposten genom att navigera till transaktion ST12.  
4. Redundans över ASCS-resurser från klusternod A till klusternod B.  
5. Kontrollera att låsposten, som genererades innan REDUNDAnsen SAP ASCS/SCS-klusterresurserna behålls.  

![Figur 3: Låsposten behålls efter redundanstest](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Mer information finns i [Felsökning för Enqueue Failover i ASCS med ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Nästa steg

* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Om du vill veta hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP 
* HANA på Azure (stora instanser), se [SAP HANA (stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md).
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (virtuella datorer)][sap-hana-ha]
