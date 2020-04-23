---
title: Azure VMware Solution by CloudSimple – Optimera ditt CloudSimple Private Cloud för Oracle RAC
description: Beskriver hur du distribuerar ett nytt kluster och optimerar en virtuell dator för installation och konfiguration av Oracle Real Application Clusters (RAC)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b945beaa7497e1ad19315bacf1284dd0cbc24d6a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868080"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimera ditt CloudSimple Private Cloud för att installera Oracle RAC

Du kan distribuera ORACLE Real Application Clusters (RAC) i din CloudSimple Private Cloud-miljö. I den här guiden beskrivs hur du distribuerar ett nytt kluster och optimerar en virtuell dator för Oracle RAC-lösningen. När du har slutfört stegen i det här avsnittet kan du installera och konfigurera Oracle RAC.

## <a name="storage-policy"></a>Lagringsprincip

En lyckad implementering av Oracle RAC kräver ett tillräckligt antal noder i klustret.  I vSAN-lagringsprincipen tillämpas fel på att tolerera (FTT) på datadiskar som används för att lagra databasen, logga och göra om diskar.  Det antal noder som krävs för att effektivt tolerera fel är 2N+1 där N är värdet av FTT.

Exempel: Om den önskade ftten är 2 måste det totala antalet noder i klustret vara 2*2+1 = 5.

## <a name="overview-of-deployment"></a>Översikt över distribution

I följande avsnitt beskrivs hur du konfigurerar din CloudSimple Private Cloud-miljö för Oracle RAC.

1. Metodtips för diskkonfiguration
2. Distribuera CloudSimple Private Cloud vSphere Cluster
3. Konfigurera nätverk för Oracle RAC
4. Konfigurera vSAN-lagringsprinciper
5. Skapa virtuella Oracle-datorer och skapa delade VM-diskar
6. Ställa in tillhörighetsregler för vm-till-värd

## <a name="best-practices-for-disk-configuration"></a>Metodtips för diskkonfiguration

Oracle RAC virtuella datorer har flera diskar, som används för specifik funktion.  Delade diskar är monterade på alla virtuella datorer, som används av Oracle RAC Cluster.  Operativsystem och programvara installationsdiskar är monterade endast på de enskilda virtuella datorer.  

![Översikt över Oracle RAC-diskar för virtuella datorer](media/oracle-vm-disks-overview.png)

I det här exemplet används de diskar som definierats i tabellen nedan.

| Disk                                      | Syfte                                       | Delad disk |
|-------------------------------------------|-----------------------------------------------|-------------|
| Operativsystem                                        | Operativsystemdisk                         | Inga          |
| Rutnät                                      | Installera plats för Oracle Grid-programvara     | Inga          |
| DATABASE                                  | Installera plats för Oracles databasprogram | Inga          |
| ORAHOME (ORAHOME)                                   | Basplats för Oracles databasbinärer    | Inga          |
| DATA1, DATA2, DATA3, DATA4                | Disk där Oracles databasfiler lagras   | Ja         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Gör om loggdiskar                                | Ja         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Röstningsdiskar                                  | Ja         |
| FRA1, FRA2                                | Snabbt återställningsområde diskar                      | Ja         |

