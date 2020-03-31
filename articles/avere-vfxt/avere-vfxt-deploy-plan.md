---
title: Planera ditt Avere vFXT-system - Azure
description: Förklarar planering att göra innan du distribuerar Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: cd0c74c8aa40b3e96716ef37aa27b08b5f6aece1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547548"
---
# <a name="plan-your-avere-vfxt-system"></a>Planera för ditt Avere vFXT-system

I den här artikeln beskrivs hur du planerar ett nytt Avere vFXT för Azure-kluster som är placerat och dimensionerat på lämpligt sätt för dina behov.

Innan du går till Azure Marketplace eller skapar några virtuella datorer bör du tänka på följande:

* Hur interagerar klustret med andra Azure-resurser?
* Var ska klusterelement finnas i privata nätverk och undernät?
* Vilken typ av backend-lagring kommer du att använda och hur kommer klustret att komma åt det?
* Hur kraftfull behöver klusternoderna vara för att stödja arbetsflödet?

Läs vidare för att läsa mer.

## <a name="learn-the-components-of-the-system"></a>Lär dig komponenterna i systemet

Det kan vara bra att förstå komponenterna i Avere vFXT för Azure-systemet när du börjar planera.

* Klusternoder - Klustret består av tre eller flera virtuella datorer som konfigurerats som klusternoder. Fler noder ger systemet högre dataflöde och en större cache.

* Cache - Cachekapaciteten är jämnt fördelad mellan klusternoderna. Ange cachestorleken per nod när du skapar klustret. Nodstorlekarna läggs till för att bli den totala cachestorleken.

* Klusterstyrenhet - Klusterstyrenheten är ytterligare en virtuell dator som finns i samma undernät som klusternoderna. Styrenheten behövs för att skapa klustret och för pågående hanteringsuppgifter.

* Backend-lagring – De data som du vill ha cachelagrat lagras på lång sikt i ett maskinvarulagringssystem eller en Azure Blob-behållare. Du kan lägga till lagringsutrymme när du har skapat Avere vFXT för Azure-kluster, eller om du använder Blob-lagring kan du lägga till och konfigurera behållaren när du skapar klustret.

