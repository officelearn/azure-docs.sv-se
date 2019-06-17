---
title: Azure privata DNS-frågor och svar
description: Vanliga frågor och svar om Azure privat DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: c963cb1b6930b41a703b479e0213311d971e6606
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082851"
---
# <a name="azure-private-dns-faq"></a>Azure privata DNS-frågor och svar

> [!IMPORTANT]
> Azure Private DNS är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>Stöder privata domäner i Azure DNS?

Stöd för privata domäner stöds med hjälp av funktionen för privata Azure DNS-zoner. Privata DNS-zoner som hanteras med samma verktyg som internet-ansluten Azure DNS-zoner. De är matchas från i de angivna virtuella nätverken. Mer information finns i den [översikt](private-dns-overview.md).

Information om andra interna DNS-alternativ i Azure finns i [namnmatchning för virtuella datorer och rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones fungerar i Azure-regioner?

Ja. Privata zoner har stöd för DNS-matchning mellan virtuella nätverk i Azure-regioner. Privata zoner fungerar även om du inte uttryckligen peerkoppla virtuella nätverk. De virtuella nätverken måste anges som virtuella matchningsnätverk för privat zon. Kunder kan måste de virtuella nätverken är peer-kopplad för TCP/HTTP-trafik kan flöda från en region till en annan.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Är anslutningen till Internet från virtuella nätverk som krävs för privata zoner?

Nej. Privata zoner fungerar tillsammans med virtuella nätverk. Kunder använda dem för att hantera domäner för virtuella datorer eller andra resurser inom och mellan virtuella nätverk. Internetanslutning krävs inte för namnmatchning.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Kan samma privat zon användas för flera virtuella nätverk för matchning av?

Ja. Du kan associera upp till 1000 virtuella nätverk med en privat zon.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Kan ett virtuellt nätverk som hör till en annan prenumeration läggas till som ett virtuellt nätverk som är länkade till en privat zon?

Ja. Du måste ha skrivbehörighet för åtgärden på de virtuella nätverken och privata DNS-zonen. Skrivbehörighet kan beviljas till flera RBAC-roller. Till exempel har klassiska nätverk deltagare RBAC-roller skrivbehörighet till virtuella nätverk. Mer information om RBAC-roller finns i [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Automatiskt registrerade VM DNS-poster i en privat zon tas bort automatiskt när du tar bort den virtuella datorn?

Ja. Om du tar bort en virtuell dator inom ett virtuellt nätverk som är länkade med autoregistrering aktiverat tas registrerade poster bort automatiskt.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Kan en automatiskt registrerade VM-post i en privat zon från ett virtuellt nätverk som är länkade tas bort manuellt?

Ja. Du kan skriva över de automatiskt registrerad DNS-posterna med en manuellt skapad DNS-post i zonen. Följande frågor och svar kan du lösa det här avsnittet.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Vad händer när jag försöker att manuellt skapa en ny DNS-post i en privat zon som har samma värdnamn som en befintlig virtuell dator som ska registreras automatiskt i ett länkat virtuellt nätverk?

Försök att manuellt skapa en ny DNS-post till en privat zon som har samma värdnamn som en befintlig, automatiskt registrerad-dator i ett virtuellt nätverk som är länkad. När du gör det, skriver den nya DNS-posten över automatiskt registrerade VM-post. Om du försöker ta bort den här manuellt skapade DNS-posten från zonen igen, lyckas ta bort. Automatisk registrering sker igen så länge som den virtuella datorn fortfarande finns och har en privat IP-adress som är kopplade till den. DNS-posten skapas automatiskt i zonen.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Vad händer när vi ta bort länken till ett länkat virtuellt nätverk från en privat zon? Automatiskt registrerade VM-poster från det virtuella nätverket tas bort från zonen för?

Ja. Om du vill ta bort länken till ett länkat virtuellt nätverk från en privat zon, kan du uppdatera DNS-zonen för att ta bort länken tillhörande virtuella nätverk. I den här processen tas VM-poster som har registreras automatiskt bort från zonen.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Vad händer om vi tar bort ett länkade virtuellt nätverk som är länkad till en privat zon? Har vi uppdatera den privata zonen för att ta bort länken till det virtuella nätverket som ett länkade virtuella nätverk från zonen manuellt?

Ja. När du tar bort ett länkade virtuellt nätverk utan att bryta dess länk från en privat zon först lyckas din borttagningen. Men det virtuella nätverket är inte automatiskt avlänkas från din privata zon, om sådana. Du måste manuellt ta bort länken till det virtuella nätverket från den privata zonen. Därför ta bort länken till ditt virtuella nätverk från din privata zonen innan du tar bort den.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>DNS-matchningen med hjälp av standard FQDN (internal.cloudapp.net) fungerar fortfarande även när en privat zon (till exempel private.contoso.com) är länkad till ett virtuellt nätverk?

Ja. Privata zoner ersätter inte DNS-upplösning som standard med hjälp av Azure-tillhandahållen internal.cloudapp.net zonen. Den erbjuds som en ytterligare funktion eller förbättring. Om du förlitar dig på Azure-tillhandahållen internal.cloudapp.net eller på din egen privat zon använder du det fullständiga Domännamnet för zonen som du vill matcha mot.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>DNS-suffix på virtuella datorer inom ett virtuellt nätverk som är länkade ändras med den privata zonen?

Nej. DNS-suffix på de virtuella datorerna i ditt länkade virtuella nätverk är som standard medföljer Azure suffix (”*. internal.cloudapp.net”). Du kan manuellt ändra den här DNS-suffix på dina virtuella datorer med den privata zonen.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Vilka är gränserna för användning för privat DNS för Azure?

Följande standard begränsningar gäller när du använder privat DNS för Azure.

| Resource | Standardgräns |
| --- | --- |
|Privata DNS-zoner per prenumeration|1000|
|Postuppsättningar per privata DNS-zon|25,000|
|Poster per uppsättning av poster|20|
|Virtuella nätverkslänkar per privat DNS-zon|1000|
|Virtuella nätverk-länkar per privata DNS-zoner med automatisk registrering aktiverat|100|
|Antal privata DNS-zoner som ett virtuellt nätverk kan få kopplas till en med automatisk registrering är aktiverat|1|
|Antal privata DNS-zoner som ett virtuellt nätverk kan få länkad|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Finns det portal-stöd för privata zoner?

Ja, och privata zoner som redan har skapats via API: er, PowerShell, CLI och SDK: er är synliga på Azure portal.

## <a name="next-steps"></a>Nästa steg

- [Mer information om privat DNS för Azure](private-dns-overview.md)