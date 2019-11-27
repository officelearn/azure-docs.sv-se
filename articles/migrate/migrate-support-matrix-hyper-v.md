---
title: Stöd för Hyper-V-utvärdering/migrering i Azure Migrate
description: Läs mer om stöd för Hyper-V-utvärdering/migrering med Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 6562d3f15d080a3bbc54a9985c12eae5908a9980
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186657"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Stödmatris för utvärdering och migrering av Hyper-V

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera och migrera lokala virtuella Hyper-V-datorer.



## <a name="hyper-v-scenarios"></a>Hyper-V-scenarier

Tabellen sammanfattar scenarier som stöds för virtuella Hyper-V-datorer.

**Distribution** | **Information***
--- | ---
**Utvärdera lokala virtuella Hyper-V-datorer** | [Konfigurera](tutorial-prepare-hyper-v.md) din första utvärdering.<br/><br/> [Kör](scale-hyper-v-assessment.md) en storskalig bedömning.
**Migrera virtuella Hyper-V-datorer till Azure** | [Prova](tutorial-migrate-hyper-v.md) migrering till Azure.

## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Information**
--- | ---
Azure-behörigheter | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
Hyper-V:s virtuella datorer | Utvärdera upp till 35 000 virtuella Hyper-V-datorer i ett enda projekt. Du kan ha flera projekt i en Azure-prenumeration. Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.
Placering | [Granska](migrate-support-matrix.md#supported-geographies) stödda geografiska områden.


## <a name="assessment-hyper-v-host-requirements"></a>Bedömning-krav för Hyper-V-värd

| **Support**                | **Information**               
| :-------------------       | :------------------- |
| **Värd distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | Du behöver administratörs behörighet på Hyper-V-värden. <br/> Om du inte vill tilldela administratörs behörighet skapar du ett lokalt konto eller domän användar konto och lägger till användaren i dessa grupper – fjärrhanterings användare, Hyper-V-administratörer och användare av prestanda övervakning. |
| **Värd operativ system** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2.<br/> Du kan inte utvärdera virtuella datorer som finns på Hyper-V-värdar som kör Windows Server 2012. |
| **PowerShell-fjärrkommunikation**   | Måste vara aktiverat på varje värd. |
| **Hyper-V-replikering**       | Om du använder Hyper-V-replikering (eller om du har flera virtuella datorer med samma VM-identifierare) och identifierar både de ursprungliga och replikerade virtuella datorerna med hjälp av Azure Migrate, är det inte säkert att utvärderingen som genereras av Azure Migrate är korrekt. |


## <a name="assessment-hyper-v-vm-requirements"></a>Utvärdering – krav för virtuell Hyper-V-dator

| **Support**                  | **Information**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure. |
| **Integrerings tjänster**       | [Integrerings tjänsterna för Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på de virtuella datorer som du bedömer, för att kunna avbilda information om operativ systemet. |



## <a name="assessment-appliance-requirements"></a>Bedömnings krav

Vid utvärdering kör Azure Migrate en förenklad installation för att identifiera virtuella Hyper-V-datorer och skicka VM-metadata och prestanda data till Azure Migrate. Installationen körs på en virtuell Hyper-V-dator och du konfigurerar med en komprimerad virtuell Hyper-V-hårddisk som du hämtar från Azure Portal. I följande tabell sammanfattas kraven på installationen.

| **Support**                | **Information**               
| :-------------------       | :------------------- |
| **Distribution av utrustning**   |  Du distribuerar installationen som en virtuell Hyper-V-dator.<br/> Den virtuella dator som tillhandahålls av Azure Migrate är Hyper-V VM version 5,0.<br/> Hyper-V-värden måste köra Windows Server 2012 R2 eller senare.<br/> Värden behöver tillräckligt med utrymme för att allokera 16 GB RAM-minne, 8 virtuella processorer, runt 80 GB lagrings utrymme och en extern växel för den virtuella datorns dator.<br/> Enheten behöver en statisk eller dynamisk IP-adress och Internet åtkomst.
| **Azure Migrate projekt**  |  En apparat kan associeras med ett enda projekt.<br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> Du kan utvärdera upp till 35 000 virtuella datorer i ett projekt.
| **Hyper-V-värdar**          | En apparat kan ansluta till upp till 300 Hyper-V-värdar.
| **Identifikation**              | En enda apparat kan identifiera upp till 5000 virtuella datorer.
| **Utvärderings grupp**       | Du kan lägga till upp till 35 000 datorer i en enda grupp.
| **Utvärdering**             | Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.



## <a name="assessment-appliance-url-access"></a>URL-åtkomst för utvärderings utrustning

För att utvärdera de virtuella datorerna behöver Azure Migrate-enheten Internet anslutning.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad proxy ger du åtkomst till URL: erna i tabellen, vilket säkerställer att proxyn matchar eventuella CNAME-poster som tas emot vid sökning av URL: erna.
- Om du har en avlyssnings-proxy kan du behöva importera Server certifikatet från proxyservern till enheten.


**URL** | **Information**  
--- | ---
*.portal.azure.com | Navigering till Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Logga in till din Azure-prenumeration
*.microsoftonline.com <br/> *.microsoftonline-p.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
management.azure.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
dc.services.visualstudio.com | Loggning och övervakning
*.vault.azure.net | Hantera hemligheter i Azure Key Vault när du kommunicerar mellan installations programmet och tjänsten.
aka.ms/* | Tillåt åtkomst till aka-länkar.
https://download.microsoft.com/download/* | Tillåter hämtning från Microsoft Download-webbplatsen.



## <a name="assessment-port-requirements"></a>Utvärdering-port krav

I följande tabell sammanfattas port kraven för utvärdering.

**Anordningar** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ``` https://<appliance-ip-or-name>:44368 ```<br/> Utgående anslutningar på portarna 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**Hyper-V-värd/-kluster** | Inkommande anslutningar på WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta konfigurations-och prestanda-metadata för virtuella Hyper-V-datorer med en Common Information Model CIM-session.

## <a name="migration-limitations"></a>Migration-begränsningar
Du kan välja upp till 10 virtuella datorer på en gång för replikering. Om du vill migrera fler datorer kan du replikera i grupper om 10.

## <a name="migration-hyper-v-host-requirements"></a>Migration-krav för Hyper-V-värd

| **Support**                | **Information**               
| :-------------------       | :------------------- |
| **Värd distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | Du behöver administratörs behörighet på Hyper-V-värden. |
| **Värd operativ system** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migrering – krav för virtuell Hyper-V-dator

| **Support**                  | **Information**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure. |
| **Behörigheter**           | Du behöver administratörs behörighet för varje virtuell Hyper-V-dator som du vill utvärdera. |
| **Integrerings tjänster**       | [Integrerings tjänsterna för Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på de virtuella datorer som du bedömer, för att kunna avbilda information om operativ systemet. |
| **Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativ system:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> För andra operativ system måste du göra justeringar manuellt innan migreringen. Relevanta artiklar innehåller instruktioner om hur du gör detta. |
| **Linux-start**                 | Om/boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om/Boot är en del av rot-partitionen (/) bör partitionen/-partitionen finnas på OS-disken och inte omfatta andra diskar. |
| **UEFI-start**                  | Den migrerade virtuella datorn i Azure kommer automatiskt att konverteras till en virtuell dator med BIOS-start. Den virtuella datorn ska endast köra Windows Server 2012 och senare. OS-disken bör ha upp till fem partitioner eller färre och storleken på OS-disken måste vara mindre än 300 GB.
  |
| **Disk storlek**                  | 2 TB för OS-disken, 4 TB för data diskar.
| **Disk nummer** | Högst 16 diskar per virtuell dator.
| **Krypterade diskar/volymer**    | Stöds inte för migrering. |
| **RDM/passthrough-diskar**      | Stöds inte för migrering. |
| **Delad disk** | Virtuella datorer som använder delade diskar stöds inte för migrering.
| **MSNFS**                        | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte. |
| **-**                      | Virtuella datorer med iSCSI-mål stöds inte för migrering.
| **Mål disk**                | Du kan bara migrera till virtuella Azure-datorer med Managed disks. |
| **IPv6** | Stöds ej.
| **NIC-teamning** | Stöds ej.
| **Azure Site Recovery** | Du kan inte replikera med Azure Migrate Server-migrering om den virtuella datorn är aktive rad för replikering med Azure Site Recovery.





## <a name="migration-hyper-v-host-url-access"></a>Migrering – åtkomst till URL för Hyper-V-värd

I följande tabell sammanfattas URL-åtkomst kraven för Hyper-V-värdar.

**URL** | **Information**  
--- | ---
login.microsoftonline.com | Åtkomst kontroll och identitets hantering med hjälp av Active Directory.
*.backup.windowsazure.com | Överföring och samordning av replikeringsdata.
*.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net | Ladda upp data till lagrings konton.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
time.windows.com | Verifierar tidssynkronisering mellan system och global tid.

## <a name="migration-port-access"></a>Migrering-port åtkomst

I följande tabell sammanfattas port kraven för Hyper-V-värdar och virtuella datorer för VM-migrering.

**Anordningar** | **Anslutning**
--- | ---
Hyper-V-värdar/VM: ar | Utgående anslutningar på HTTPS-port 443 för att skicka VM-replikeringsdata till Azure Migrate.




## <a name="next-steps"></a>Nästa steg

[Förbered för migrering av Hyper-V VM](tutorial-prepare-hyper-v.md) .