![Oracle-diskkonfiguration för virtuell dator](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Konfiguration av virtuella datorer

* Varje virtuell dator är konfigurerad med fyra SCSI-styrenheter.
* SCSI-styrenhetstyp är inställd på VMware paravirtual.
* Flera virtuella diskar (.vmdk) skapas.
* Diskar är monterade på olika SCSI-styrenheter.
* Multi writer sharing type är inställd för delade klusterdiskar.
* vSAN-lagringsprincipen har definierats för att säkerställa hög tillgänglighet för diskar.

### <a name="operating-system-and-software-disk-configuration"></a>Konfiguration av operativsystem och programvara

Varje Virtuell Oracle-dator är konfigurerad med flera diskar för värdoperativsystemet, byte, programvaruinstallation och andra os-funktioner.  Dessa diskar delas inte mellan de virtuella datorerna.  

* Tre diskar för varje virtuell dator konfigureras som virtuella diskar och monteras på virtuella Oracle RAC-datorer.
    * OS-disk
    * Disk för lagring av Oracle Grid installerar filer
    * Disk för lagring av Oracle-databasinstallationsfiler
* Diskar kan konfigureras som **Tunna etablerade**.
* Varje disk är monterad på den första SCSI-styrenheten (SCSI0).  
* Delning är inställt på **Ingen delning**.
* Redundans definieras på lagringen med vSAN-principer.  

![Oracle RAC-konfiguration av datadiskgrupp](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Konfiguration av datadisk

Datadiskar används främst för att lagra databasfiler.  

* Fyra diskar är konfigurerade som virtuella diskar och monterade på alla Virtuella Oracle RAC-datorer.
* Varje disk är monterad på en annan SCSI-styrenhet.
* Varje virtuell disk är konfigurerad som **Tjock etablering Eager Zeroed**.  
* Delning är inställt på **Multi-writer**.  
* Diskarna måste konfigureras som en ASM-diskgrupp (Automatic Storage Management).  
* Redundans definieras på lagringen med vSAN-principer.  
* ASM-redundans är inställd **på Extern** redundans.

![Oracle RAC-konfiguration av datadiskgrupp](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Gör om loggdiskkonfiguration

Gör om loggfiler används för att lagra en kopia av de ändringar som gjorts i databasen.  Loggfilerna används när data måste återställas efter eventuella fel.

* Om du gör om loggdiskar måste konfigureras som flera diskgrupper.  
* Sex diskar skapas och monteras på alla virtuella Oracle RAC-datorer.
* Diskar är monterade på olika SCSI-styrenheter
* Varje virtuell disk är konfigurerad som **Tjock etablering Eager Zeroed**.
* Delning är inställt på **Multi-writer**.  
* Diskarna måste konfigureras som två ASM-diskgrupper.
* Varje ASM-diskgrupp innehåller tre diskar, som finns på olika SCSI-styrenheter.  
* ASM-redundansen är inställd på **Normal** redundans.
* Fem gör om loggfiler skapas i både ASM Redo-logggruppen

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC gör om loggdiskgruppskonfiguration](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle-röstdiskkonfiguration

Röstningsdiskar tillhandahåller kvorumdiskfunktioner som en extra kommunikationskanal för att undvika situationer med delad hjärna.

* Fem diskar skapas och monteras på alla virtuella Oracle RAC-datorer.
* Diskar är monterade på en SCSI-styrenhet
* Varje virtuell disk är konfigurerad som **Tjock etablering Eager Zeroed**.
* Delning är inställt på **Multi-writer**.  
* Diskarna måste konfigureras som en ASM-diskgrupp.  
* ASM-redundansen är inställd **på Hög** redundans.

![Oracle RAC-konfiguration av röstdiskgrupp](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle snabb återställning område diskkonfiguration (valfritt)

Den snabba återställningsområdet (FRA) är filsystem som förvaltas av Oracle ASM diskgrupp.  FRA tillhandahåller en delad lagringsplats för säkerhetskopierings- och återställningsfiler. Oracle skapar arkiverade loggar och flashback loggar i snabbt återställningsområdet. Oracle Recovery Manager (RMAN) kan eventuellt lagra sina säkerhetskopior och bildkopior i området snabb återställning, och den använder den när du återställer filer under medieåterställning.

* Två diskar skapas och monteras på alla virtuella Oracle RAC-datorer.
* Diskar är monterade på olika SCSI-styrenhet
* Varje virtuell disk är konfigurerad som **Tjock etablering Eager Zeroed**.
* Delning är inställt på **Multi-writer**.  
* Diskarna måste konfigureras som en ASM-diskgrupp.  
* ASM-redundans är inställd **på Extern** redundans.

![Oracle RAC-konfiguration av röstdiskgrupp](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Distribuera CloudSimple Private Cloud vSphere kluster

Så här distribuerar du ett vSphere-kluster i ditt privata moln:

1. Skapa [ett privat moln](create-private-cloud.md)från CloudSimple-portalen . CloudSimple skapar en standard vCenter-användare med namnet "cloudowner" i det nyskapade privata molnet. Mer information om standardanvändare och behörighetsmodell för Private Cloud finns [i Lär dig behörighetsmodellen För privat moln](learn-private-cloud-permissions.md).  Det här steget skapar det primära hanteringsklustret för ditt privata moln.

2. Expandera [det privata molnet](expand-private-cloud.md) med ett nytt kluster från CloudSimple-portalen.  Det här klustret används för att distribuera Oracle RAC.  Välj antalet noder baserat på önskad feltolerans (minst tre noder).

## <a name="set-up-networking-for-oracle-rac"></a>Konfigurera nätverk för Oracle RAC

1. Skapa [två virtuella nätverk i](create-vlan-subnet.md)ditt privata moln , ett för Oracles offentliga nätverk och ett för Oracles privata nätverk och tilldela lämpliga CIDR-nät undernät.
2. När VLAN har skapats skapar du de [distribuerade portgrupperna på Private Cloud vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Konfigurera en [virtuell DHCP- och DNS-serverserver](dns-dhcp-setup.md) på hanteringsklustret för Oracle-miljön.
4. [Konfigurera DNS-vidarebefordran på DNS-servern](on-premises-dns-setup.md#create-a-conditional-forwarder) som är installerad i det privata molnet.

## <a name="set-up-vsan-storage-policies"></a>Konfigurera vSAN-lagringsprinciper

vSAN-principer definierar fel att tolerera och disk striping för data som lagras på VM-diskarna.  Lagringsprincipen som skapas måste tillämpas på VM-diskarna när den virtuella datorn skapas.

1. [Logga in på vSphere-klienten](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access) för ditt privata moln.
2. Välj Principer och **profiler**på den övre menyn .
3. Välj **VM-lagringsprinciper** på den vänstra menyn och välj sedan **Skapa en vm-lagringsprincip**.
4. Ange ett meningsfullt namn för principen och klicka på **NÄSTA**.
5. I avsnittet **Principstruktur** väljer du **Aktivera regler för vSAN-lagring** och klickar på **NÄSTA**.
6. I avsnittet **vSAN-tillgänglighet** > **Availability** väljer du **Ingen** för platskatastroftolerans. Om du vill tolerera fel väljer du **alternativet RAID - Spegling** för önskad FTT.
    ![vSAN-inställningar](media/oracle-rac-storage-wizard-vsan.png).
7. I avsnittet **Avancerat** väljer du antalet diskränder per objekt. För reservation för objektutrymme väljer du **Tjock etablerad**. Välj **Inaktivera objektkontrollsumma**. Klicka på **NÄSTA**.
8. Följ anvisningarna på skärmen för att visa listan över kompatibla vSAN-datalager, granska inställningarna och avsluta installationen.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Skapa virtuella Oracle-datorer och skapa delade VM-diskar för Oracle

Om du vill skapa en virtuell dator för Oracle klonar du en befintlig virtuell dator eller skapar en ny.  I det här avsnittet beskrivs hur du skapar en ny virtuell dator och sedan klonar den för att skapa en andra efter installation av basoperativsystemet.  När de virtuella datorerna har skapats kan du skapa en tilläggsdiskar i dem.  Oracle-kluster använder delade diskar för lagring, data, loggar och gör om loggar.

### <a name="create-vms"></a>Skapa VM:ar

1. Klicka på ikonen **Värdar och kluster i** vCenter. Välj det kluster som du skapade för Oracle.
2. Högerklicka på klustret och välj **Ny virtuell dator**.
3. Välj **Skapa ny virtuell dator** och klicka på **Nästa**.
4. Namnge datorn, välj oraklet för den virtuella datorn och klicka på **Nästa**.
5. Markera klusterresursen och klicka på **Nästa**.
6. Markera vSAN-datalagret för klustret och klicka på **Nästa**.
7. Behåll standardvalet för KOMPATIBILITETEN ESXi 6.5 och klicka på **Nästa**.
8. Välj gäst-OS för ISO för den virtuella datorn som du skapar och klicka på **Nästa**.
9. Välj den hårddiskstorlek som krävs för att installera operativsystemet.
10. Om du vill installera programmet på en annan enhet klickar du på **Lägg till ny enhet**.
11. Välj nätverksalternativ och tilldela den distribuerade portgruppen som skapats för det offentliga nätverket.
12. Om du vill lägga till ytterligare nätverksgränssnitt klickar du på **Lägg till ny enhet** och väljer den distribuerade portgrupp som skapats för det privata nätverket.
13. För Ny DC/DVD-enhet väljer du den ISO-fil för datalager som innehåller ISO för den önskade installationen av operativsystemet. Markera filen som du tidigare har laddat upp till mappen Iso-filer och mallar och klicka på **OK**.
14. Granska inställningarna och klicka på **OK** för att skapa den nya virtuella datorn.
15. Slå på den virtuella datorn. Installera operativsystemet och eventuella uppdateringar som krävs

När operativsystemet har installerats kan du klona en andra virtuell dator. Högerklicka på den virtuella datorn posten och välj och klona alternativet.

### <a name="create-shared-disks-for-vms"></a>Skapa delade diskar för virtuella datorer

Oracle använder delad disk för att lagra data, logga och göra om loggfiler.  Du kan skapa en delad disk på vCenter och montera den på båda de virtuella datorerna.  Om du vill ha högre prestanda placerar du datadiskarna på olika SCSI-styrenheter Steg nedan som visar hur du skapar en delad disk på vCenter och sedan bifoga den till en virtuell dator. vCenter Flash-klienten används för att ändra egenskaperna för den virtuella datorn.

#### <a name="create-disks-on-the-first-vm"></a>Skapa diskar på den första virtuella datorn

1. Högerklicka på en av de virtuella Oracle-datorerna i vCenter och välj **Redigera inställningar**.
2. I avsnittet ny enhet väljer du **SCSI-styrenhet** och klickar på **Lägg till**.
3. I avsnittet ny enhet väljer du **Ny hårddisk** och klickar på **Lägg till**.
4. Expandera egenskaperna för ny hårddisk.
5. Ange hårddiskens storlek.
6. Ange den vm-lagringsprincip som ska vara den vSAN-lagringsprincip som du definierade tidigare.
7. Välj plats som en mapp i vSAN-datalager. Platsen hjälper till med att bläddra och ansluta diskarna till en andra virtuell dator.
8. För disketablering väljer du **Tjock etablering ivrig nollställd**.
9. För delning anger du **Flera författare**.
10. För den virtuella enhetsnoden väljer du den nya SCSI-styrenheten som skapades i steg 2.

    ![Skapa diskar på första virtuella datorer](media/oracle-rac-new-hard-disk.png)

Upprepa steg 2 – 10 för alla nya diskar som krävs för Oracle-data, loggar och gör om loggfiler.

#### <a name="attach-disks-to-second-vm"></a>Koppla diskar till den andra virtuella datorn

1. Högerklicka på en av de virtuella Oracle-datorerna i vCenter och välj **Redigera inställningar**.
2. I avsnittet ny enhet väljer du **SCSI-styrenhet** och klickar på **Lägg till**.
3. I avsnittet ny enhet väljer du **Befintlig hårddisk** och klickar på **Lägg till**.
4. Bläddra till den plats där disken skapades för den första virtuella datorn och välj VMDK-filen.
5. Ange den vm-lagringsprincip som ska vara den vSAN-lagringsprincip som du definierade tidigare.
6. För disketablering väljer du **Tjock etablering ivrig nollställd**.
7. För delning anger du **Flera författare**.
8. För den virtuella enhetsnoden väljer du den nya SCSI-styrenheten som skapades i steg 2.

    ![Skapa diskar på första virtuella datorer](media/oracle-rac-existing-hard-disk.png)

Upprepa steg 2 – 7 för alla nya diskar som krävs för Oracle-data, loggar och gör om loggfiler.

## <a name="set-up-vm-host-affinity-rules"></a>Ställa in värdtillhörighetsregler för virtuella datorer

VM-till-värd-tillhörighetsregler säkerställer att den virtuella datorn körs på önskad värd.  Du kan definiera regler för vCenter för att säkerställa att Oracles virtuella dator körs på värden med tillräckliga resurser och för att uppfylla eventuella specifika licenskrav.

1. I CloudSimple-portalen [eskalerar du molnägarens användares privilegier.](escalate-private-cloud-privileges.md)
2. [Logga in på vSphere-klienten](https://docs.azure.cloudsimple.com/vsphere-access) för ditt privata moln.
3. I vSphere-klienten väljer du det kluster där virtuella Oracle-datorer distribueras och klickar på **Konfigurera**.
4. Välj **VM/värdgrupper**under Konfigurera .
5. Klicka **+** på .
6. Lägg till en VM-grupp. Välj **VM-grupp** som typ. Ange namnet på gruppen. Markera de virtuella datorerna och klicka sedan på **OK** för att skapa gruppen.
6. Lägg till en värdgrupp. Välj **Värdgrupp** som typ. Ange namnet på gruppen. Välj de värdar där de virtuella datorerna ska köras och klicka sedan på **OK** för att skapa gruppen.
7. Om du vill skapa en regel klickar du på **VM/Värdregler**.
8. Klicka **+** på .
9. Ange ett namn på regeln och markera **Aktivera**.
10. För regeltypen väljer du **Virtuella datorer som ska vara värd**för .
11. Välj den VM-grupp som innehåller virtuella Oracle-datorer.
12. Välj **Måste köras på värdar i den här gruppen**.
13. Markera den värdgrupp som du skapade.
14. Skapa regeln genom att klicka på **OK**.

## <a name="references"></a>Referenser

* [Om vSAN-principer](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware Multi-Writer-attribut för delade VMDK:er](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
