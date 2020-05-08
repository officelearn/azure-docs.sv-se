---
title: Koncept – lagring
description: Lär dig mer om de viktigaste lagrings funktionerna i Azure VMware-lösningen (AVS) för hands versioner av privata moln.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7e58a829a40d590b7936a58ccdc866211a4f5cb4
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740361"
---
# <a name="azure-vmware-solution-avs-preview-storage-concepts"></a>Azure VMware Solution (AVS)-förhands granskning av lagrings koncept

AVS-privata moln tillhandahåller inbyggd lagring i hela klustret med VMware virtuellt San. All lokal lagring från varje värd i ett kluster används i ett virtuellt San-datalager och kryptering av data på rest är tillgänglig och aktive ras som standard. Du kan använda Azure Storage resurser för att utöka lagrings funktionerna i dina privata moln.

## <a name="vsan-clusters"></a>Virtuellt San-kluster

Lokal lagring i varje kluster värd används som en del av ett virtuellt San-datalager. Alla diskgroups använder en NVMe-cachenivå på 1,6 TB med RAW, per värd, SSD-baserad kapacitet på 15,4 TB. Storleken på nivån för ett klusters RAW-kapacitet är antalet värdar per värd gånger antalet värdar. Ett fyra värd kluster ger till exempel 61,6 TB RAW-kapacitet på kapacitets nivån virtuellt San.

Lokal lagring i kluster värdar används i virtuellt San data lager för hela klustret. Alla data lager skapas som en del av en distribution av privata moln och är tillgängliga för användning omedelbart. Cloudadmin-användaren och alla användare i gruppen CloudAdmin kan hantera data lager med följande virtuellt San-behörigheter:
- Data lager. AllocateSpace
- Datastore.Browse
- Data lager. config
- Data lager. DeleteFile
- Data lager. FileManagement
- Data lager. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Kryptering av data i vila

Virtuellt San-datalager använder kryptering av data i vila som standard. Krypterings lösningen är KMS-baserad och stöder vCenter-åtgärder för nyckel hantering. Nycklar lagras krypterade, omslutna av en HSM-baserad Azure Key Vault huvud nyckel. När en värd tas bort från ett kluster kan data på SSD ogiltig förklaras omedelbart.

## <a name="scaling"></a>Skalning

Intern kluster lagrings kapacitet skalas genom att lägga till värdar i ett kluster. För kluster som använder våra värdar ökas kapaciteten för den obearbetade klustret med 15,4 TB med varje ytterligare värd. Kluster som har skapats med GP-värdar har sin RAW-kapacitet ökad med 7,7 TB med varje ytterligare värd. I båda typerna av kluster tar värdar cirka 10 minuter att lägga till i ett kluster. Mer information om hur du skalar kluster finns i själv studie kursen [skala privat moln] [självstudie-Scale-Private-Cloud].

## <a name="azure-storage-integration"></a>Integrering av Azure Storage

Du kan använda Azure Storage-tjänster på arbets belastningar som körs i ditt privata moln. Azure Storage-tjänsterna omfattar lagrings konton, Table Storage och Blob Storage. Anslutningen av arbets belastningar till Azure Storage-tjänster passerar inte Internet. Den här anslutningen ger ytterligare säkerhet och gör att du kan använda SLA-baserade Azure Storage-tjänster i dina privata moln arbets belastningar.

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig om [identitets koncept för privata moln][concepts-identity].

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorials-scale-private-cloud]: ./tutorials-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md