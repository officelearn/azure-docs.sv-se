---
title: Azure Migrate stöd matrix för Hyper-V-bedömning och migrering
description: Sammanfattar inställningar och begränsningar för Hyper-V-bedömning och migrering med hjälp av Azure Migrate-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811354"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Stödmatris för Hyper-V-bedömning och migrering

Du kan använda den [Azure Migrate-tjänsten](migrate-overview.md) att utvärdera och migrera datorer till Microsoft Azure-molnet. Den här artikeln sammanfattas support inställningar och begränsningar för att utvärdera och migrera lokala virtuella Hyper-V-datorer.



## <a name="hyper-v-scenarios"></a>Hyper-V-scenarier

Tabellen beskriver scenarier som stöds för Hyper-V-datorer.

**Distribution** | **Information om*** 
--- | --- 
**Utvärdera lokala virtuella Hyper-V-datorer** | [Konfigurera](tutorial-prepare-hyper-v.md) din första utvärdering.<br/><br/> [Kör](scale-hyper-v-assessment.md) en storskalig utvärdering.
**Migrera Hyper-V-datorer till Azure** | [Prova att använda](tutorial-migrate-hyper-v.md) migrering till Azure.

    

## <a name="azure-migrate-projects"></a>Azure Migrate-projekt

**Support** | **Detaljer**
--- | ---
Azure-behörigheter | Du behöver deltagare eller ägare behörigheter i prenumerationen för att skapa ett Azure Migrate-projekt.
Hyper-V:s virtuella datorer | Utvärdera upp till 10 000 Hyper-V-datorer i ett enda projekt.

Ett projekt kan innehålla både virtuella VMware-datorer och Hyper-V-datorer till en gräns för utvärdering.


## <a name="assessment-hyper-v-host-requirements"></a>Värdkrav för utvärdering av Hyper-V

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Distributionen av värden**       | Hyper-V-värden kan vara fristående eller distribueras i ett kluster. |
| **Behörigheter**           | Du måste ha administratörsbehörighet på Hyper-V-värden. |
| **Värdoperativsystem** | Windows Server 2016 eller Windows Server 2012 R2.<br/> Du kan inte utvärdera virtuella dator på Hyper-V-värdar som kör Windows Server 2019. |
| **PowerShell-fjärrkommunikation**   | Måste vara aktiverat på varje värd. |
| **Hyper-V Replica**       | Om du använder Hyper-V-replikering (eller du har flera virtuella datorer med samma VM-identifierare) och identifiera både de ursprungliga och replikerade virtuella datorer med hjälp av Azure Migrate, utvärderingen genereras av Azure Migrate kanske inte är korrekt. |


## <a name="assessment-hyper-v-vm-requirements"></a>Kraven för utvärdering av Hyper-V virtuella datorer

