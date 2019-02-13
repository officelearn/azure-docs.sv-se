---
title: Återställa från oåterkallelig dataförlust i Azure Stack med infrastruktur Backup-tjänsten | Microsoft Docs
description: När ett oåterkalleligt fel gör Azure Stack misslyckas, kan återställa infrastrukturdata när du återupprättar distributionen av Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 3fd91ef17415e2c6ebb735fdedb098f8aa319d8c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207423"
---
# <a name="recover-from-catastrophic-data-loss"></a>Återställa från oåterkallelig dataförlust

*Gäller för: Integrerade Azure Stack-system.*

Azure Stack Azure-tjänster som körs i ditt datacenter och kan köras i miljöer som är så litet som fyra noder som är installerad i ett enda rack. Däremot körs Azure på fler än 40 regioner i flera datacenter och flera zoner i varje region. Användarresurser som kan sträcka sig över flera servrar, rack, Datacenter och regioner. Med Azure Stack har du för närvarande endast möjlighet att distribuera hela molnet till ett enda rack. Detta visar molnet för risk för katastrofer i ditt datacenter eller fel som beror på större produktbuggar. När en katastrof inträffar, frånkopplas Azure Stack-instans. Alla data som är potentiellt oåterkalleligt.

Beroende på de grundläggande orsakerna till förlust av data, kan du behöva reparera en enda infrastruktur-tjänst eller återställa en hel Azure Stack-instans. Du kan även behöva återställa till annan maskinvara på samma plats eller på en annan plats.

Det här scenariot adresser återställa hela installationen om det uppstår ett fel av utrustningen och omdistribution av det privata molnet.

| Scenario                                                           | Förlust av data                            | Överväganden                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Återställa från oåterkallelig dataförlust på grund av haveri eller produkt bugg | Alla data för infrastruktur och användaren och appen | Användarprogram och dina data är skyddade separat från infrastrukturdata |

## <a name="workflows"></a>Arbetsflöden

Vägen för att skydda Azure startar börjar med säkerhetskopiering av infrastruktur- och app/klienttrafik data separat. Det här dokumentet beskriver hur du skyddar infrastrukturen. 

![Första distributionen av Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow1.png)

I värsta fall där alla data går förlorade kan du återställa Azure Stack är processen med att återställa informationen infrastruktur som är unika för den distributionen av Azure Stack och alla användardata. 

![Distribuera om Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Återställ

Om det är oåterkallelig dataförlust, men maskinvaran som kan fortfarande användas, krävs omdistribution av Azure Stack. Du kan ange lagringsplats och autentiseringsuppgifter som krävs för att komma åt säkerhetskopior under en ny distribution. I det här läget finns behöver inte ange de tjänster som måste återställas. Infrastruktur för säkerhetskopiering Controller lägger in status för plan som en del av arbetsflödet.

Om det finns en katastrof som återger maskinvaran som inte kan användas, är omdistribution endast möjligt på ny maskinvara. Omdistributionen kan ta flera veckor medan ersättning av maskinvara är sorterade och tas emot i datacentret. Återställning av data från plan går när som helst. Återställning stöds inte om versionen av omdistribuerade instansen är mer än en version som är större än den version som används i den senaste säkerhetskopieringen. 

| Distributionsläge | Startpunkt | Slutpunkt                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ren installation   | Baslinje-version | OEM distribuerar Azure Stack och uppdaterar till den senaste versionen.                                                                                                                                          |
| Återställningsläge   | Baslinje-version | OEM distribuerar Azure Stack i återställningsläge och hanterar den version som matchar krav baserat på den senaste säkerhetskopian som är tillgängliga. OEM-tillverkaren slutförs distributionen genom att uppdatera till senaste versionen som stöds. |

## <a name="data-in-backups"></a>Data i säkerhetskopior

Azure Stack har stöd för en typ av distribution kallas återställningsläge för molnet. Det här läget används bara om du väljer att återställa Azure Stack efter en katastrof eller produktfel återges lösningen oåterkalleligt. Det här distributionsläget återställer inte någon av de data som lagras i lösningen. Omfattningen för det här distributionsläget är begränsad till återställa följande data:

 - Distributionsindata
 - Intern identitetssystem
 - Federerad identifiera konfigurationen (frånkopplade distributioner)
 - Rotcertifikat som används av interna certifikatutfärdare
 - Azure Resource Manager configuration användardata, till exempel prenumerationer, planer, erbjudanden och kvoter för lagring, nätverk och beräkna resurser
 - KeyVault-hemligheter och valv
 - RBAC principtilldelningar och rolltilldelningar 

Ingen av användaren infrastruktur som en tjänst (IaaS) eller en plattform som en tjänst (PaaS) resurser återställs under distributionen. Det vill säga går virtuella IaaS-datorer, lagringskonton, blobbar, tabeller, nätverkskonfiguration och så vidare förlorade. Syftet med molnet är att se till att dina operatörer och användare kan logga in igen på portalen när distributionen är klar. Användare som loggar in igen ser inte någon av sina resurser. Användarna har sina prenumerationer som har återställts och tillsammans med som ursprungligt planer och innehåller principer som definieras av administratören. Användare som loggar in till systemet fungerar under samma begränsningar som införts av den ursprungliga lösningen före haveriet. Efter återställningen av molnet operatorn manuellt kan återställa mervärde och tredje parts RPs och associerade data.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om metodtips för [med Backup-tjänsten infrastruktur](azure-stack-backup-best-practices.md).
