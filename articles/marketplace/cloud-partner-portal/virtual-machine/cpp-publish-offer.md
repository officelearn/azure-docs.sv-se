---
title: Publicera ett erbjudande om en virtuell dator på Azure Marketplace
description: Visar de steg som krävs för att publicera ett befintligt erbjudande om en virtuell dator på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: c35f721131b997dcfdb0f23a91a39329168b757c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277352"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicera ett erbjudande om en virtuell dator

 Det sista steget, när du har definierat erbjudandet i portalen och skapat de associerade tekniska tillgångarna, är att skicka erbjudandet för publicering. Följande diagram visar de viktigaste stegen i publiceringsprocessen för att "publicera":

![Publiceringssteg för erbjudande om virtuella datorer](./media/publishvm_013.png)

I följande tabell beskrivs de här stegen och en maximal tidsuppskattning för att de ska kunna slutföras:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publiceringssteg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validera förutsättningar         | 15 min   | Erbjudandeinformation och erbjudandeinställningar valideras.                        |
| Validering av testkörning (valfritt) | 2 timmar | Om du har valt att aktivera Test Drive validerar Microsoft testenhetskonfigurationen, distributionen och replikeringen via de valda områdena. |
| Certifiering                  | 3 dagar | Erbjudandet analyseras av Azure Certification Team. Det här steget kommer att utföra genomsökningar efter virus, skadlig kod, säkerhetsefterlevnad och säkerhetsproblem. Feedback ges om ett problem hittas. |
| Etablering                   | 4 dagar   | VM-erbjudandet replikeras i marknadsplatsproduktionssystem.               |
| Registrering av förpackning och leadgenerering | \<1 timme  | Erbjudandets tekniska tillgångar är paketerade för kundanvändning och leadsystemen är konfigurerade och konfigurerade. |
|  Signering av utgivare             |  -        | Slutlig utgivare granskning och bekräftelse innan erbjudandet går live. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för erbjudandeinformation) för att kontrollera att det uppfyller alla dina krav.  |
| Etablering                   | 4 dagar | Slutfört VM-erbjudande replikeras i marknadsplatsproduktionssystem och regioner. | 
| Live                           | 4 dagar | VM-erbjudandet släpps, replikeras till de regioner som krävs och görs tillgängligt för allmänheten. |
|  |  |

Tillåt upp till 16 dagar för att den här processen ska slutföras.  När du har gått igenom de här publiceringsstegen visas ditt VM-erbjudande på [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

