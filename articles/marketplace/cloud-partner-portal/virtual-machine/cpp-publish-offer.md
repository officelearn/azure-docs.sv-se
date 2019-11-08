---
title: Publicera ett erbjudande för virtuell dator på Azure Marketplace
description: Visar en lista över de steg som krävs för att publicera ett befintligt erbjudande om virtuell dator på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 1b07f3f3edab47f8f75835dffd4cc3f89f17ab63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824414"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicera ett erbjudande för virtuell dator

 Det sista steget, när du har definierat erbjudandet i portalen och skapat tillhör ande tekniska till gångar, är att skicka in erbjudandet om publicering. Följande diagram visar de huvudsakliga stegen i publicerings processen för att "gå live":

![Publicerings steg för erbjudande från virtuell dator](./media/publishvm_013.png)

I följande tabell beskrivs de här stegen och det finns en maximal tids beräkning för att slutföra slut för ande:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publicerings steg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera krav         | 15 min   | Information om erbjudandet och erbjudande inställningarna verifieras.                        |
| Verifiering av test enhet (valfritt) | 2 timmar | Om du har valt att aktivera test enheten, verifierar Microsoft test enhetens konfiguration, dess distribution och replikering genom de valda regionerna. |
| Certifiering                  | 3 dagar | Erbjudandet analyseras av Azure-certifierings teamet. I det här steget utförs genomsökningar efter virus, skadlig kod, säkerhets krav och säkerhets problem. Feedback ges om ett problem påträffas. |
| Etablering                   | 4 dagar   | VM-erbjudandet replikeras i produktions systemen för Marketplace.               |
| Registrering av paketering och skapande av lead | \< 1 timme  | Erbjudandets tekniska till gångar är paketerade för kund användning och lead-systemen konfigureras och konfigureras. |
|  Publisher-signering             |  -        | Slutlig utgivar granskning och bekräftelse innan erbjudandet går live. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för att erbjuda information) för att kontrol lera att det uppfyller alla krav.  |
| Etablering                   | 4 dagar | Det slutliga virtuella dator erbjudandet replikeras i produktions system och regioner i Marketplace. | 
| Live                           | 4 dagar | VM-erbjudandet släpps, replikeras till de regioner som krävs och görs tillgängliga för allmänheten. |
|  |  |

Tillåt att den här processen slutförs i upp till 16 dagar.  När du har gått igenom dessa publicerings steg visas ditt virtuella dator erbjudande i [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

