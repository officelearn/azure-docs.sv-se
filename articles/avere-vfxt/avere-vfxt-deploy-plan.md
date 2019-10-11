---
title: Planera ditt AVERT vFXT-system – Azure
description: Förklarar hur du planerar att göra innan du distribuerar AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256234"
---
# <a name="plan-your-avere-vfxt-system"></a>Planera för ditt Avere vFXT-system

I den här artikeln förklaras hur du planerar en ny vFXT för Azure-kluster som är placerade och lämplig storlek för dina behov. 

Innan du går till Azure Marketplace eller skapar virtuella datorer bör du tänka på hur klustret ska samverka med andra element i Azure. Planera var kluster resurserna kommer att finnas i ditt privata nätverk och undernät och Bestäm var ditt Server lagrings utrymme ska vara. Se till att de klusternoder som du skapar är tillräckligt kraftfulla för att stödja ditt arbets flöde. 

Fortsätt att läsa om du vill veta mer.

## <a name="resource-group-and-network-infrastructure"></a>Resurs grupp och nätverks infrastruktur

Överväg var elementen i ditt AVERT vFXT för Azure-distribution ska vara. Diagrammet nedan visar ett möjligt arrangemang för det Avera vFXT för Azure-komponenter:

![Diagram som visar kluster styrenheten och virtuella kluster datorer i ett undernät. Runt under nätets gränser är en VNet-gränser. Inuti VNet är en sexhörning som representerar lagrings tjänstens slut punkt. den är ansluten med en streckad pil till en blob-lagring utanför VNet.](media/avere-vfxt-components-option.png)

Följ dessa rikt linjer när du planerar ditt AVERT vFXT Systems nätverks infrastruktur:

* Alla element ska hanteras med en ny prenumeration som skapats för den AVERT vFXT-distributionen. Fördelarna innefattar: 
  * Enklare kostnads uppföljning – Visa och granska alla kostnader från resurser, infrastruktur och Compute-cykler i en prenumeration.
  * Enklare rensning – du kan ta bort hela prenumerationen när du är klar med projektet.
  * Bekväm partitionering av resurs kvoter – skydda andra kritiska arbets belastningar från möjliga resurs begränsningar genom att isolera de vFXT-klienter och-kluster i en enda prenumeration. Detta förhindrar en konflikt när du hämtar ett stort antal klienter för ett arbets flöde med hög prestanda beräkning.

* Leta upp dina klient beräknings system nära vFXT-klustret. Backend-lagring kan vara mer fjärran sluten.  

* VFXT-klustret och den virtuella kluster styrenheten bör finnas i samma virtuella nätverk (VNet) i samma resurs grupp och använda samma lagrings konto. Mallen för att skapa automatiserade kluster hanterar detta i de flesta situationer.

* Klustret måste finnas i sitt eget undernät för att undvika IP-adress konflikter med klienter eller beräknings resurser. 

* Mallen för att skapa kluster kan skapa de flesta infrastruktur resurser som behövs för klustret, inklusive resurs grupper, virtuella nätverk, undernät och lagrings konton. Om du vill använda resurser som redan finns kontrollerar du att de uppfyller kraven i den här tabellen. 

  | Resurs | Använd befintlig? | Krav |
  |----------|-----------|----------|
  | Resursgrupp | Ja, om det är tomt | Måste vara tom| 
  | Lagringskonto | Ja om du ansluter en befintlig BLOB-behållare efter att klustret har skapats <br/>  Nej om du skapar en ny BLOB-behållare när klustret skapas | Den befintliga BLOB-behållaren måste vara tom <br/> &nbsp; |
  | Virtuellt nätverk | Ja | Måste inkludera en Storage Service-slutpunkt om du skapar en ny Azure Blob-behållare | 
  | Undernät | Ja |   |

## <a name="ip-address-requirements"></a>Krav för IP-adress 

Kontrol lera att klustrets undernät har ett stort tillräckligt IP-adressintervall för att stödja klustret. 

The aver vFXT-klustret använder följande IP-adresser:

* En IP-adress för kluster hantering. Den här adressen kan flyttas från nod till nod i klustret, men är alltid tillgänglig så att du kan ansluta till konfigurations verktyget AVERT på kontroll panelen.
* För varje klusternod:
  * Minst en IP-adress som riktas mot klienter. (Alla klient adresser hanteras av klustrets *vserver*, som kan flytta dem mellan noder efter behov.)
  * En IP-adress för kluster kommunikation
  * En instans-IP-adress (tilldelad till den virtuella datorn)

Om du använder Azure Blob Storage kan det även krävas IP-adresser från klustrets VNet:  

* Ett Azure Blob Storage-konto måste ha minst fem IP-adresser. Behåll detta krav i åtanke om du hittar Blob Storage i samma VNet som klustret.
* Om du använder Azure Blob Storage som ligger utanför det virtuella nätverket för klustret, bör du skapa en slut punkt för lagrings tjänsten i VNet. Den här slut punkten använder ingen IP-adress.

Du kan välja att hitta nätverks resurser och Blob Storage (om det används) i olika resurs grupper från klustret.

## <a name="vfxt-node-size"></a>vFXT Node-storlek

De virtuella datorer som fungerar som klusternoder bestämmer hur data flödet och lagrings kapaciteten för din cache är. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Varje vFXT-nod kommer att vara identisk. Det innebär att om du skapar ett kluster med tre noder kommer du att ha tre virtuella datorer av samma typ och storlek. 

