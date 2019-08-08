---
title: Azure Migrate support mat ris för Hyper-V-utvärdering och migrering
description: Sammanfattar inställningar och begränsningar för Hyper-V-utvärdering och migrering med hjälp av tjänsten Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 105cbf173a9abe1adf0999f63740d47b3da51a29
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856288"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Stödmatris för utvärdering och migrering av Hyper-V

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera och migrera lokala virtuella Hyper-V-datorer.



## <a name="hyper-v-scenarios"></a>Hyper-V-scenarier

Tabellen sammanfattar scenarier som stöds för virtuella Hyper-V-datorer.

**Distribution** | **Information***
--- | ---
**Utvärdera lokala virtuella Hyper-V-datorer** | [Konfigurera](tutorial-prepare-hyper-v.md) din första utvärdering.<br/><br/> [Kör](scale-hyper-v-assessment.md) en storskalig bedömning.
**Migrera virtuella Hyper-V-datorer till Azure** | [Prova](tutorial-migrate-hyper-v.md) migrering till Azure. 

Migrering av Hyper-V-servrar som hanteras med System Center Virtual Machine Manager (VMM) stöds inte av Azure Migrate Server-migrering. 

## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Detaljer**
--- | ---
Azure-behörigheter | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
Hyper-V:s virtuella datorer | Utvärdera upp till 35 000 virtuella Hyper-V-datorer i ett enda projekt. Du kan ha flera projekt i en Azure-prenumeration. Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.
Geografi | Du kan skapa Azure Migrate projekt i ett antal geografiska områden. Även om du kan skapa projekt i specifika ographies kan du utvärdera eller migrera datorer för andra mål platser. Projektets geografi används bara för att lagra identifierade metadata.

  **Geografi** | **Lagrings plats för metadata**
  --- | ---
  Azure Government | Virginia (USA-förvaltad region)
  Asien och stillahavsområdet | Asien, östra eller Sydostasien
  Australien | Östra Australien eller Australien, sydöstra
  Kanada | Kanada, centrala eller Kanada, öst
  Europa | Europa, norra eller Europa, västra
  Indien | Centrala Indien eller södra Indien
  Japan |  Japan, östra eller Japan, väst
  Storbritannien och Nordirland | Storbritannien, södra eller Storbritannien, västra
  USA | USA, centrala eller västra USA 2


 > [!NOTE]
 > Stöd för Azure Government är för närvarande bara tillgängligt för den [äldre versionen](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate.


## <a name="assessment-hyper-v-host-requirements"></a>Bedömning-krav för Hyper-V-värd

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Värd distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | Du behöver administratörs behörighet på Hyper-V-värden. |
| **Värd operativ system** | Windows Server 2016 eller Windows Server 2012 R2.<br/> Du kan inte utvärdera virtuella datorer som finns på Hyper-V-värdar som kör Windows Server 2019. |
| **PowerShell-fjärrkommunikation**   | Måste vara aktiverat på varje värd. |
| **Hyper-V-replikering**       | Om du använder Hyper-V-replikering (eller om du har flera virtuella datorer med samma VM-identifierare) och identifierar både de ursprungliga och replikerade virtuella datorerna med hjälp av Azure Migrate, är det inte säkert att utvärderingen som genereras av Azure Migrate är korrekt. |


## <a name="assessment-hyper-v-vm-requirements"></a>Utvärdering – krav för virtuell Hyper-V-dator

| **Support**                  | **Detaljer**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure. |
| **Behörigheter**           | Du behöver administratörs behörighet för varje virtuell Hyper-V-dator som du vill utvärdera. |
| **Integrerings tjänster**       | [Integrerings tjänsterna för Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på de virtuella datorer som du bedömer, för att kunna avbilda information om operativ systemet. |
| **UEFI-start**                  | Virtuella datorer med UEFI-start stöds inte för migrering. |
| **Krypterade diskar/volymer**    | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering. |
| **RDM/passthrough-diskar**      | Om de virtuella datorerna har RDM-eller passthrough-diskar replikeras inte dessa diskar till Azure. |
| **NFS**                        | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte. |
| **Mål disk**                | Azure Migrate bedömningar rekommenderar endast migrering till virtuella Azure-datorer med Managed disks. |


## <a name="assessment-appliance-requirements"></a>Bedömnings krav

Vid utvärdering kör Azure Migrate en förenklad installation för att identifiera virtuella Hyper-V-datorer och skicka VM-metadata och prestanda data till Azure Migrate. Installationen körs på en virtuell Hyper-V-dator och du konfigurerar med en komprimerad virtuell Hyper-V-hårddisk som du hämtar från Azure Portal. I följande tabell sammanfattas kraven på installationen.

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Azure Migrate projekt**  |  En apparat kan associeras med ett enda projekt.<br/> Du kan identifiera upp till 5000 virtuella Hyper-V-datorer med en enda apparat.
| **Hyper-V**    |  Du distribuerar installationen som en virtuell Hyper-V-dator.<br/> Den virtuella dator som tillhandahålls är Hyper-V VM version 5,0.<br/> Den virtuella datorns värd måste köra Windows Server 2012 R2 eller senare.<br/> Det behöver tillräckligt med utrymme för att allokera 16 GB RAM, 8 virtuella processorer och 1 extern växel för den virtuella datorn.<br/> Enheten kräver en statisk eller dynamisk IP-adress och Internet åtkomst.


## <a name="assessment-appliance-url-access"></a>URL-åtkomst för utvärderings utrustning

För att utvärdera de virtuella datorerna behöver Azure Migrate-enheten Internet anslutning.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad proxy ger du åtkomst till URL: erna i tabellen, vilket säkerställer att proxyn matchar eventuella CNAME-poster som tas emot vid sökning av URL: erna.
- Om du har en avlyssnings-proxy kan du behöva importera Server certifikatet från proxyservern till enheten.


**URL** | **Detaljer**  
--- | ---
*.portal.azure.com | Navigering till Azure Portal
*.windows.net | Logga in till din Azure-prenumeration
*.microsoftonline.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
management.azure.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
dc.services.visualstudio.com | Loggning och övervakning
*.vault.azure.net | Hantera hemligheter i Azure Key Vault när du kommunicerar mellan installations programmet och tjänsten.
aka.ms/* | Tillåt åtkomst till aka-länkar.
https://download.microsoft.com/download/* | Tillåter hämtning från Microsoft Download-webbplatsen.



## <a name="assessment-port-requirements"></a>Utvärdering-port krav

I följande tabell sammanfattas port kraven för utvärdering.

**Anordningar** | **anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med hjälp av URL: en:``` https://<appliance-ip-or-name>:44368 ```<br/> Utgående anslutningar på port 443 för att skicka metadata för identifiering och prestanda till Azure Migrate.
**Hyper-V-värd/-kluster** | Inkommande anslutningar på WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta konfigurations-och prestanda-metadata för virtuella Hyper-V-datorer med en Common Information Model CIM-session.

## <a name="migration-hyper-v-host-requirements"></a>Migration-krav för Hyper-V-värd

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Värd distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | Du behöver administratörs behörighet på Hyper-V-värden. |
| **Värd operativ system** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migrering – krav för virtuell Hyper-V-dator

| **Support**                  | **Detaljer**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem som stöds av Azure. |
| **Behörigheter**           | Du behöver administratörs behörighet för varje virtuell Hyper-V-dator som du vill utvärdera. |
| **Integrerings tjänster**       | [Integrerings tjänsterna för Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på de virtuella datorer som du bedömer, för att kunna avbilda information om operativ systemet. |
| **Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativ system:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> För andra operativ system måste du göra justeringar manuellt innan migreringen. Relevanta artiklar innehåller instruktioner om hur du gör detta. |
| **Linux-start**                 | Om/boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om/Boot är en del av rot-partitionen (/) bör partitionen/-partitionen finnas på OS-disken och inte omfatta andra diskar. |
| **UEFI-start**                  | Virtuella datorer med UEFI-start stöds inte för migrering.  |
| **Disk storlek**                  | 2 TB för OS-disken, 4 TB för data diskar.
| **Disk nummer** | Högst 16 diskar per virtuell dator.
| **Krypterade diskar/volymer**    | Stöds inte för migrering. |
| **RDM/passthrough-diskar**      | Stöds inte för migrering. |
| **Delad disk** | Virtuella datorer som använder delade diskar stöds inte för migrering.
| **NFS**                        | NFS-volymer som monterats som volymer på de virtuella datorerna replikeras inte. |
| **-**                      | Virtuella datorer med iSCSI-mål stöds inte för migrering.
| **Mål disk**                | Du kan bara migrera till virtuella Azure-datorer med Managed disks. |
| **IPv6** | Stöds ej.
| **NIC-teamning** | Stöds ej.
| **Azure Site Recovery** | Du kan inte replikera med Azure Migrate Server-migrering om den virtuella datorn är aktive rad för replikering med Azure Site Recovery.





## <a name="migration-hyper-v-host-url-access"></a>Migrering – åtkomst till URL för Hyper-V-värd

I följande tabell sammanfattas URL-åtkomst kraven för Hyper-V-värdar.

**URL** | **Detaljer**  
--- | ---
login.microsoftonline.com | Åtkomst kontroll och identitets hantering med hjälp av Active Directory.
*.backup.windowsazure.com | Överföring och samordning av replikeringsdata.
*.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate tjänst-URL: er.
*.blob.core.windows.net | Ladda upp data till lagrings konton.
dc.services.visualstudio.com | Ladda upp program loggar som används för intern övervakning.
time.windows.com | Verifierar tidssynkronisering mellan system och global tid.

## <a name="migration-port-access"></a>Migrering-port åtkomst

I följande tabell sammanfattas port kraven för Hyper-V-värdar och virtuella datorer för VM-migrering.

**Anordningar** | **anslutning**
--- | ---
Hyper-V-värdar/VM: ar | Utgående anslutningar på HTTPS-port 443 för att skicka VM-replikeringsdata till Azure Migrate.




## <a name="next-steps"></a>Nästa steg

[Förbered för migrering av Hyper-V VM](tutorial-prepare-hyper-v.md) .
