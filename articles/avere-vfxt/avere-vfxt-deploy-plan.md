---
title: Planera din Avere vFXT system - Azure
description: Beskriver planering innan du distribuerar Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 46978d19a0789bb43e861ca89661aa5b78eb4ec7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409891"
---
# <a name="plan-your-avere-vfxt-system"></a>Planera för ditt Avere vFXT-system

Den här artikeln beskriver hur du planerar en ny Avere vFXT för Azure-kluster som är placerat och lämplig storlek för dina behov. 

Överväg hur klustret kommer att interagera med andra element i Azure innan du gå till Azure Marketplace eller skapa virtuella datorer. Planera där klusterresurser ska finnas i ditt privata nätverk och undernät, och bestäm där backend-lagringen är. Se till att noderna i klustret som du skapar är kraftfullt nog för att stödja ditt arbetsflöde. 

Fortsätt att läsa om du vill veta mer.

## <a name="resource-group-and-network-infrastructure"></a>Resurs-grupp- och nätverksinfrastrukturen

Överväg att där element i din Avere vFXT för Azure-distribution ska vara. Diagrammet nedan visar ett möjligt arrangemang för Avere vFXT för Azure-komponenter:

![Diagram som visar klustret styrenhet och klustret virtuella datorer i ett undernät. Runt undernätet är gränsen en gräns för virtuellt nätverk. I det virtuella nätverket finns en sexhörning som representerar tjänsten lagringsslutpunkten; Det är kopplat till en streckad pil till en Blob-lagring utanför det virtuella nätverket.](media/avere-vfxt-components-option.png)

Följ dessa riktlinjer när du planerar Avere vFXT systemets nätverksinfrastruktur:

* Alla element ska hanteras med en ny prenumeration som skapats för Avere vFXT distributionen. Fördelarna innefattar: 
  * Enklare kostnaden spårnings - visa och granska alla kostnader från resurser, infrastruktur och beräkning cykler i en prenumeration.
  * Enklare rensning - kan du ta bort hela prenumerationen när du är klar med projektet.
  * Praktisk partitionering av resurs kvoter - skydda andra arbetsbelastningar från möjliga resursbegränsning genom att isolera Avere vFXT klienter och -kluster i en enda prenumeration. På så sätt undviker konflikt när du tar upp ett stort antal klienter för ett högpresterande databehandling arbetsflöde.

* Hitta din beräkning klientsystem nära vFXT klustret. Backend-lagringen kan vara lägre.  

* VFXT klustret och kontrollanten kluster VM måste finnas i samma virtuella nätverk (vnet) i samma resursgrupp och använder samma lagringskonto. Skapa mallen kluster hanterar för de flesta situationer.

* Klustret måste finnas i ett eget undernät att undvika IP-adresskonflikter med klienter eller beräkningsresurser. 

* Klustret skapas mallen kan skapa de flesta av de nödvändiga infrastrukturresurser för klustret, inklusive resursgrupper, virtuella nätverk, undernät och storage-konton. Om du vill använda resurser som redan finns, kontrollera att de uppfyller kraven i den här tabellen. 

  | Resurs | Använd befintlig? | Krav |
  |----------|-----------|----------|
  | Resursgrupp | Ja, om den är tom | Måste vara tom| 
  | Lagringskonto | Ja om du ansluter en befintlig Blob-behållaren när klustret har skapats <br/>  Nej om du skapar en ny Blob-behållaren när klustret skapas | Befintlig Blob-behållare måste vara tom <br/> &nbsp; |
  | Virtuellt nätverk | Ja | Måste innehålla en slutpunkt för lagring om du skapar en ny Azure Blob-behållare | 
  | Undernät | Ja |   |

## <a name="ip-address-requirements"></a>IP-adresskraven 

Se till att ditt kluster undernät har en tillräckligt stor för IP-adressintervall för klustret. 

Avere vFXT klustret använder följande IP-adresser:

* Ett kluster IP-adress för hantering. Den här adressen kan flytta från nod till nod i klustret, men alltid är tillgänglig så att du kan ansluta till konfigurationsverktyget Avere på Kontrollpanelen.
* För varje nod i klustret:
  * Minst en klientinriktade IP-adress. (Alla klientinriktade-adresser som hanteras av klustrets *vserver*, vilket kan du flytta dem mellan noder efter behov.)
  * En IP-adress för klusterkommunikation
  * En instans IP-adress (tilldelas den virtuella datorn)

Om du använder Azure Blob storage måste kan det också kräva IP-adresser från ditt kluster vnet:  

* Ett Azure Blob storage-konto kräver minst fem IP-adresser. Tänk på det här kravet om du hitta Blob-lagring i samma virtuella nätverk som klustret.
* Om du använder Azure Blob-lagring som är utanför det virtuella nätverket för klustret, bör du skapa en slutpunkt för lagring i det virtuella nätverket. Den här slutpunkten använder inte en IP-adress.

Du har möjlighet att söka efter nätverksresurser och Blob-lagring (om det används) i olika resursgrupper från klustret.

## <a name="vfxt-node-size"></a>vFXT nodstorlek

De virtuella datorerna som fungerar som klusternoder fastställa begäran dataflöde och lagring kapaciteten för din cachelagring. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Each vFXT node will be identical. Det vill säga om du skapar ett kluster med tre noder har tre virtuella datorer av samma typ och storlek. 

