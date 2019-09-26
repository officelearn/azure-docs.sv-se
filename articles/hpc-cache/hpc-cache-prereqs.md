---
title: Förutsättningar för Azure HPC-cache (för hands version)
description: Krav för att använda Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: fab85785ea183736b4012c349af143ef3a8c784a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299916"
---
# <a name="prerequisites-for-azure-hpc-cache-preview"></a>Krav för Azure HPC cache (för hands version)

Innan du använder Azure Portal för att skapa en ny Azure HPC-cache kontrollerar du att din miljö uppfyller dessa krav.

## <a name="azure-subscription"></a>Azure-prenumeration

En betald prenumeration rekommenderas.

> [!NOTE]
> I för hands versionen måste Azure HPC-teamet lägga till din prenumeration i åtkomst listan innan den kan användas för att skapa en cache-instans. Med den här proceduren ser du till att varje kund får svars tider med hög kvalitet från testcacheminnen. Fyll i [det här formuläret](https://aka.ms/onboard-hpc-cache) om du vill begära åtkomst.

## <a name="network-infrastructure"></a>Nätverks infrastruktur

Två nätverksrelaterade krav bör ställas in innan du kan använda din cache:

* Ett dedikerat undernät för Azure HPC cache-instansen
* DNS-stöd så att cachen kan komma åt lagring och andra resurser

### <a name="cache-subnet"></a>Cache-undernät

Azure HPC-cachen behöver ett dedikerat undernät med följande kvaliteter:

* Under nätet måste ha minst 64 IP-adresser tillgängliga.
* Under nätet kan inte vara värd för andra virtuella datorer, även för relaterade tjänster som klient datorer.
* Om du använder flera Azure HPC cache-instanser behöver var och en av sina egna undernät.

Det bästa sättet är att skapa ett nytt undernät för varje cache. Du kan skapa ett nytt virtuellt nätverk och undernät som en del av att skapa cachen.

### <a name="dns-access"></a>DNS-åtkomst

Cachen behöver DNS för att få åtkomst till resurser utanför det virtuella nätverket. Beroende på vilka resurser du använder kan du behöva konfigurera en anpassad DNS-server och konfigurera vidarebefordran mellan servern och Azure DNS servrar:

* För att få åtkomst till Azure Blob Storage-slutpunkter och andra interna resurser behöver du den Azure-baserade DNS-servern.
* För att komma åt lokal lagring måste du konfigurera en anpassad DNS-server som kan matcha dina lagrings-värdnamn.

Om du bara behöver åtkomst till Blob Storage kan du använda standard-Azure-standardservern för cacheminnet. Men om du behöver åtkomst till andra resurser bör du skapa en anpassad DNS-server och konfigurera den så att den vidarebefordrar alla Azure-speciella matchnings begär anden till Azure DNS-servern. (En enkel DNS-server kan också användas för att belastningsutjämna klient anslutningar mellan alla tillgängliga cache-nätplatser.)

Lär dig mer om Azure Virtual Networks och DNS-serverkonfigurationer i [namn matchning för resurser i virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Behörigheter

Kontrol lera de här behörighets kraven innan du börjar skapa din cache.

* Cache-instansen måste kunna skapa virtuella nätverks gränssnitt (NIC). Den användare som skapar cachen måste ha tillräcklig behörighet i prenumerationen för att skapa nätverkskort.
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* Om du använder Blob Storage behöver Azure HPC cache auktorisering för att komma åt ditt lagrings konto. Du kan använda rollbaserad åtkomst kontroll (RBAC) för att ge cache åtkomst till Blob Storage. Två roller krävs: Lagrings konto deltagare och data deltagare i Storage blob. Följ instruktionerna i [Lägg till lagrings mål](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) för att lägga till rollerna.

## <a name="storage-infrastructure"></a>Lagrings infrastruktur

Cachen stöder export av Azure Blob-behållare eller NFS-maskinvara. Du kan definiera lagrings mål när du skapar cachen, men du kan även lägga till lagrings utrymme efteråt.

Varje lagrings typ har särskilda krav. 

### <a name="nfs-storage-requirements"></a>Krav för NFS-lagring

Om du använder den lokala maskin varu lagringen måste cacheminnet ha nätverks åtkomst med hög bandbredd till data centret från dess undernät. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) eller liknande åtkomst rekommenderas.

NFS-backend-lagring måste vara en kompatibel maskin-/program varu plattform. Kontakta Azure HPC-teamet för mer information.

### <a name="blob-storage-requirements"></a>Blob Storage-krav

Om du vill använda Azure Blob Storage med ditt cacheminne behöver du ett kompatibelt lagrings konto och antingen en tom BLOB-behållare eller en behållare som är ifylld med Azure HPC cache-formaterade data enligt beskrivningen i [Flytta data till Azure Blob Storage](hpc-cache-ingest.md).

Skapa kontot och behållaren innan du försöker lägga till det som ett lagrings mål.

Använd följande inställningar om du vill skapa ett kompatibelt lagrings konto:

* Historik **Standard**
* Konto typ: **StorageV2 (generell användning v2)**
* Replikering **Lokalt redundant lagring (LRS)**
* Åtkomst nivå (standard): **hot**

Det är en bra idé att använda ett lagrings konto på samma plats som din cache.
<!-- need to clarify location - same region or same resource group or same virtual network? -->

Du måste också ge cache-programmet åtkomst till ditt Azure Storage-konto. Följ beskrivningen i [Lägg till lagrings mål](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) för att ge cachen åtkomst roller till lagrings konto deltagare och lagrings-BLOB-datadeltagare. Om du inte är lagrings kontots ägare kan du låta ägaren göra detta steg.

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure HPC cache-instans](hpc-cache-create.md) från Azure Portal