| Instans typ | Virtuella processorer | Minne  | Lokal SSD-lagring  | Max antal datadiskar | Ej cachelagrad disk data flöde | NÄTVERKSKORT (antal) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 Mbit/s | 16 000 Mbit/s (8)  |

Disk-cache per nod kan konfigureras och kan Rage från 1000 GB till 8000 GB. 4 TB per nod är den rekommenderade cachestorleken för Standard_E32s_v3-noder.

Mer information om de här virtuella datorerna finns i Microsoft Azure-dokumentationen:

* [Minnesoptimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Konto kvot

Se till att prenumerationen har kapacitet att köra det AVERT vFXT-kluster och alla data behandlings-eller klient system som används. Läs [kvoten för vFXT-klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) för mer information.

## <a name="back-end-data-storage"></a>Data lagring på Server Sidan

Var ska det AVERT vFXT-kluster lagra dina data när de inte finns i cacheminnet? Bestäm om din arbets uppsättning ska lagras på lång sikt i en ny BLOB-behållare eller i ett befintligt lagrings system för moln eller maskin vara. 

Om du vill använda Azure Blob Storage för Server delen bör du skapa en ny behållare som en del av att skapa vFXT-klustret. Med det här alternativet skapas och konfigureras den nya behållaren så att den är redo att användas så fort klustret är klart. 

Läs [skapa AVERT vFXT för Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) för mer information.

> [!NOTE]
> Endast tomma Blob Storage-behållare kan användas som Core-för det AVERT vFXT systemet. VFXT måste kunna hantera objekt arkivet utan att befintliga data behöver bevaras. 
>
> Läs [Flytta data till vFXT-klustret](avere-vfxt-data-ingest.md) om du vill lära dig att kopiera data till klustrets nya behållare effektivt genom att använda klient datorer och vFXT-cachen aver.

Om du vill använda ett befintligt lokalt lagrings system måste du lägga till det i vFXT-klustret när det har skapats. Läs [Konfigurera lagring](avere-vfxt-add-storage.md) för detaljerade instruktioner om hur du lägger till ett befintligt lagrings system i det AVERT vFXT-klustret.

## <a name="cluster-access"></a>Kluster åtkomst 

AVERT vFXT för Azure-kluster finns i ett privat undernät och klustret har ingen offentlig IP-adress. Du måste ha någon metod för att komma åt det privata under nätet för kluster administration och klient anslutningar. 

Åtkomst alternativ är:

* Hopp värd – tilldela en offentlig IP-adress till en separat virtuell dator i det privata nätverket och Använd den för att skapa en SSL-tunnel till klusternoderna. 

  > [!TIP]
  > Om du anger en offentlig IP-adress på kluster styrenheten kan du använda den som hopp värd. Mer information finns [i kluster styrenheten som hopp värd](#cluster-controller-as-jump-host) .

* Virtuellt privat nätverk (VPN) – Konfigurera en punkt-till-plats-eller plats-till-plats-VPN-anslutning till ditt privata nätverk.

* Azure-ExpressRoute – konfigurera en privat anslutning via en ExpressRoute-partner. 

Mer information om de här alternativen finns i [Azure Virtual Network-dokumentationen om Internet kommunikation](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Kluster styrenhet som hopp värd

Om du anger en offentlig IP-adress på kluster styrenheten kan du använda den som en hopp värd för att kontakta det vFXT-kluster som finns utanför det privata under nätet. Men eftersom styrenheten har åtkomst behörighet för att ändra klusternoder skapar detta en liten säkerhets risk.  

För att förbättra säkerheten för en kontrollant med en offentlig IP-adress skapar distributions skriptet automatiskt en nätverks säkerhets grupp som begränsar inkommande åtkomst till port 22. Du kan skydda systemet ytterligare genom att låsa åtkomsten till ditt utbud av IP-adresser – det vill säga bara tillåta anslutningar från datorer som du vill använda för kluster åtkomst.

När du skapar klustret kan du välja om du vill skapa en offentlig IP-adress på kluster styrenheten. 

* Om du skapar ett nytt VNet eller ett nytt undernät, tilldelas kluster styrenheten en offentlig IP-adress.
* Om du väljer ett befintligt VNet och undernät kommer kluster styrenheten endast att ha privata IP-adresser. 

## <a name="vm-access-roles"></a>Åtkomst roller för virtuella datorer 

Azure använder [rollbaserad åtkomst kontroll](../role-based-access-control/index.yml) (RBAC) för att auktorisera de virtuella datorerna i klustret för att utföra vissa uppgifter. Till exempel behöver kluster styrenheten auktorisering för att skapa och konfigurera klusternodens virtuella datorer. Klusternoderna måste kunna tilldela eller tilldela om IP-adresser till andra klusternoder.

Två inbyggda Azure-roller används för de AVERT vFXT virtuella datorerna: 

* Kluster styrenheten använder den inbyggda rollen " [avere](../role-based-access-control/built-in-roles.md#avere-contributor)". 
* Klusternoder använder den inbyggda rollen " [aver operator](../role-based-access-control/built-in-roles.md#avere-operator) "

Om du behöver anpassa åtkomst roller för AVERT vFXT-komponenter måste du definiera en egen roll och sedan tilldela den till de virtuella datorerna när de skapas. Du kan inte använda distributions mal len i Azure Marketplace. Kontakta Microsofts kund tjänst och support genom att öppna ett ärende i Azure Portal enligt beskrivningen i [få hjälp med systemet](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Nästa steg: förstå distributions processen

[Distributions översikt](avere-vfxt-deploy-overview.md) ger en stor bild av alla steg som behövs för att skapa ett AVERT VFXT för Azure-systemet och få den redo att hantera data.  