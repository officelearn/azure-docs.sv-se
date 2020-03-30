---
title: Förutsättningar för Azure HPC-cache
description: Förutsättningar för att använda Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: 40d282ad30a800a5e5a36a8d2211ec8da7ce63ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271855"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Förutsättningar för Azure HPC-cache

Innan du använder Azure-portalen för att skapa en ny Azure HPC-cache, se till att din miljö uppfyller dessa krav.

## <a name="azure-subscription"></a>Azure-prenumeration

En betald prenumeration rekommenderas.

> [!NOTE]
> Under de första månaderna av GA-versionen måste Azure HPC-cacheteamet lägga till din prenumeration i åtkomstlistan innan den kan användas för att skapa en cacheinstans. Den här proceduren hjälper till att säkerställa att varje kund får svarstider av hög kvalitet från sina cacheminnen. Fyll [i det här formuläret](https://aka.ms/onboard-hpc-cache) för att begära åtkomst.

## <a name="network-infrastructure"></a>Nätverksinfrastruktur

Två nätverksrelaterade förutsättningar bör ställas in innan du kan använda cachen:

* Ett dedikerat undernät för Azure HPC Cache-instansen
* DNS-stöd så att cachen kan komma åt lagring och andra resurser

### <a name="cache-subnet"></a>Cacheundernät

Azure HPC-cachen behöver ett dedikerat undernät med följande egenskaper:

* Undernätet måste ha minst 64 IP-adresser tillgängliga.
* Undernätet kan inte vara värd för andra virtuella datorer, även för relaterade tjänster som klientdatorer.
* Om du använder flera Azure HPC-cacheinstanser behöver var och en sitt eget undernät.

Det bästa är att skapa ett nytt undernät för varje cache. Du kan skapa ett nytt virtuellt nätverk och ett nytt undernät som en del av skapandet av cacheminnet.

### <a name="dns-access"></a>DNS-åtkomst

Cachen behöver DNS för att komma åt resurser utanför det virtuella nätverket. Beroende på vilka resurser du använder kan du behöva konfigurera en anpassad DNS-server och konfigurera vidarebefordran mellan servern och Azure DNS-servrar:

* För att komma åt Azure Blob-lagringsslutpunkter och andra interna resurser behöver du den Azure-baserade DNS-servern.
* För att komma åt lokal lagring måste du konfigurera en anpassad DNS-server som kan matcha dina lagringsvärdnamn.

Om du bara behöver åtkomst till Blob-lagring kan du använda standard-DNS-servern som tillhandahålls av Azure för cacheminnet. Men om du behöver åtkomst till andra resurser bör du skapa en anpassad DNS-server och konfigurera den för att vidarebefordra alla Azure-specifika lösningsbegäranden till Azure DNS-servern.

En enkel DNS-server kan också användas för att ladda balans klientanslutningar bland alla tillgängliga cachemonteringspunkter.

Läs mer om virtuella Azure-nätverk och DNS-serverkonfigurationer i [Namnmatchning för resurser i virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Behörigheter

Kontrollera dessa behörighetsrelaterade förutsättningar innan du börjar skapa cacheminnet.

* Cacheinstansen måste kunna skapa nätverkskort (Virtual Network Interfaces). Användaren som skapar cachen måste ha tillräckliga privilegier i prenumerationen för att skapa nätverkskort.

* Om du använder Blob-lagring behöver Azure HPC-cachen auktorisering för att komma åt ditt lagringskonto. Använd rollbaserad åtkomstkontroll (RBAC) för att ge cacheåtkomst till din Blob-lagring. Två roller krävs: Storage Account Contributor och Storage Blob Data Contributor.

  Följ instruktionerna i [Lägg till lagringsmål](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) för att lägga till rollerna.

## <a name="storage-infrastructure"></a>Lagringsinfrastruktur

Cachen stöder Azure Blob-behållare eller NFS-maskinvarulagringsexport. Lägg till lagringsmål när du har skapat cacheminnet.

Varje lagringstyp har specifika förutsättningar.

### <a name="blob-storage-requirements"></a>Krav på bloblagring

Om du vill använda Azure Blob-lagring med cacheminnet behöver du ett kompatibelt lagringskonto och antingen en tom Blob-behållare eller en behållare som fylls med Azure HPC-cacheformaterade data enligt beskrivningen i [Flytta data till Azure Blob-lagring](hpc-cache-ingest.md).

Skapa kontot innan du försöker lägga till ett lagringsmål. Du kan skapa en ny behållare när du lägger till målet.

Om du vill skapa ett kompatibelt lagringskonto använder du följande inställningar:

* Prestanda: **Standard**
* Konto typ: **StorageV2 (allmänt ändamål v2)**
* Replikering: **Lokalt redundant lagring (LRS)**
* Åtkomstnivå (standard): **Hot**

Det är bra att använda ett lagringskonto på samma plats som cacheminnet.
<!-- clarify location - same region or same resource group or same virtual network? -->

Du måste också ge cacheprogrammet åtkomst till ditt Azure-lagringskonto som nämns i [Behörigheter](#permissions)ovan. Följ proceduren i [Lägg till lagringsmål](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) för att ge cacheminnet de åtkomstroller som krävs. Om du inte är lagringskontoägare ber du ägaren göra det här steget.

### <a name="nfs-storage-requirements"></a>NFS-lagringskrav

Om du använder ett NFS-lagringssystem (till exempel ett lokalt NAS-system för maskinvara) kontrollerar du att det uppfyller dessa krav. Du kan behöva arbeta med nätverksadministratörer eller brandväggshanterare för ditt lagringssystem (eller datacenter) för att verifiera dessa inställningar.

> [!NOTE]
> Lagringsmål skapas misslyckas om cachen har otillräcklig åtkomst till NFS lagringssystem.

Mer information ingår i [Felsökning av NAS-konfiguration och NFS-lagringsmålproblem](troubleshoot-nas.md).

* **Nätverksanslutning:** Azure HPC-cachen behöver nätverksåtkomst med hög bandbredd mellan cacheundernätet och NFS-systemets datacenter. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) eller liknande åtkomst rekommenderas. Om du använder en VPN kan du behöva konfigurera den för att klämma fast TCP MSS vid 1350 för att se till att stora paket inte är blockerade. Läs begränsningar för [VPN-paketstorlek](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) för ytterligare hjälp med felsökning av VPN-inställningar.

* **Tillträde till port:** Cachen behöver åtkomst till specifika TCP/UDP-portar i ditt lagringssystem. Olika typer av lagring har olika portkrav.

  Följ den här proceduren om du vill kontrollera inställningarna för lagringssystemet.

  * Utfärda `rpcinfo` ett kommando till ditt lagringssystem för att kontrollera de nödvändiga portarna. Kommandot nedan visar portarna och formaterar relevanta resultat i en tabell. (Använd systemets IP-adress i stället för *<storage_IP>* sikt.)

    Du kan utfärda det här kommandot från alla Linux-klienter som har NFS-infrastruktur installerad. Om du använder en klient i klustrets undernät kan den också hjälpa till att verifiera anslutningen mellan undernätet och lagringssystemet.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Kontrollera att alla portar som ``rpcinfo`` returneras av frågan tillåter obegränsad trafik från Azure HPC-cachens undernät.

  * Förutom de portar som `rpcinfo` returneras av kommandot kontrollerar du att dessa vanliga portar tillåter inkommande och utgående trafik:

    | Protokoll | Port  | Tjänst  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind (rpcbind)  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr (nlockmgr) |
    | TCP/UDP  | 4046  | monterad   |
    | TCP/UDP  | 4047  | status   |

  * Kontrollera brandväggsinställningarna för att vara säker på att de tillåter trafik på alla dessa nödvändiga portar. Var noga med att kontrollera brandväggar som används i Azure samt lokala brandväggar i ditt datacenter.

* **Tillgång till katalog:** Aktivera `showmount` kommandot på lagringssystemet. Azure HPC Cache använder det här kommandot för att kontrollera att din konfiguration för lagringsmål pekar på en giltig export och även för att se till att flera fästen inte kommer åt samma underkataloger (en risk för filkollision).

  > [!NOTE]
  > Om NFS-lagringssystemet använder NetApps ONTAP 9.2-operativsystem **ska du inte aktivera `showmount` **. [Kontakta Microsoft Service och support](hpc-cache-support-ticket.md) för hjälp.

  Läs mer om kataloglista åtkomst i [felsökningsartikeln](troubleshoot-nas.md#enable-export-listing)för NFS-lagringsmål .

* **Root-åtkomst:** Cachen ansluter till backend-systemet som användar-ID 0. Kontrollera de här inställningarna på ditt lagringssystem:
  
  * Aktivera `no_root_squash`. Det här alternativet säkerställer att fjärrrotanvändaren kan komma åt filer som ägs av root.

  * Kontrollera exportprinciper för att se till att de inte innehåller begränsningar för rotåtkomst från cachens undernät.

  * Om lagringen har export som är underkataloger för en annan export kontrollerar du att cachen har root-åtkomst till det lägsta segmentet i sökvägen. Läs [Root-åtkomst på katalogsökvägar](troubleshoot-nas.md#allow-root-access-on-directory-paths) i felsökningsartikeln för NFS-lagringsmål för mer information.

* NFS backend-lagring måste vara en kompatibel maskinvaru-/programvaruplattform. Kontakta Azure HPC-cacheteamet för mer information.

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure HPC-cacheinstans](hpc-cache-create.md) från Azure-portalen
