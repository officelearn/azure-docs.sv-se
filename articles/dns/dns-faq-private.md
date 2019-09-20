---
title: Vanliga frågor och svar om Azure Private DNS
description: Vanliga frågor och svar om Azure Privat DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155713"
---
# <a name="azure-private-dns-faq"></a>Vanliga frågor och svar om Azure Private DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>Stöder Azure DNS privata domäner?

Stöd för privata domäner stöds med Azure Privat DNS Zones-funktionen. Privat DNS zoner hanteras med samma verktyg som Internet riktade Azure DNS zoner. De kan bara matchas från de angivna virtuella nätverken. Mer information finns i [översikten](private-dns-overview.md).

Information om andra interna DNS-alternativ i Azure finns i [namn matchning för virtuella datorer och roll instanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Kommer Azure DNS Private Zones att fungera i Azure-regioner?

Ja. Privata zoner stöds för DNS-matchning mellan virtuella nätverk i Azure-regioner. Privata zoner fungerar även utan att uttryckligen peer-koppla de virtuella nätverken. Alla virtuella nätverk måste anges som matchnings virtuella nätverk för den privata zonen. Du kan behöva att de virtuella nätverken ska peer-kopplas för att TCP/HTTP-trafik ska flöda från en region till en annan.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Är anslutningen till Internet från virtuella nätverk som krävs för privata zoner?

Nej. Privata zoner fungerar tillsammans med virtuella nätverk. Du kan använda dem för att hantera domäner för virtuella datorer eller andra resurser i och mellan virtuella nätverk. Ingen Internet anslutning krävs för namn matchning.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Kan samma privata zon användas för flera virtuella nätverk för matchning?

Ja. Du kan koppla upp till 1000 virtuella nätverk med en enda privat zon.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Kan ett virtuellt nätverk som tillhör en annan prenumeration läggas till som ett länkat virtuellt nätverk till en privat zon?

Ja. Du måste ha behörigheten Skriv åtgärd för de virtuella nätverken och den privata DNS-zonen. Skriv behörigheten kan beviljas till flera RBAC-roller. Till exempel har rollen klassisk nätverks deltagare RBAC behörigheten Skriv till virtuella nätverk. Mer information om RBAC-roller finns i [rollbaserad åtkomst kontroll](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Kommer de automatiskt registrerade DNS-posterna för virtuella datorer i en privat zon automatiskt att tas bort när du tar bort den virtuella datorn?

Ja. Om du tar bort en virtuell dator i ett länkat virtuellt nätverk med automatisk registrering aktive rad tas registrerade poster bort automatiskt.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Kan en automatiskt registrerad virtuell dator post i en privat zon från ett länkat virtuellt nätverk tas bort manuellt?

Ja. Du kan skriva över de automatiskt registrerade DNS-posterna med en manuellt skapad DNS-post i zonen. Följande fråga och svar riktar sig till det här avsnittet.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Vad händer när jag försöker skapa en ny DNS-post manuellt i en privat zon som har samma värdnamn som en automatiskt registrerad befintlig virtuell dator i ett länkat virtuellt nätverk?

Du försöker skapa en ny DNS-post manuellt i en privat zon som har samma värdnamn som en befintlig, automatiskt registrerad virtuell dator i ett länkat virtuellt nätverk. När du gör det skriver den nya DNS-posten över den automatiskt registrerade posten för den virtuella datorn. Om du försöker ta bort den här manuellt skapade DNS-posten från zonen igen lyckas borttagningen. Den automatiska registreringen sker igen så länge den virtuella datorn fortfarande finns och har en privat ansluten IP-adress. DNS-posten skapas igen automatiskt i zonen.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Vad händer när vi tar bort länkar till ett länkat virtuellt nätverk från en privat zon? Kommer de automatiskt registrerade poster för virtuella datorer från det virtuella nätverket att tas bort från zonen?

Ja. Om du vill ta bort länken mellan ett länkat virtuellt nätverk från en privat zon uppdaterar du DNS-zonen för att ta bort den associerade virtuella nätverks länken. I den här processen tas poster för virtuella datorer som registrerats automatiskt bort från zonen.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Vad händer när vi tar bort ett länkat virtuellt nätverk som är länkat till en privat zon? Måste vi uppdatera den privata zonen manuellt för att ta bort länkar till det virtuella nätverket som ett länkat virtuellt nätverk från zonen?

Ja. När du tar bort ett länkat virtuellt nätverk utan att ta bort länken från en privat zon måste du först ta bort åtgärden. Men det virtuella nätverket avlänkas inte automatiskt från din privata zon, om det finns några. Du måste ta bort kopplingen mellan det virtuella nätverket och den privata zonen manuellt. Därför bör du ta bort länken till det virtuella nätverket från din privata zon innan du tar bort det.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Kommer DNS-matchning med hjälp av standard-FQDN (internal.cloudapp.net) fortfarande fungera även om en privat zon (till exempel private.contoso.com) är länkad till ett virtuellt nätverk?

Ja. Privata zoner ersätter inte standard-DNS-matchningar med hjälp av den Azure-angivna internal.cloudapp.net-zonen. Den erbjuds som en ytterligare funktion eller förbättring. Oavsett om du förlitar dig på den Azure-tillhandahållna internal.cloudapp.net eller på en egen privat zon, använder du FQDN för den zon som du vill matcha mot.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Kommer DNS-suffixet på virtuella datorer i ett länkat virtuellt nätverk att ändras till den privata zonen?

Nej. DNS-suffixet på de virtuella datorerna i det länkade virtuella nätverket är kvar som standard Azure-tillhandahållet suffix ("*. internal.cloudapp.net"). Du kan manuellt ändra det här DNS-suffixet på dina virtuella datorer till den privata zonen.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Vilka är användnings gränserna för Azure Privat DNS?

Följande standard gränser gäller när du använder Azure Privat DNS.

| Resource | Standardgräns |
| --- | --- |
|Privat DNS zoner per prenumeration|1000|
|Post uppsättningar per Privat DNS zon|25,000|
|Poster per post uppsättning|20|
|Virtual Network länkar per privat DNS-zon|1000|
|Virtuella nätverk länkar per privat DNS-zoner med automatisk registrering aktive rad|100|
|Antal privata DNS-zoner som ett virtuellt nätverk kan kopplas till med automatisk registrering aktive rad|1|
|Antal privata DNS-zoner som ett virtuellt nätverk kan länkas till|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Finns det stöd för Portal för privata zoner?

Ja och privata zoner som redan har skapats via API: er, PowerShell, CLI och SDK: er visas på Azure Portal.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Varför visas inte mina befintliga privata DNS-zoner i den nya Portal upplevelsen?

Som en del av den förhands gransknings uppdaterings versionen levererade vi en ny resurs modell för privata DNS-zoner. Dina befintliga privata DNS-zoner måste migreras till en ny resurs modell innan de kan visas i den nya Portal upplevelsen. Se nedan för instruktioner om hur du migrerar till en ny resurs modell.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hur gör jag för att migrera mina befintliga privata DNS-zoner till den nya modellen?
Vi rekommenderar starkt att du migrerar till den nya resurs modellen så snart som möjligt. Äldre resurs modell kommer att stödjas men ytterligare funktioner kommer inte att utvecklas ovanpå den här modellen. I framtiden planerar vi att bli av med den nya resurs modellens fördel. Vägledning om hur du migrerar befintliga privata DNS-zoner till en ny resurs modell finns i[migreringsguiden för Azure DNS privata zoner](private-dns-migration-guide.md).

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Privat DNS](private-dns-overview.md)