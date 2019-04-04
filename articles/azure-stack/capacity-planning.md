---
title: Kapacitetsplanering för Azure Stack | Microsoft Docs
description: Läs mer om kapacitetsplanering för Azure Stack-distributioner.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e4678b445dce5b337fb7d51e1b938adb944b4440
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648732"
---
# <a name="azure-stack-capacity-planning"></a>Azure Stack-kapacitetsplanering
När du utvärderar en Azure Stack-lösning finns maskinvara konfigurationsalternativ som har en direkt inverkan på den totala kapaciteten i Azure Stack-molnet. Det här är de klassiska val av CPU, minne densitet, lagringskonfiguration, och övergripande lösningen skala eller antalet servrar. Till skillnad från en traditionell virtualiseringslösning gäller inte det enkla aritmetiskt av dessa komponenter för att fastställa användbar kapacitet. Den första orsaken till detta är att Azure Stack har byggts för att vara värd för infrastruktur eller hantering av komponenterna i själva lösningen. Den andra orsaken är att några av lösningens kapaciteten är reserverad till stöd för återhämtning; uppdatering av lösningens programvara på ett sätt att minimera störningar för klienternas arbetsbelastningar.

> [!IMPORTANT]
> Information om tillhandahållna kapacitetsplanering och tillhörande kalkylblad är avsedda enbart som en guide som hjälper dig att planera för Azure Stack och konfigurationsbeslut. De är inte avsedda att fungera som en ersättning för undersökning och analys. 

## <a name="compute-and-storage-capacity-planning"></a>Beräkning och lagring av kapacitetsplanering
Ett Azure Stack-lösningen har utformats som ett hyperkonvergerat kluster för beräkning, nätverk och lagring. Detta tillåter effektiv användning eller delning av alla maskinvarukapacitet i klustret, kallas en Skalningsenhet för Azure Stack på ett sätt som ger tillgänglighet och skalbarhet. All infrastruktur programvara finns i en uppsättning virtuella datorer (VM) och delar samma fysiska servrar som de virtuella klientdatorerna. Alla virtuella datorer som sedan hanteras av den Skalningsenhet Windows Server-Klusterteknikerna och enskilda Hyper-V-instanser. Den här metoden förenklar anskaffning och hantering av en Azure Stack-lösning och ger för dataförflyttning och skalbarhet för alla tjänster (klient och infrastruktur) över hela Skalningsenheten.

Endast fysisk resurs som inte har etablerats i en Azure Stack-lösning över är serverminne. Andra resurser, CPU-kärnor, nätverkets bandbredd och lagringskapacitet, kommer vara överbelastade för bästa användning av tillgängliga resurser. Vid beräkning av tillgänglig kapacitet för en lösning för är fysisk serverminne den viktigaste deltagaren. Användning av andra resurser är sedan förstå förhållandet mellan överetablering som är möjligt och vad som kan användas för avsedd arbetsbelastning.

Cirka 30 virtuella datorer används för att vara värd för Azure Stack-infrastruktur och sammanlagt förbrukar ungefär 230 GB minne och 140 virtuella kärnor. Anledningen till att det här antalet virtuella datorer är att uppfylla nödvändiga service-uppdelning så att de uppfyller säkerhet, skalbarhet, underhåll och korrigeringar krav. Den här strukturen för intern tjänst kan framtida införandet av nya infrastrukturtjänster medan de utvecklar.

För att stödja automatisk uppdatering av alla fysisk server och infrastruktur programvarukomponenter, eller korrigerings- och uppdateringsinformation, infrastruktur och användar-VM förbrukar placeringar inte alla minnesresurser Skalningsenheten. En del av den totala mängden minnet på alla servrar på en Skalningsenhet blir inte allokerat till stöd för krav på lösningens elasticitet. Till exempel när den fysiska servern Windows Server-avbildning uppdateras flyttas de virtuella datorerna som finns på servern någon annanstans i Skalningsenheten medan serverns Windows Server-avbildningar har uppdaterats. När uppdateringen är klar har servern startats om och returnerade underhåller arbetsbelastningar. Målet för korrigeringar och uppdateringar för en Azure Stack-lösning är att undvika att behöva stoppa värdbaserade virtuella datorer. I sträva efter att uppfylla målet, tilldelats ett absolut minsta krav minneskapacitet för en server för att tillåta att flytta virtuella datorer i Skalningsenheten. Den här placering och förflyttning gäller både infrastrukturens virtuella datorer och virtuella datorer som skapas på uppdrag av användaren eller klientorganisationen för Azure Stack-lösning. De slutliga implementeringsresultat är så att mängden minne som är reserverade för nödvändig VM-transporten kan vara mycket mer än en enda server kapacitet på grund av placering utmaningar när virtuella datorer har olika minneskrav. Ytterligare prestanda i kategorin på minnesanvändningen är för den Windows Server-instansen. Grundläggande operativsystem-instans för varje server förbrukar minne för operativsystemet och dess virtuella sidan tabeller tillsammans med det minne som används av Hyper-V i hanteringen av var och en av de värdbaserade virtuella datorerna.

VM-kapacitet bestäms av tillgängligt minne. De virtuella kärnorna till fysiska kärnor förhållanden är exempel på hur VM-densiteten ändra tillgängliga CPU-kapacitet, såvida inte lösningen har konstruerats med ett större antal fysiska kärnor (en annan processor har valts). Detsamma gäller för lagringskapacitet och lagringskapacitet för cache.

Ovanstående exempel för VM-densitet är endast för förklaring. Det finns ytterligare komplexiteten i beräkningar i support. Kontakta Microsoft eller en lösningspartner krävs att förstå ytterligare alternativ för kapacitetsplanering och den resulterande, tillgängliga kapaciteten.

Resten av det här avsnittet beskriver krav för distribution av Azure Stack för beräkning och lagring. Använd informationen för att få en grundläggande förståelse för vilka komponenter som krävs och deras minsta konfigurationsvärden. Mer information finns också för att beskriva hur lösningen har konfigurerats för tillgänglig kapacitet och eventuella begränsningar av systemet när det gäller kapaciteten för klient-kapacitet och prestanda.

> [!NOTE]
> Kapacitetskrav för kapacitetsplanering för nätverk är minimal eftersom bara storleken på det offentliga VIP kan konfigureras. Information om hur du lägger till flera offentliga IP-adresser till Azure Stack finns i [lägger du till offentliga IP-adresser](azure-stack-add-ips.md).


## <a name="next-steps"></a>Nästa steg
[Compute-kapacitetsplanering](capacity-planning-compute.md)
