---
title: "Återställa från oåterkallelig dataförlust i Azure-stacken använder infrastruktur-säkerhetskopieringstjänsten | Microsoft Docs"
description: "När ett oåterkalleligt fel gör att Azure-stacken misslyckas, kan återställa infrastrukturdata när återupprättar Azure Stack-distribution."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: mabrigg
ms.openlocfilehash: 141641b01b338e3426861dad7424a1de1bd2c63c
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="recover-from-catastrophic-data-loss"></a>Återställa från oåterkallelig dataförlust

*Gäller för: Azure Stack integrerat system.*

Azure-stacken kör Azure-tjänster i ditt datacenter. Azure-stacken kan köras i så liten som fyra noder som är installerad i ett enda rack miljöer. Däremot körs Azure i mer än 40 områden i flera datacenter och flera zoner i varje region. Användarresurser som kan sträcka sig över flera servrar, rack, Datacenter och regioner. Med Azure-stacken har du för närvarande endast möjlighet att distribuera hela molnet till en enda ställning. Detta visar ditt moln för risk för katastrofer i datacenter eller fel på grund av större buggar. När en katastrof inträffar frånkopplas Azure Stack-instans. Alla data är potentiellt allvarligt.

Du kan behöva reparera en enda infrastruktur-tjänst eller återställa en hel instans av Azure-stacken beroende på orsaken till förlust av data. Du kan även behöva återställa till annan maskinvara på samma plats eller på en annan plats.

Det här scenariot adresser återställning vid fel hela installationen av utrustningen och Omdistributionen av det privata molnet.

| Scenario                                                           | Förlust av data                            | Överväganden                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Återställa från oåterkallelig dataförlust på grund av programfel för katastrofåterställning eller produkt | Alla data för infrastruktur- och användar- och app | Användarprogram och data är skyddade separat från infrastrukturdata |

## <a name="workflows"></a>Arbetsflöden

Transporten för att skydda Azure startar börjar med att säkerhetskopiera data för infrastruktur- och app/separat. Det här dokumentet beskriver hur du skyddar infrastrukturen. 

![Första distributionen av Azure-stacken](media\azure-stack-backup\azure-stack-backup-workflow1.png)

I värsta fall där alla data går förlorade kan är återställa Azure-stacken processen att unika infrastrukturdata återställas till att distributionen av Azure-stacken och alla användardata. 

![Distribuera Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Återställ

Om det är oåterkallelig dataförlust men maskinvaran kan fortfarande användas, krävs Omdistributionen av Azure-stacken. Du kan ange den lagringsplats och de autentiseringsuppgifter som krävs för att komma åt säkerhetskopieringar under omdistributionen. I det här läget finns behöver du inte ange de tjänster som ska återställas. Infrastruktur för säkerhetskopiering Controller lägger in plan status som en del av arbetsflödet.

Om det finns en katastrof som återger maskinvaran inte kan användas, går bara Omdistributionen på ny maskinvara. Omdistributionen kan ta flera veckor innan ersättning av maskinvara är sorteras och anländer i datacentret. Återställning av data från plan kan när som helst. Återställning stöds inte om versionen av omdistribuerade instansen är mer än en version som är större än den version som används i den senaste säkerhetskopieringen. 

| Distributionsläget | Startpunkt | Slutpunkt                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ren installation   | Baslinje build | OEM distribuerar Azure Stack och uppdaterar till den senaste versionen.                                                                                                                                          |
| Återställningsläge   | Baslinje build | OEM distribuerar Azure Stack i återställningsläge och hanterar den version som matchar krav utifrån den senaste säkerhetskopian som är tillgängliga. OEM-tillverkaren har slutförts distributionen genom att uppdatera till senaste versionen. |

## <a name="data-in-backups"></a>Data i säkerhetskopieringar

Azure-stacken stöder en typ av distribution kallas återställningsläge i molnet. Det här läget används bara om du väljer att återställa Azure Stack efter en katastrof eller produktfel renderas lösningen oåterkalleligt. Det här distributionsläget kan inte återställa några av de data som lagras i lösningen. Omfattningen för det här distributionsläget är begränsad till återställa följande uppgifter:

 - Indata för distribution
 - Internt identitetssystem
 - Federerad identifiera configuration (frånkopplade distributioner)
 - Rotcertifikat som används av interna certifikatutfärdare
 - Azure Resource Manager configuration användardata, till exempel prenumerationer, planer, erbjudanden och kvoter för lagring, nätverk och beräkna resurser
 - KeyVault hemligheter och valv
 - RBAC principtilldelningar och rolltilldelningar 

Ingen av användaren infrastruktur som en tjänst (IaaS) eller en plattform som en tjänst (PaaS) resurser återställs under distributionen. Det vill säga går IaaS-VM, storage-konton, blobbar, tabeller, nätverkskonfigurationen och så vidare förlorade. Syftet med molnet återställning är att kontrollera din operatörer och användare kan logga in igen på portalen när distributionen är klar. Användare som loggar in igen visas inte någon av sina resurser. Användarna har sina prenumerationer återställas och tillsammans med som ursprungligt planer och innehåller principer som definieras av administratören. Användare som loggar in till systemet körs under samma begränsningar som införts av den ursprungliga lösningen före haveriet. Efter återställningen av molnet operatorn manuellt återställa mervärde och tredje parts RPs och associerade data.

## <a name="next-steps"></a>Nästa steg

 - Lär dig mer om metodtips för [med hjälp av tjänsten infrastruktur säkerhetskopiering](azure-stack-backup-best-practices.md).
