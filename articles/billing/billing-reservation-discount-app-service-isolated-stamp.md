---
title: Hur reservations rabatter gäller för Azure App Service isolerade stämplar
description: Lär dig hur reservations rabatter gäller Azure App Service isolerade stämplar.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298261"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Hur reservations rabatter gäller för Azure App Service isolerade stämplar

När du har köpt App Service Isolerad stämpel avgift reserverad kapacitet tillämpas reservations rabatten automatiskt på den stämpla avgiften i en region. Reservations rabatten gäller den användning som genereras av den isolerade stämpelns avgifts mätare. Arbetare, ytterligare klient delar och andra resurser som är kopplade till stämpeln fortsätter att debiteras enligt normal taxa.

## <a name="reservation-discount-application"></a>Reservations rabatt program

Avgifts rabatten för App Service Isolerad stämpeln används för att köra isolerade stämplar per timme. Om du inte har en stämpel som har distribuerats i en timme slösas den reserverade kapaciteten under den timmen. Den överför inte.

Efter köpet matchas den reservation som du köper till en isolerad stämpel som körs i en angiven region. Om du stänger av den stämpeln tillämpas reservations rabatter automatiskt på alla andra stämplar som körs i regionen. När det inte finns några stämplar tillämpas reservationen på nästa stämpel som skapats i regionen.

När stämplarna inte körs under en hel timme, tillämpas reservationen automatiskt på andra matchande stämplar i samma region under samma timme.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Välj en Stamp-typ – Windows eller Linux

En tom isolerad stämpel genererar Windows Stamp-mätare som standard. Till exempel när inga arbetare distribueras. Den fortsätter att generera mätaren när Windows-arbetare distribueras. Mätaren ändras till Linux-stämpelns mätare om du distribuerar en Linux-arbetsprocess. Stämpeln genererar Windows-mätaren när både Linux-och Windows-arbetare distribueras.

Därför kan Stamp-mätaren ändras mellan Windows och Linux under stämpelns livs längd. Under tiden är reservationer ett speciellt operativ system. Du måste köpa en reservation som stöder de anställda som du planerar att distribuera till stämpeln. Endast Windows-stämplar och blandade stämplar använder Windows-reservationen. Stämplar med endast Linux-arbetare använder Linux-reservationen.

Den enda gången du ska köpa en Linux-reservation är när du planerar att _bara_ ha Linux-arbetare i stämpeln.

## <a name="discount-examples"></a>Rabatt exempel

I följande exempel visas hur den reserverade instans rabatten för den isolerade stämpeln gäller, beroende på distributionerna.

- **Exempel 1**: Du köper en instans av isolerad reserverad Stamp-kapacitet i en region utan att App Service Isolerad stämplar. Du distribuerar en ny stämpel till regionen och betalar reserverade priser för den stämpeln.
- **Exempel 2**: Du köper en instans av isolerad reserverad Stamp-kapacitet i en region som redan har en App Service Isolerad-stämpel distribuerad. Du får börja ta emot den reserverade taxan för den distribuerade stämpeln.
- **Exempel 3**: Du köper en instans av isolerad reserverad Stamp-kapacitet i en region med en App Service Isolerad-stämpel som redan har distribuerats. Du får börja ta emot den reserverade hastigheten på den distribuerade stämpeln. Senare tar du bort stämpeln och distribuerar en ny. Du får den reserverade taxan för den nya stämpeln. Rabatter överför inte för varaktigheter utan distribuerade stämplar.
- **Exempel 4**: Du köper en instans av kapaciteten för isolerad Linux-standardstämpel i en region och distribuerar sedan en ny stämpel till regionen. När stämpeln ursprungligen distribueras utan arbetare, så genererar den Windows Stamp-mätaren. Ingen rabatt tas emot. När den första Linux-arbetsprocessen har distribuerats, genererar den Linux-stämpelns mätare och reservations rabatten gäller. Om en Windows-anställd senare distribueras till Stamp, återställs stämpelns mätare till Windows. Du får inte längre rabatt för den isolerade Linux-reservationen.

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [hantera Azure reservations](billing-manage-reserved-vm-instance.md).
- Om du vill veta mer om förskotts betalning App Service Isolerad stämpel reserverad kapacitet för att spara pengar, se betalning [för Azure App Service isolerad stämpel med reserverad kapacitet](billing-prepay-app-service-isolated-stamp.md).
- Mer information om Azure Reservations finns i följande artiklar:
  - [Vad är Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Hantera reservationer i Azure](billing-manage-reserved-vm-instance.md)
  - [Förstå reservations användningen för en prenumeration med priser för betala per användning](billing-understand-reserved-instance-usage.md)
  - [Förstå reservations användningen för företags registreringen](billing-understand-reserved-instance-usage-ea.md)
