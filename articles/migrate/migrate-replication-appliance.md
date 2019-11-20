---
title: Arkitektur för Azure Migrate replikerings utrustning
description: Innehåller en översikt över Azure Migrate Replication-enhet för agent-baserad migrering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186561"
---
# <a name="replication-appliance"></a>Replikeringsfil

Den här artikeln beskriver den replikeringsprincip som används av Azure Migrate: Server utvärdering när du migrerar virtuella VMware-datorer, fysiska datorer och privata/offentliga virtuella moln till Azure med hjälp av en agent-baserad migrering. 

Verktyget är tillgängligt i [Azure Migrate](migrate-overview.md) Hub. Hubben innehåller inbyggda verktyg för utvärdering och migrering, samt verktyg från andra Azure-tjänster och oberoende program varu leverantörer (ISV: er) från tredje part.


## <a name="appliance-overview"></a>Apparat översikt

Replikeringen distribueras som en enda lokal dator, antingen som en virtuell VMware-dator eller en fysisk server. Den körs:
- **Replikeringsfil**: replikeringstjänsten samordnar kommunikationen och hanterar datareplikering för lokala virtuella VMware-datorer och fysiska servrar som replikeras till Azure.
- **Processerver**: processervern som installeras som standard på serverdatorn och gör följande:
    - **Gateway för replikering**: den fungerar som en gateway för replikering. Den tar emot replikeringsdata från datorer som har Aktiver ATS för replikering. Det optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar dem till Azure.
    - **Agent installations program**: utför en push-installation av mobilitets tjänsten. Tjänsten måste installeras och köras på alla lokala datorer som du vill replikera för migrering.

## <a name="appliance-deployment"></a>Distribution av utrustning

**Distribuera som** | **Används för** | **Information**
--- | --- |  ---
Virtuell VMware-dator | Används vanligt vis när du migrerar virtuella VMware-datorer med hjälp av Azure Migrate Migration Tool med agent-baserad migrering. | Du hämtar en embryo-mall från Azure Migrate hubben och importerar till vCenter Server för att skapa den virtuella datorn.
En fysisk dator | Används när du migrerar lokala fysiska servrar om du inte har en VMware-infrastruktur, eller om du inte kan skapa en virtuell VMware-dator med en ägg-mall. | Du hämtar ett installations program från Azure Migrate hubben och kör det för att konfigurera installations programmet.

## <a name="appliance-deployment-requirements"></a>Krav för installation av utrustning

[Granska](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) distributions kraven.



## <a name="appliance-license"></a>Produkt licens
Enheten levereras med en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar. Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för den virtuella dator enheten.

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den inledande replikeringen till Azure Storage med den angivna replikeringsprincipen. 
2. Trafiken replikeras till offentliga slut punkter i Azure Storage via Internet. Det finns inte stöd för att replikera trafik över ett virtuellt privat nätverk (VPN) från plats till plats från en lokal plats till Azure.
3. När den inledande replikeringen har slutförts börjar delta-replikeringen. Spårade ändringar för en dator loggas.
4. Kommunikationen sker på följande sätt:
    - Virtuella datorer kommunicerar med replikerings enheten på port HTTPS 443 inkommande, för hantering av replikering.
    - Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (körs på replikerings enheten) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.
5. Replikeringsdata loggar första marken i ett cache Storage-konto i Azure. Dessa loggar bearbetas och data lagras på en Azure-hanterad disk.

![Arkitektur](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Installations program

Enheten uppgraderas manuellt från Azure Migrate Hub. Vi rekommenderar att du alltid kör den senaste versionen.

1. I Azure Migrate > servrar > Azure Migrate: Server utvärdering, infrastruktur servrar, klickar du på **konfigurations servrar**.
2. I **konfigurations servrar**visas en länk i **agent version** när det finns en ny version av Replikerings enheten. 
3. Ladda ned installations programmet till replikeringstjänsten och installera uppgraderingen. Installations programmet identifierar den version som är aktuell som körs på enheten.
 
## <a name="next-steps"></a>Nästa steg

[Lär dig hur](tutorial-assess-vmware.md#set-up-the-appliance-vm) du konfigurerar-installationen för VMware.
[Lär dig hur](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) du konfigurerar-enheten för Hyper-V.

