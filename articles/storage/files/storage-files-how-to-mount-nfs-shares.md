---
title: Montera en fil resurs i Azure NFS – Azure Files
description: Lär dig hur du monterar en nätverks fil system resurs.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 530ae82720e6b4eb6a3e4d1021c0b37b9f4dbf5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707449"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Montera en NFS-filresurs

[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner med antingen SMB-protokollet (Server Message Block Protocol) eller NFS-protokollet (Network File System). Den här artikeln fokuserar på montering med NFS. mer information om hur du monterar med SMB finns i [använda Azure Files med Linux](storage-how-to-use-files-linux.md). Mer information om var och en av de tillgängliga protokollen finns i [Azure File Share Protocols](storage-files-compare-protocols.md).

## <a name="limitations"></a>Begränsningar

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Skapa en NFS-resurs](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > NFS-resurser kan bara nås från betrodda nätverk. Anslutningar till NFS-resursen måste härstamma från någon av följande källor:

- Använd någon av följande nätverks lösningar:
    - [Skapa antingen en privat slut punkt](storage-files-networking-endpoints.md#create-a-private-endpoint) (rekommenderas) eller [begränsa åtkomsten till din offentliga slut punkt](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Konfigurera en punkt-till-plats (P2s) VPN på Linux som ska användas med Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Konfigurera en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).
    - Konfigurera [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Inaktivera säker överföring

1. Logga in på Azure Portal och få åtkomst till det lagrings konto som innehåller den NFS-resurs som du skapade.
1. Välj **konfiguration**.
1. Välj **inaktive rad** för **säker överföring krävs**.
1. Välj **Spara**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Skärm bild av skärmen för konfiguration av lagrings konto med säker överföring inaktive rad.":::

## <a name="mount-an-nfs-share"></a>Montera en NFS-resurs

1. När fil resursen har skapats väljer du resursen och väljer **Anslut från Linux**.
1. Ange den monterings Sök väg som du vill använda och kopiera skriptet.
1. Anslut till klienten och Använd det tillhandahållna monterings skriptet.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Skärm bild av skärmen för konfiguration av lagrings konto med säker överföring inaktive rad.":::

Nu har du monterat din NFS-resurs.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Files med vår artikel, [Planera för en Azure Files distribution](storage-files-planning.md).
- Om det uppstår några problem kan du läsa [Felsöka Azure NFS-filresurser](storage-troubleshooting-files-nfs.md).