| Instanstyp | vCPU:er | Minne  | Lokal SSD-lagring  | Maximalt antal datadiskar | Icke cachelagrat diskgenomflöde | Nätverkskort (antal) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 Mbit/s | 16 000 Mbit/s (8)  |

Diskcache per nod kan konfigureras och kan rage från 1 000 GB till 8000 GB. 4 TB per nod är den rekommendera cachestorleken för Standard_E32s_v3 noder.

Mer information om dessa virtuella datorer finns i Microsoft Azure-dokumentationen:

* [Minnesoptimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Kontokvoten

Se till att din prenumeration har kapacitet att köra Avere vFXT klustret samt eventuella databehandling eller klienten system som används. Läs [kvot för klustret vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) mer information.

## <a name="back-end-data-storage"></a>Backend-datalagring

Var ska Avere vFXT klustret lagra dina data när det inte finns i cacheminnet? Besluta om din arbetsminnet ska vara lagrade långsiktig i en ny blobbehållare eller i ett befintligt moln eller maskinvara lagringssystemet. 

Om du vill använda Azure Blob storage för backend-servern bör du skapa en ny behållare som en del av vFXT klustret skapas. Det här alternativet skapar och konfigurerar den nya behållaren så att det är klart att användas när klustret är klart. 

Läs [skapar Avere vFXT för Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) mer information.

> [!NOTE]
> Endast tom Blob storage-behållare kan användas som core filter för Avere vFXT system. VFXT måste kunna hantera arkivet objekt utan att behöva spara befintlig data. 
>
> Läs [flytta data till klustret vFXT](avere-vfxt-data-ingest.md) och lär dig att kopiera data till klustrets ny behållare effektivt med hjälp av klientdatorer och Avere vFXT cache.

Om du vill använda ett befintligt lokalt storage system du måste lägga till det vFXT klustret när den har skapats. Läs [konfigurerar du lagring](avere-vfxt-add-storage.md) detaljerade anvisningar om hur du lägger till en befintlig lagringssystemet Avere vFXT klustret.

## <a name="cluster-access"></a>Klusteråtkomst 

Avere vFXT för Azure-kluster finns i ett privat undernät och klustret har inte en offentlig IP-adress. Du måste ha någon metod för att komma åt privat undernät för administration av klustret och klientanslutningar. 

Åtkomst till alternativen är:

* Hoppa värden – tilldela en offentlig IP-adress till en separat virtuell dator i det privata nätverket och använda den för att skapa en SSL-tunnel till klusternoderna. 

  > [!TIP]
  > Om du ställer in en offentlig IP-adress på styrenheten för klustret, kan du använda den som jump-värd. Läs [kluster domänkontrollanter som går du vidare värden](#cluster-controller-as-jump-host) för mer information.

* Virtuellt privat nätverk (VPN) – Konfigurera en punkt-till-plats eller plats-till-plats VPN-anslutning till ditt privata nätverk.

* Azure ExpressRoute – konfigurera en privat anslutning via en ExpressRoute-partner. 

Mer information om alternativen finns i [Azure Virtual Network-dokumentationen om internet-kommunikation](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Kluster-domänkontrollanter som går du vidare värd

Om du ställer in en offentlig IP-adress på kontrollanten kluster, kan du använda den som en jump-värd för att kontakta Avere vFXT klustret från utanför privat undernät. Men eftersom kontrollanten har behörighet att ändra klusternoder, skapar detta en liten säkerhetsrisk.  

För att förbättra säkerheten för en kontrollant med en offentlig IP-adress, skapar distributionsskriptet automatiskt en nätverkssäkerhetsgrupp som begränsar inkommande åtkomst till port 22. Du kan ytterligare skydda systemet genom att låsa åtkomst till ditt intervall med IP-källadresser – det vill säga Tillåt endast anslutningar från datorer som du planerar att använda för åtkomst till klustret.

När du skapar klustret kan välja du om du ska skapa en offentlig IP-adress på kontrollanten kluster eller inte. 

* Om du skapar ett nytt virtuellt nätverk eller ett nytt undernät, tilldelas kontrollanten klustret en offentlig IP-adress.
* Om du väljer ett befintligt virtuellt nätverk och undernät, har kontrollanten kluster endast privata IP-adresser. 

## <a name="vm-access-roles"></a>VM access-roller 

Azure använder [rollbaserad åtkomstkontroll](../role-based-access-control/index.yml) (RBAC) för att auktorisera virtuella datorer att utföra vissa uppgifter i klustret. Kluster-styrenheten måste till exempel behörighet att skapa och konfigurera virtuella datorer i klusternoden. Klusternoderna måste kunna tilldela eller omtilldelar IP-adresser till andra noder i klustret.

Två inbyggda roller för Azure används för Avere vFXT virtuella datorer: 

* Kontrollanten klustret använder den inbyggda rollen [Avere deltagare](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Klusternoder använda den inbyggda rollen [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator)

Om du vill anpassa åtkomst roller för Avere vFXT komponenter måste du definiera en egen roll och tilldela den till de virtuella datorerna när de skapas. Du kan inte använda mallen för distribution i Azure Marketplace. Kontakta Microsofts kundservice och Support genom att öppna en biljett i Azure-portalen, enligt beskrivningen i [få hjälp med ditt system](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Nästa steg: Förstå distributionsprocessen

[Översikt över distribution](avere-vfxt-deploy-overview.md) ger en översikt över alla steg som behövs för att skapa en Avere vFXT för Azure-systemet och blir redo att hantera data.  