* Klienter - Klientdatorer som använder de cachelagrade filerna ansluter till klustret med hjälp av en sökväg till virtuella filer i stället för att komma åt lagringssystemen direkt. (Läs mer i [Mount Avere vFXT-klustret](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Prenumerations-, resursgrupps- och nätverksinfrastruktur

Tänk på var elementen i din Avere vFXT för Azure-distribution kommer att vara. Diagrammet nedan visar ett möjligt arrangemang för Avere vFXT för Azure-komponenter:

![Diagram som visar klusterstyrenheten och kluster-virtuella datorer i ett undernät. Runt undernätsgränsen finns en vnet-gräns. Inuti det virtuella nätverket finns en hexagon som representerar slutpunkten för lagringstjänster. Den är ansluten med en streckad pil till en Blob-lagring utanför det virtuella nätverket.](media/avere-vfxt-components-option.png)

Följ dessa riktlinjer när du planerar nätverket Avere vFXT-klustrets nätverksinfrastruktur:

* Skapa en ny prenumeration för varje Avere vFXT för Azure-distribution. Hantera alla komponenter i den här prenumerationen.

  Fördelarna med att använda en ny prenumeration för varje distribution är:
  * Enklare kostnadsspårning – Visa och granska alla kostnader från resurser, infrastruktur och beräkningscykler i en prenumeration.
  * Enklare rensning – Du kan ta bort hela prenumerationen när du är klar med projektet.
  * Bekväm partitionering av resurskvoter - Isolera Avere vFXT-klienter och kluster i en enda prenumeration för att skydda andra kritiska arbetsbelastningar från eventuell resursbegränsning. Den här separationen förhindrar konflikter när du tar upp ett stort antal klienter för ett högpresterande datorarbetsflöde.

* Leta reda på klientberäkningssystemen nära vFXT-klustret. Backend-lagring kan vara mer avlägset.  

* Leta upp vFXT-klustret och klusterstyrenheten VM tillsammans – närmare bestämt ska de vara:

  * I samma virtuella nätverk
  * I samma resursgrupp
  * Använda samma lagringskonto
  
  Mallen för att skapa kluster hanterar den här konfigurationen för de flesta situationer.

* Klustret måste finnas i sitt eget undernät för att undvika IP-adresskonflikter med klienter eller andra beräkningsresurser.

* Använd mallen för att skapa kluster för att skapa de flesta nödvändiga infrastrukturresurser för klustret, inklusive resursgrupper, virtuella nätverk, undernät och lagringskonton.

  Om du vill använda resurser som redan finns kontrollerar du att de uppfyller kraven i den här tabellen.

  | Resurs | Använda befintliga? | Krav |
  |----------|-----------|----------|
  | Resursgrupp | Ja, om den är tom | Måste vara tomt|
  | Lagringskonto | **Ja** om du ansluter en befintlig Blob-behållare efter att klustret har skapats <br/>  **Nej** om du skapar en ny Blob-behållare när kluster skapas | Befintlig Blob-behållare måste vara tom <br/> &nbsp; |
  | Virtuellt nätverk | Ja | Måste innehålla en slutpunkt för lagringstjänster om du skapar en ny Azure Blob-behållare |
  | Undernät | Ja | Det går inte att innehålla andra resurser |

## <a name="ip-address-requirements"></a>IP-adresskrav

Kontrollera att klustrets undernät har tillräckligt stort IP-adressintervall för att stödja klustret.

Avere vFXT-klustret använder följande IP-adresser:

* En IP-adress för klusterhantering. Den här adressen kan flyttas från nod till nod i klustret efter behov så att den alltid är tillgänglig. Använd den här adressen om du vill ansluta till konfigurationsverktyget för Avere Kontrollpanelen.
* För varje klusternod:
  * Minst en klientvänd IP-adress. (Alla klientvända adresser hanteras av klustrets *vserver*, som kan flytta IP-adresserna mellan noder efter behov.)
  * En IP-adress för klusterkommunikation
  * En instans IP-adress (tilldelad till den virtuella datorn)

Om du använder Azure Blob-lagring kan det också kräva IP-adresser från klustrets virtuella nätverk:  

* Ett Azure Blob-lagringskonto kräver minst fem IP-adresser. Tänk på det här kravet om du hittar Blob-lagring i samma virtuella nätverk som klustret.
* Om du använder Azure Blob-lagring som ligger utanför klustrets virtuella nätverk skapar du en slutpunkt för lagringstjänster i det virtuella nätverket. Slutpunkten använder ingen IP-adress.

Du har möjlighet att hitta nätverksresurser och Blob-lagring (om den används) i olika resursgrupper från klustret.

## <a name="vfxt-node-size"></a>vFXT-nodstorlek

De virtuella datorer som fungerar som klusternoder bestämmer begärandendataflöde och lagringskapaciteten för cacheminnet. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Varje vFXT-nod kommer att vara identisk. Det vill säga om du skapar ett trenodskluster har du tre virtuella datorer av samma typ och storlek.

| Typ av instans | Virtuella processorer | Minne  | Lokal SSD-lagring  | Maximalt antal datadiskar | Okopplat diskdataflöde | Nätverkskort (antal) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

Diskcachen per nod kan konfigureras och kan rasa från 1 000 GB till 8 000 GB. 4 TB per nod är den rekommenderade cachestorleken för Standard_E32s_v3 noder.

Mer information om dessa virtuella datorer finns i Microsoft Azure-dokumentationen: [Minnesoptimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Kontokvot

Se till att din prenumeration har kapacitet att köra Avere vFXT-klustret samt alla dator- eller klientsystem som används. Läs [Kvot för vFXT-klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) för mer information.

## <a name="back-end-data-storage"></a>Lagring av backend-data

Backend-lagringssystem både levererar filer till klustrets cache och tar även emot ändrade data från cacheminnet. Bestäm om din arbetsuppsättning ska lagras på lång sikt i en ny Blob-behållare eller i ett befintligt lagringssystem (moln eller maskinvara). Dessa backend-lagringssystem kallas *kärnfilers*.

### <a name="hardware-core-filers"></a>Hårdvara kärna filers

Lägg till maskinvarulagringssystem i vFXT-klustret när du har skapat klustret. Du kan använda en mängd populära maskinvarusystem, inklusive lokala system, så länge lagringssystemet kan nås från klustrets undernät.

Läs [Konfigurera lagring](avere-vfxt-add-storage.md) för detaljerade instruktioner om hur du lägger till ett befintligt lagringssystem i Avere vFXT-klustret.

### <a name="cloud-core-filers"></a>Filers i molnet

Avere vFXT för Azure-systemet kan använda tomma Blob-behållare för backend-lagring. Behållare måste vara tomma när de läggs till i klustret - vFXT-systemet måste kunna hantera sitt objektlager utan att behöva bevara befintliga data.

> [!TIP]
> Om du vill använda Azure Blob-lagring för serverdelen skapar du en ny behållare som en del av skapandet av vFXT-klustret. Mallen för klusterskapande kan skapa och konfigurera en ny Blob-behållare så att den är klar att användas så snart klustret är tillgängligt. Att lägga till en behållare senare är mer komplicerat.
>
> Mer information [finns i Skapa Avere vFXT för Azure.](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)

När du har lagt till den tomma Blob-lagringsbehållaren som en kärnfiler kan du kopiera data till den via klustret. Använd en parallell kopieringsmekanism med flera trådar. Läs [Flytta data till vFXT-klustret](avere-vfxt-data-ingest.md) för att lära dig hur du kopierar data till klustrets nya behållare effektivt med hjälp av klientdatorer och Avere vFXT-cachen.

## <a name="cluster-access"></a>Klusteråtkomst

Avere vFXT för Azure-klustret finns i ett privat undernät och klustret har ingen offentlig IP-adress. Du måste ha något sätt att komma åt det privata undernätet för klusteradministration och klientanslutningar.

Åtkomstalternativen inkluderar:

* Jump host - Tilldela en offentlig IP-adress till en separat virtuell dator i det privata nätverket och använda den för att skapa en SSL-tunnel till klusternoderna.

  > [!TIP]
  > Om du anger en offentlig IP-adress på klusterstyrenheten kan du använda den som hoppvärd. Läs [klusterstyrenhet som hoppvärd](#cluster-controller-as-jump-host) för mer information.

* Virtuellt privat nätverk (VPN) – Konfigurera en punkt-till-plats- eller plats-till-plats-VPN mellan ditt privata nätverk i Azure och företagsnätverk.

* Azure ExpressRoute - Konfigurera en privat anslutning via en ExpressRoute-partner.

Mer information om de här alternativen finns i [dokumentationen till Azure Virtual Network om internetkommunikation](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Klusterkontrollant som hoppvärd

Om du anger en offentlig IP-adress på klusterstyrenheten kan du använda den som hoppvärd för att kontakta Avere vFXT-klustret utanför det privata undernätet. Men eftersom styrenheten har behörighet att ändra klusternoder, skapar detta en liten säkerhetsrisk.

För att förbättra säkerheten för en styrenhet med en offentlig IP-adress skapar distributionsskriptet automatiskt en nätverkssäkerhetsgrupp som begränsar inkommande åtkomst till endast port 22. Du kan ytterligare skydda systemet genom att låsa åtkomsten till ditt utbud av IP-källadresser - det vill säga tillåt bara anslutningar från datorer som du tänker använda för klusteråtkomst.

När du skapar klustret kan du välja om du vill skapa en offentlig IP-adress på klusterstyrenheten eller inte.

* Om du skapar ett **nytt virtuellt nätverk** eller ett **nytt undernät**tilldelas klusterstyrenheten en **offentlig** IP-adress.
* Om du väljer ett befintligt virtuellt nätverk och undernät har klusterstyrenheten bara **privata** IP-adresser.

## <a name="vm-access-roles"></a>VM-åtkomstroller

Azure använder [rollbaserad åtkomstkontroll](../role-based-access-control/index.yml) (RBAC) för att auktorisera kluster-virtuella datorer för att utföra vissa uppgifter. Klusterstyrenheten behöver till exempel auktorisering för att skapa och konfigurera virtuella datorer för klusternoder. Klusternoder måste kunna tilldela eller tilldela om IP-adresser till andra klusternoder.

Två inbyggda Azure-roller används för virtuella Avere vFXT-datorer:

* Klusterstyrenheten använder den inbyggda rollen [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor).
* Klusternoder använder den inbyggda rollen [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Om du behöver anpassa åtkomstroller för Avere vFXT-komponenter måste du definiera din egen roll och sedan tilldela den till de virtuella datorerna när de skapas. Du kan inte använda distributionsmallen på Azure Marketplace. Kontakta Microsofts kundtjänst och support genom att öppna en biljett i Azure-portalen enligt beskrivningen i [Få hjälp med ditt system](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Nästa steg

[Distribution översikt](avere-vfxt-deploy-overview.md) ger storbildsvy över de steg som behövs för att skapa en Avere vFXT för Azure-system och få den redo att tjäna data.
