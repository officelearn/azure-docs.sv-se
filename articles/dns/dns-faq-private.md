---
title: Vanliga frågor och svar om Azure Private DNS
description: I den här artikeln får du lära dig vanliga frågor om Azure Private DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939428"
---
# <a name="azure-private-dns-faq"></a>Vanliga frågor och svar om Azure Private DNS

Följande är vanliga frågor och svar om Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>Stöder Azure DNS privata domäner?

Privata domäner stöds med hjälp av azure private DNS-zoner funktionen. Privata DNS-zoner kan endast lösas inifrån angivna virtuella nätverk. Mer information finns i [översikten](private-dns-overview.md).

Information om andra interna DNS-alternativ i Azure finns i [Namnmatchning för virtuella datorer och rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Fungerar Azure Private DNS-zoner i Azure-regioner?

Ja. Privata zoner stöds för DNS-lösning mellan virtuella nätverk i Azure-regioner. Privata zoner fungerar även utan att uttryckligen peering de virtuella nätverken. Alla virtuella nätverk måste vara länkade till den privata DNS-zonen.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Krävs anslutning till Internet från virtuella nätverk för privata zoner?

Nej. Privata zoner fungerar tillsammans med virtuella nätverk. Du använder dem för att hantera domäner för virtuella datorer eller andra resurser inom och mellan virtuella nätverk. Internetanslutning krävs inte för namnmatchning.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Kan samma privata zon användas för flera virtuella nätverk för upplösning?

Ja. Du kan länka en privat DNS-zon med tusentals virtuella nätverk. Mer information finns i [Azure DNS-gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Kan ett virtuellt nätverk som tillhör en annan prenumeration länkas till en privat zon?

Ja. Du måste ha skrivåtgärdsbehörighet för de virtuella nätverken och den privata DNS-zonen. Skrivbehörigheten kan beviljas till flera RBAC-roller. Den klassiska nätverksdeltagaren RBAC-rollen har till exempel skrivbehörighet till virtuella nätverk och rollen Deltagare i privata DNS-zoner har skrivbehörighet för de privata DNS-zonerna. Mer information om RBAC-roller finns i [Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Kommer de automatiskt registrerade DNS-posterna för virtuella datorer i en privat zon att tas bort automatiskt när du tar bort den virtuella datorn?

Ja. Om du tar bort en virtuell dator i ett länkat virtuellt nätverk med autoregistration aktiverat tas de registrerade posterna bort automatiskt.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Kan en automatiskt registrerad virtuell datorpost i en privat zon från ett länkat virtuellt nätverk tas bort manuellt?

Ja. Du kan skriva över de automatiskt registrerade DNS-posterna med en manuellt skapad DNS-post i zonen. Följande fråga och svar behandlar detta ämne.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Vad händer när jag försöker skapa en ny DNS-post manuellt i en privat zon som har samma värdnamn som en automatiskt registrerad befintlig virtuell dator i ett länkat virtuellt nätverk?

Du försöker skapa en ny DNS-post manuellt i en privat zon som har samma värdnamn som en befintlig, automatiskt registrerad virtuell dator i ett länkat virtuellt nätverk. När du gör det skriver den nya DNS-posten över den automatiskt registrerade posten för virtuella datorer. Om du försöker ta bort den manuellt skapade DNS-posten från zonen igen lyckas borttagningen. Den automatiska registreringen sker igen så länge den virtuella datorn fortfarande finns och har en privat IP-kopplad till den. DNS-posten återskapas automatiskt i zonen.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Vad händer när vi tar bort länken till ett länkat virtuellt nätverk från en privat zon? Kommer de automatiskt registrerade posterna för virtuella datorer från det virtuella nätverket också att tas bort från zonen?

Ja. Om du vill ta bort länken till ett länkat virtuellt nätverk från en privat zon uppdaterar du DNS-zonen för att ta bort länken för det associerade virtuella nätverket. I den här processen tas poster för virtuella datorer som registrerades automatiskt bort från zonen.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Vad händer när vi tar bort ett länkat virtuellt nätverk som är länkat till en privat zon? Måste vi manuellt uppdatera den privata zonen för att ta bort länken till det virtuella nätverket som ett länkat virtuellt nätverk från zonen?

Nej. När du tar bort ett länkat virtuellt nätverk utan att först ta bort länken från en privat zon, lyckas borttagningen och länkarna till DNS-zonen rensas automatiskt.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Kommer DNS-upplösningen med hjälp av standard FQDN (internal.cloudapp.net) fortfarande att fungera även när en privat zon (till exempel private.contoso.com) är länkad till ett virtuellt nätverk?

Ja. Privata zoner ersätter inte standardområdet Azure-internal.cloudapp.net. Oavsett om du förlitar dig på azure-internal.cloudapp.net eller på din egen privata zon använder du FQDN för zonen som du vill lösa mot.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Kommer DNS-suffixet på virtuella datorer i ett länkat virtuellt nätverk att ändras till det privata områdets?

Nej. DNS-suffixet på de virtuella datorerna i det länkade virtuella nätverket förblir standardsuffixet för Azure ("*.internal.cloudapp.net"). Du kan manuellt ändra dns-suffixet på dina virtuella datorer till den privata zonen.
Mer information om hur du ändrar det här suffixet finns [i Använda dynamisk DNS för att registrera värdnamn på din egen DNS-server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Vilka är användningsgränserna för Azure DNS Private zones?

Se [Azure DNS-gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) för information om användningsgränserna för privata Azure DNS-zoner.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Varför visas inte mina befintliga privata DNS-zoner i den nya portalupplevelsen?

Om din befintliga privata DNS-zon skapades med förhandsgransknings-API måste du migrera dessa zoner till ny resursmodell. Privata DNS-zoner som skapats med förhandsgransknings-API visas inte i ny portalupplevelse. Se nedan för instruktioner om hur du migrerar till ny resursmodell.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hur migrerar jag mina befintliga privata DNS-zoner till den nya modellen?

Vi rekommenderar starkt att du migrerar till den nya resursmodellen så snart som möjligt. Äldre resursmodell kommer att stödjas, men ytterligare funktioner kommer inte att utvecklas ovanpå den här modellen. I framtiden har vi för avsikt att förålga den till förmån för ny resursmodell. Mer information om hur du migrerar dina befintliga privata DNS-zoner till ny resursmodell finns[i migreringsguiden för privata Azure DNS-zoner](private-dns-migration-guide.md).

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Private DNS](private-dns-overview.md)
