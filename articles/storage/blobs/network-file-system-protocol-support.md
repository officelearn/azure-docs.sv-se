---
title: Stöd för Network File System 3,0 i Azure Blob Storage (för hands version) | Microsoft Docs
description: Blob Storage stöder nu NFS-protokollet (Network File System) 3,0. Det här stödet gör att Linux-klienter kan montera en behållare i Blob Storage från en virtuell Azure-dator (VM) eller en lokal dator.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b76e25b2961bf3fb268da6622cbfcce3a50a3396
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100553"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>NFS (Network File System) 3,0 protokoll stöd i Azure Blob Storage (för hands version)

Blob Storage stöder nu NFS-protokollet (Network File System) 3,0. Det här stödet gör att Linux-klienter kan montera en behållare i Blob Storage från en virtuell Azure-dator (VM) eller en lokal dator. 

> [!NOTE]
> NFS 3,0 protokoll stöd i Azure Blob Storage finns i offentlig för hands version och är tillgängligt i följande regioner: USA, östra, USA, centrala och Kanada, centrala.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Allmänt arbets flöde: montera en lagrings konto behållare

För att montera en lagrings konto behållare måste du göra detta.

1. Registrera NFS 3,0-protokoll funktion med din prenumeration.

2. Kontrol lera att funktionen har registrerats.

3. Skapa ett Azure-Virtual Network (VNet).

4. Konfigurera nätverks säkerhet.

5. Skapa och konfigurera lagrings konto som bara accepterar trafik från det virtuella nätverket.

6. Skapa en behållare i lagrings kontot.

7. Montera behållaren.

Steg-för-steg-anvisningar finns i [montera Blob Storage på Linux med hjälp av Network File System (NFS) 3,0-protokollet (för hands version)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Det är viktigt att slutföra dessa uppgifter i rätt ordning. Du kan inte montera behållare som du skapar innan du aktiverar NFS 3,0-protokollet på ditt konto. När du har aktiverat NFS 3,0-protokollet på ditt konto kan du också inaktivera det.

## <a name="network-security"></a>Nätverkssäkerhet

Ditt lagrings konto måste finnas i ett VNet. Ett VNet gör det möjligt för klienter att ansluta på ett säkert sätt till ditt lagrings konto. Det enda sättet att skydda data i ditt konto är genom att använda ett VNet och andra nätverks säkerhets inställningar. Andra verktyg som används för att skydda data, inklusive konto nyckel auktorisering, Azure Active Directory (AD) säkerhet och åtkomst kontrol listor (ACL) stöds inte ännu i konton som har stöd för NFS 3,0-protokollet aktiverat. 

Mer information finns i [nätverks säkerhets rekommendationer för Blob Storage](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Nätverks anslutningar som stöds

En klient kan ansluta via en offentlig eller [privat slut punkt](../common/storage-private-endpoints.md)och kan ansluta från någon av följande nätverks platser:

- Det virtuella nätverk som du konfigurerar för ditt lagrings konto. 

  I den här artikeln hänvisar vi till det virtuella nätverket som det *primära virtuella*nätverket. Läs mer i [bevilja åtkomst från ett virtuellt nätverk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ett peer-kopplat VNet som finns i samma region som det primära virtuella nätverket.

  Du måste konfigurera ditt lagrings konto för att tillåta åtkomst till detta peered VNet. Läs mer i [bevilja åtkomst från ett virtuellt nätverk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ett lokalt nätverk som är anslutet till ditt primära VNet med hjälp av [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) eller en [ExpressRoute-Gateway](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager). 

  Mer information finns i [Konfigurera åtkomst från lokala nätverk](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Ett lokalt nätverk som är anslutet till ett peer-nätverk.

  Detta kan göras med hjälp av [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) eller en [ExpressRoute-Gateway](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) tillsammans med [Gateway-överföring](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Om du ansluter från ett lokalt nätverk kontrollerar du att klienten tillåter utgående kommunikation via portarna 111 och 2048. NFS 3,0-protokollet använder dessa portar.

## <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funktioner som ännu inte stöds

Följande Azure Storage funktioner stöds inte när du aktiverar NFS 3,0-protokollet på ditt konto. 

- Azure Active Directory (AD) säkerhet

- POSIX-liknande åtkomst kontrol listor (ACL: er)

- Möjligheten att aktivera NFS 3,0-stöd för befintliga lagrings konton

- Möjligheten att inaktivera NFS 3,0-stöd i ett lagrings konto (när du har aktiverat det)

- Möjlighet att skriva till blobbar med både NFS 3,0 och andra REST-API: er eller SDK: er. 

  Om du vill använda REST-API: er eller SDK: er för att skriva till en BLOB ska du kontrol lera att du bara använder NFS 3,0 för att utföra Läs åtgärder, annars kan blobbar skadas.

## <a name="nfs-30-features-not-yet-supported"></a>NFS 3,0-funktioner stöds ännu inte

Följande NFS 3,0-funktioner stöds ännu inte med Azure Data Lake Storage Gen2.

- NFS 3,0 över UDP. Endast NFS 3,0 över TCP stöds.

- Låsa filer med Network Lock Manager (NLM). Monterings kommandon måste innehålla `-o nolock` parametern.

- Montera under kataloger. Du kan bara montera rot katalogen (container).

- Visar monteringar (t. ex. genom att använda kommandot `showmount -a` )

- Lista över exporter (till exempel: genom att använda kommandot `showmount -e` )

- Exportera en behållare som skrivskyddad

## <a name="pricing"></a>Prissättning

Under för hands versionen debiteras de data som lagras i ditt lagrings konto med samma kapacitets taxa som Blob Storage-avgifter per GB per månad. 

En transaktion debiteras inte under för hands versionen. Prissättningen för transaktioner kan ändras och kommer att fastställas när den är allmänt tillgänglig.

## <a name="next-steps"></a>Nästa steg

För att komma igång, se [montera Blob Storage på Linux med hjälp av Network File System (NFS) 3,0-protokollet (för hands version)](network-file-system-protocol-support-how-to.md).





