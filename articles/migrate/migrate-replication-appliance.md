---
title: Azure Migrate installation replikeringsarkitektur | Microsoft Docs
description: Översikt över Azure Migrate replikering installationen
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811445"
---
# <a name="replication-appliance"></a>Installation av replikering

Den här artikeln beskriver den installationen för replikering som används av Azure Migrate: Server-utvärdering när du migrerar virtuella VMware-datorer och fysiska datorer privata/offentliga molnbaserade virtuella datorer till Azure, med hjälp av en agentbaserad-migrering. 

Verktyget är tillgängligt i den [Azure Migrate](migrate-overview.md) hub. Hubben innehåller inbyggda verktyg för bedömning och migrering, samt verktyg från andra Azure-tjänster och från oberoende programleverantörer för från tredje part (ISV).


## <a name="appliance-overview"></a>Översikt över installation

Replikering installationen distribueras som en enda lokala dator, antingen som en VMware-VM eller en fysisk server. Den körs:
- **Replikering installation**: Replikering installationen samordnar kommunikationen och hanterar datareplikering för lokala virtuella VMware-datorer och fysiska servrar replikerar till Azure.
- **Processerver**: Processervern, som installeras som standard på enheten som replikering och gör följande:
    - **Replikeringsgateway**: Den fungerar som en replikeringsgateway. Den tar emot replikeringsdata från datorer som har aktiverats för replikering. Det optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar det till Azure.
    - **Installationsprogrammet för agenten**: Utför en push-installation av Mobilitetstjänsten. Den här tjänsten måste vara installerad och körs på var och en lokal dator som du vill replikera för migrering.

## <a name="appliance-deployment"></a>Distribution av installation

**Distribuera som** | **Används för** | **Detaljer**
--- | --- |  ---
Virtuell VMware-dator | Används vanligtvis när du migrerar virtuella VMware-datorer med hjälp av Migreringsverktyget för Azure Migrate med agentbaserad migreringen. | Du hämta OVA-mall från Azure Migrate-navet och importera till vCenter Server för att skapa virtuell dator.
En fysisk dator | Används när du migrerar lokala fysiska servrar om du inte har en VMware-infrastruktur, eller om det inte går att skapa en VMware-VM med en OVA-mall. | Du hämta ett installationsprogram för programvara från Azure Migrate-navet och kör den för att konfigurera datorn för installation.

## <a name="appliance-deployment-requirements"></a>Krav på installation

[Granska](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) distributionskraven.



## <a name="appliance-license"></a>Licens för installation
Enheten som levereras med en utvärderingslicens för Windows Server 2016 som är giltig i 180 dagar. Om utvärderingsperioden är nära förfallodatum, rekommenderar vi att du hämtar och distribuerar en ny installation eller att du aktiverar licensen VM-enhetens operativsystem.

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator, börjar den inledande replikeringen till Azure storage med hjälp av den angivna replikeringsprincipen. 
2. Trafik som replikeras till Azure storage-offentliga slutpunkter för via internet. Replikering av trafik via ett plats-till-plats virtuellt privat nätverk (VPN) från en lokal plats till Azure stöds inte.
3. När den inledande replikeringen är klar startar deltareplikeringen. Spårade ändringar för en dator loggas.
4. Kommunikation händer följande:
    - Virtuella datorer kommunicera med installationen för replikering på port HTTPS 443 inkommande, för replikeringshantering.
    - Replikering installationen arrangerar replikeringen med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (som körs på installationen för replikering) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure storage över port 443 utgående.
5. Replikeringsdata loggar första mark i ett cachelagringskonto i Azure. Dessa loggar bearbetas och data lagras i en Azure hanterade diskar.

![Arkitektur](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Installationen uppgraderingar

Installationen uppgraderas manuellt från Azure Migrate-hubben. Vi rekommenderar att du alltid använder den senaste versionen.

1. I Azure Migrate > servrar > Azure Migrate: Server-utvärdering, infrastrukturservrar, klickar du på **konfigurationsservrar**.
2. I **konfigurationsservrar**, en länk visas i **Agentversion** när en ny version av installationen för replikering är tillgänglig. 
3. Ladda ned installationsprogrammet till datorn för installation av replikering och installera uppgraderingen. Installationsprogrammet identifierar den aktuella kör version i installationen.
 
## <a name="next-steps"></a>Nästa steg

[Lär dig hur](tutorial-assess-vmware.md#set-up-the-appliance-vm) att ställa in installationen för VMware.
[Lär dig hur](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) att ställa in installationen för Hyper-V.

