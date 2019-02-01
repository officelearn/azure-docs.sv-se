---
title: Planera din Avere vFXT system - Azure
description: Beskriver planering innan du distribuerar Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: c0304e290d18e6569e3fcc2efbab8af15727b80c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508170"
---
# <a name="plan-your-avere-vfxt-system"></a>Planera för ditt Avere vFXT-system

Den här artikeln beskriver hur du planerar en ny Avere vFXT för Azure-kluster för att kontrollera att du skapar klustret är placerat och lämplig storlek för dina behov. 

Överväg hur klustret kommer att interagera med andra element i Azure innan du gå till Azure Marketplace eller skapa virtuella datorer. Planera där klusterresurser ska finnas i ditt privata nätverk och undernät, och bestäm där backend-lagringen är. Se till att noderna i klustret som du skapar är kraftfullt nog för att stödja ditt arbetsflöde. 

Fortsätt att läsa om du vill veta mer.

## <a name="resource-group-and-network-infrastructure"></a>Resurs-grupp- och nätverksinfrastrukturen

Överväg att där element i din Avere vFXT för Azure-distribution ska vara. Diagrammet nedan visar ett möjligt arrangemang för Avere vFXT för Azure-komponenter:

![Diagram som visar klustret styrenhet och klustret virtuella datorer i ett undernät. Runt undernätet är gränsen en gräns för virtuellt nätverk. I det virtuella nätverket finns en sexhörning som representerar tjänsten lagringsslutpunkten; Det är kopplat till en streckad pil till en Blob-lagring utanför det virtuella nätverket.](media/avere-vfxt-components-option.png)

Följ dessa riktlinjer när du planerar Avere vFXT systemets nätverksinfrastruktur:

* Alla element ska hanteras med en ny prenumeration som skapats för Avere vFXT distributionen. Fördelarna innefattar: 
  * Enklare kostnaden spårnings - visa och granska alla kostnader från resurser, infrastruktur och beräkning cykler i en prenumeration.
  * Enklare rensning - kan du ta bort hela prenumerationen när du är klar med projektet.
  * Praktisk partitionering av resurs kvoter - skydda andra arbetsbelastningar från möjliga resursbegränsning när du tar upp det stora antalet klienter som används för högpresterande databehandling arbetsflödet genom att isolera Avere vFXT klienter och -kluster i en enstaka prenumeration.

* Hitta din beräkning klientsystem nära vFXT klustret. Backend-lagringen kan vara lägre.  

* Leta upp vFXT klustret och kontrollanten kluster virtuell dator i samma virtuella nätverk (vnet) och i samma resursgrupp för enkelhetens skull. De bör också använda samma lagringskonto. (Kontrollanten kluster skapar klustret och kan också användas för kommandoraden klusterhantering.)  

* Klustret måste finnas i ett eget undernät att undvika IP-adresskonflikter med klienter eller beräkningsresurser. 

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

## <a name="vfxt-node-sizes"></a>storleken på vFXT 

De virtuella datorerna som fungerar som klusternoder fastställa begäran dataflöde och lagring kapaciteten för din cachelagring. Du kan välja mellan två instanstyper med olika minne, processor och lokala lagringen. 

Each vFXT node will be identical. Det vill säga om du skapar ett kluster med tre noder har tre virtuella datorer av samma typ och storlek. 

| Instanstyp | vCPU:er | Minne  | Lokal SSD-lagring  | Maximalt antal datadiskar | Icke cachelagrat diskgenomflöde | Nätverkskort (antal) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25,600 IOPS <br/> 384 Mbit/s | 8 000 Mbit/s (8) |
| Standard_E32s_v3 | 32  | 256 GB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 Mbit/s | 16 000 Mbit/s (8)  |

Diskcache per nod kan konfigureras och kan rage från 1 000 GB till 8000 GB. 1 TB per nod är den rekommendera cachestorleken för Standard_D16s_v3 noder och 4 TB per nod som rekommenderas för Standard_E32s_v3 noder.

Mer information om dessa virtuella datorer finns i följande dokument för Microsoft Azure:

* [Storlekar för virtuella datorer för generell användning](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
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

Använd en grupp för förbättrad säkerhet med en offentlig IP-adress för att tillåta inkommande åtkomst endast via port 22. Du kan eventuellt ytterligare skydda systemet genom att låsa åtkomst till ditt intervall med IP-källadresser – det vill säga Tillåt endast anslutningar från datorer som du planerar att använda för åtkomst till klustret.

När du skapar klustret kan välja du om du ska skapa en offentlig IP-adress på kontrollanten kluster eller inte. 

* Om du skapar ett nytt virtuellt nätverk eller ett nytt undernät, tilldelas kontrollanten klustret en offentlig IP-adress.
* Om du väljer ett befintligt virtuellt nätverk och undernät, har kontrollanten kluster endast privata IP-adresser. 

## <a name="next-step-understand-the-deployment-process"></a>Nästa steg: Förstå distributionsprocessen

[Översikt över distribution](avere-vfxt-deploy-overview.md) ger en översikt över alla steg som behövs för att skapa en Avere vFXT för Azure-systemet och blir redo att hantera data.  