| **Support**                  | **Detaljer**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operativsystem som stöds av Azure. |
| **Behörigheter**           | Du måste ha administratörsbehörighet på varje Hyper-V virtuell dator som du vill utvärdera. |
| **Integreringstjänster**       | [Hyper-V-integreringstjänsterna](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på virtuella datorer som du har utvärderat, för att samla in information om operativsystemet. |
| **Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativsystem:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> För andra operativsystem som du behöver justera manuellt före migreringen. Artiklarna innehåller instruktioner om hur du gör detta. |
| **Linux-Start**                 | Om/Boot finns på en särskild partition, bör finnas på OS-disken och sprids inte över flera diskar.<br/> Om/Boot är en del av rotpartitionen (/), ska sedan '/' partitionen vara på OS-disken och inte omfatta andra diskar. |
| **UEFI-Startmetod**                  | Virtuella datorer med UEFI boot stöds inte för migrering. |
| **Krypterade diskar/volymer**    | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering. |
| **RDM/genomströmningsdiskar**      | Om virtuella datorer har RDM eller genomströmning diskar, replikeras inte dessa diskar till Azure. |
| **NFS**                        | NFS-volymer monterade volymer på de virtuella datorerna replikeras inte. |
| **Måldisken**                | Azure Migrate-utvärderingar rekommenderar migrering till virtuella Azure-datorer med hanterade diskar. |


## <a name="assessment-appliance-requirements"></a>Krav för utvärdering av enhet

För utvärdering av körs Azure Migrate en enkel installation för att identifiera Hyper-V-datorer och skicka data för VM-metadata och prestanda till Azure Migrate. Installationen körs på en Hyper-V virtuell dator och du har konfigurerat med en komprimerad Hyper-V virtuell Hårddisk som du laddar ned från Azure-portalen. I följande tabell sammanfattas kraven för installation.

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Azure Migrate-projekt**  |  En apparat kan associeras med ett enda projekt.<br/> Du kan identifiera upp till 5 000 Hyper-V-datorer med en enda installation.
| **Hyper-V-begränsningar**    |  Du kan distribuera installationen för som en Hyper-V virtuell dator.<br/> Installation VM som tillhandahålls är Hyper-V VM version 5.0.<br/> VM-värden måste köra Windows Server 2012 R2 eller senare.<br/> Den behöver tillräckligt med utrymme för att allokera 16 GB RAM, 4 virtuella processorer och 1 externt växlar för VM-enhet för.<br/> Installationen kräver en statisk eller dynamisk IP-adress och internet-åtkomst.
| **Hyper-V-installation**      |  Installationen har ställts in som en Hyper-V virtuell dator.<br/> Den virtuella Hårddisken som finns att hämta är Hyper-V VM version 5.0.
| **Värd**                   | VM-värd som kör installationen virtuella datorn måste köra Windows Server 2012 R2 eller senare.<br/> Den behöver tillräckligt med utrymme för att allokera 16 GB RAM-minne, 4 virtuella processorer och en extern växel för den VM-installationen.<br/> Installationen kräver en statisk eller dynamisk IP-adress och internet-åtkomst. |
| **Migreringsstöd**      | Om du vill starta replikering av virtuella datorer, The migrering Gateway-tjänsten på enheten som måste vara 1.18.7141.12919 eller senare. Logga in på webbappen installationen Kontrollera versionen. |

## <a name="assessment-appliance-url-access"></a>Utvärdering av installation URL-åtkomst

Om du vill utvärdera virtuella datorer behöver Azure Migrate-installation Internetanslutning.

- När du distribuerar installationen gör Azure Migrate en anslutningskontroll URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad firewall.proxy Tillåt åtkomst till URL: er i, se till att proxyn löser eventuella CNAME-poster har tagits emot vid Leta upp de URL: er.
- Om du har en spärrande proxy kan behöva du importera certifikatet från proxyservern till installationen. 

    
**URL** | **Detaljer**  
--- | --- 
*.portal.azure.com | Navigering på Azure Portal
*.windows.net | Logga in till din Azure-prenumeration
*.microsoftonline.com | Skapandet av Azure Active Directory-program för installation för att meddelanden om tjänsten.
management.azure.com | Skapandet av Azure Active Directory-program för installation för att meddelanden om tjänsten.
dc.services.visualstudio.com | Loggning och övervakning 
*.vault.azure.net | Hantera hemligheter i Azure Key Vault vid kommunikation mellan enheten och tjänsten.


## <a name="assessment-port-requirements"></a>Utvärdering av portkraven

I följande tabell sammanfattas portkraven för utvärdering.

**enheten** | **anslutning**
--- | --- 
**Installation** | Inkommande anslutningar på TCP-port 3389 för att tillåta anslutningar till fjärrskrivbord till installationen.<br/> Inkommande anslutningar på port 44368 fjärråtkomst till installation av appen med URL: en: https://<appliance-ip-or-name>:44368<br/> Utgående anslutningar på port 443 för att skicka metadata för identifiering och prestanda till Azure Migrate.
**Hyper-V-värd/kluster** | Inkommande anslutningar på WinRM-port 5985 (HTTP) och 5986 (HTTPS) för att hämta metadata för konfiguration och prestanda för Hyper-V-datorer med hjälp av en Common Information Model (CIM)-session.

## <a name="migration-hyper-v-host-requirements"></a>Krav för migrering-Hyper-V-värd

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Distributionen av värden**       | Hyper-V-värden kan vara fristående eller distribueras i ett kluster. |
| **Behörigheter**           | Du måste ha administratörsbehörighet på Hyper-V-värden. |
| **Värdoperativsystem** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Kraven för migrering Hyper-V virtuella datorer

| **Support**                  | **Detaljer**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operativsystem som stöds av Azure. |
| **Behörigheter**           | Du måste ha administratörsbehörighet på varje Hyper-V virtuell dator som du vill utvärdera. |
| **Integreringstjänster**       | [Hyper-V-integreringstjänsterna](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på virtuella datorer som du har utvärderat, för att samla in information om operativsystemet. |
| **Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för följande operativsystem:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> För andra operativsystem som du behöver justera manuellt före migreringen. Artiklarna innehåller instruktioner om hur du gör detta. |
| **Linux-Start**                 | Om/Boot finns på en särskild partition, bör finnas på OS-disken och sprids inte över flera diskar.<br/> Om/Boot är en del av rotpartitionen (/), ska sedan '/' partitionen vara på OS-disken och inte omfatta andra diskar. |
| **UEFI-Startmetod**                  | Virtuella datorer med UEFI boot stöds inte för migrering. |
| **Krypterade diskar/volymer**    | Virtuella datorer med krypterade diskar/volymer stöds inte för migrering. |
| **RDM/genomströmningsdiskar**      | Om virtuella datorer har RDM eller genomströmning diskar, replikeras inte dessa diskar till Azure. |
| **NFS**                        | NFS-volymer monterade volymer på de virtuella datorerna replikeras inte. |
| **Måldisken**                | Du kan migrera till Azure virtuella datorer med hanterade diskar. |




## <a name="migration-hyper-v-host-url-access"></a>Migrering – Hyper-V-värd URL-åtkomst

I följande tabell sammanfattas URL åtkomstkraven för Hyper-V-värdar.

**URL** | **Detaljer**  
--- | ---
login.microsoftonline.com | Access control och Identitetshantering hantering med Active Directory.
*.backup.windowsazure.com | Överföring av replikeringsdata och koordination.
*.hypervrecoverymanager.windowsazure.com | Anslut till Azure Migrate-tjänstens URL.
*.blob.core.windows.net | Ladda upp data till storage-konton.
dc.services.visualstudio.com | Ladda upp appen loggar som används för intern övervakning.
time.windows.com | Verifierar tidssynkronisering mellan system och global tid.

## <a name="migration-port-access"></a>Portåtkomst för migrering

I följande tabell sammanfattas portkraven på Hyper-V-värdar och virtuella datorer för VM-migrering.

**enheten** | **anslutning**
--- | --- 
Hyper-V-värdar/virtuella datorer | Utgående anslutningar på HTTPS-port 443 för att skicka data för VM-replikering till Azure Migrate.

  
## <a name="migration-vm-disk-support"></a>Stöd för migrering-VM-diskar 

**Support** | **Detaljer**
--- | ---
Migrerade diskar | Virtuella datorer kan bara migreras till managed disks (standard HHD, premium SSD) i Azure.
   

## <a name="next-steps"></a>Nästa steg

[Förbereda för Hyper-V VM-utvärdering](tutorial-prepare-hyper-v.md) för migrering.




 
