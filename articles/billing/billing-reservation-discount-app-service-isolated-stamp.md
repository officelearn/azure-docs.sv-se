---
title: Så tillämpas reservationsrabatter på Azure App Service Isolerad-stämplar
description: Lär dig hur reservationsrabatter tillämpas på Azure App Service Isolerad-stämplar.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2e9f2fd4541621e98bc1d46f23559993eeabe311
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226006"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Så tillämpas reservationsrabatter på Azure App Service Isolerad-stämplar

När du har köpt reserverad kapacitet för App Service Isolerad-stämpelavgift tillämpas rabatten automatiskt på stämpelavgiften i en region. Reservationsrabatten tillämpas på den användning som skickas av mätaren för isolerad stämpelavgift. Arbeten, ytterligare klientdelar samt andra resurser som associeras med stämpeln fortsätter att debiteras till det vanliga priset.

## <a name="reservation-discount-application"></a>Tillämpning av reservationsrabatter

Rabatten för App Service Isolerad-stämpelavgiften tillämpas per timme på isolerade stämplar som körs. Om du inte har någon stämpel distribuerad under en timme så går den reserverade kapaciteten för timmen förlorad. Den först inte vidare.

Efter köpet matchas den reservation som du köper till en isolerad stämpel som körs i en angiven region. Om du stänger av den stämpeln tillämpas reservationsrabatterna automatiskt på alla andra stämplar som körs i regionen. När det inte finns några stämplar tillämpas reservationen på nästa stämpel som skapas i regionen.

När stämplarna inte körs under en hel timme tillämpas reservationen automatiskt på andra matchande stämplar i samma region under samma timme.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Välj en stämpeltyp – Windows eller Linux

Som standard skickar en tom isolerad stämpel ut Windows-stämpelmätaren. Det gäller till exempel när inga arbeten distribueras. Den fortsätter att skicka ut mätaren när Windows-arbeten distribueras. Mätaren ändras till Linux-stämpelmätaren om du distribuerar ett Linux-arbete. Stämpeln skickar Windows-mätaren när både Linux-arbetet och Windows-arbetet distribueras.

Därmed kan stämpelmätaren ändras mellan Windows och Linux under stämpelns livslängd. Reservationer är specifika för ett visst operativsystem. Du behöver köpa en reservation som stöder de arbeten som du planerar att distribuera till stämpeln. Stämplar som endast är till för Windows samt blandade stämplar använder Windows-reservationen. Stämplar med Linux-exklusiva arbeten använder Linux-reservationen.

Den enda gång du bör köpa en Linux-reservation är om du planerar att _endast_ använda Linux-arbeten i stämpeln.

## <a name="discount-examples"></a>Exempel på rabatter

I följande exempel visas hur rabatten reserverad instans för isolerad stämpelavgift tillämpas beroende på distributionerna.

- **Exempel 1**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region utan App Service Isolerad-stämplar. Du distribuerar en ny stämpel till regionen och betalar reserverade priser för den stämpeln.
- **Exempel 2**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region där det redan finns en App Service Isolerad-stämpel distribuerad. Du börjar få det reserverade priset för den distribuerade stämpeln.
- **Exempel 3**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region där det redan finns en App Service Isolerad-stämpel distribuerad. Du börjar få det reserverade priset för den distribuerade stämpeln. Senare tar du bort stämpeln och distribuerar en ny. Du får det reserverade priset för den nya stämpeln. Rabatter förs inte vidare för varaktigheter utan distribuerade stämplar.
- **Exempel 4**: Du köper en instans av reserverad kapacitet för isolerad stämpel i en region och distribuerar sedan en ny stämpel i den regionen. När stämpeln ursprungligen distribueras utan arbetare skickar den Windows-stämpelmätaren. Ingen rabatt fås. När det första Linux-arbetet distribueras till stämpeln skickar det Linux-stämpelmätaren, och reservationsrabatten tillämpas. Om ett Windows-arbete senare distribueras till stämpeln återgår stämpelmätaren till Windows. Du får inte längre rabatt för reservationen av reserverad isolerad Linux-stämpel.

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).
- Om du vill veta mer om förhandsköp av reserverad kapacitet för App Service Isolerad-stämpel i syfte att minska utgifterna kan du läsa [Förskottsbetala för avgiften för Azure App Service Isolerad-stämpel med reserverad kapacitet](billing-prepay-app-service-isolated-stamp.md).
- Du kan läsa mer om Azure-reservationer i följande artiklar:
  - [Vad är Azure-reservationer?](billing-save-compute-costs-reservations.md)
  - [Hantera reservationer i Azure](billing-manage-reserved-vm-instance.md)
  - [Förstå reservationsanvändning för en prenumeration med Betala per användning-priser](billing-understand-reserved-instance-usage.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
