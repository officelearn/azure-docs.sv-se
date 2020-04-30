---
title: Publicera ett erbjudande för virtuell dator på Azure Marketplace
description: Visar en lista över de steg som krävs för att publicera ett befintligt erbjudande om virtuell dator på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: f66ce0c15e976898d5022bf5705a82fe0969ec07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147666"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicera ett erbjudande för virtuell dator

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett erbjudande för virtuell Azure-dator](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

 Det sista steget, när du har definierat erbjudandet i portalen och skapat tillhör ande tekniska till gångar, är att skicka in erbjudandet om publicering. Följande diagram visar de huvudsakliga stegen i publicerings processen för att "gå live":

![Publicerings steg för erbjudande från virtuell dator](./media/publishvm_013.png)

I följande tabell beskrivs de här stegen och det finns en maximal tids beräkning för att slutföra slut för ande:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publicerings steg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera krav         | 15 min   | Information om erbjudandet och erbjudande inställningarna verifieras.                        |
| Verifiering av test enhet (valfritt) | 2 timmar | Om du har valt att aktivera test enheten, verifierar Microsoft test enhetens konfiguration, dess distribution och replikering genom de valda regionerna. |
| Certifiering                  | 3 dagar | Erbjudandet analyseras av Azure-certifierings teamet. I det här steget utförs genomsökningar efter virus, skadlig kod, säkerhets krav och säkerhets problem. Feedback ges om ett problem påträffas. |
| Etablering                   | 4 dagar   | VM-erbjudandet replikeras i produktions systemen för Marketplace.               |
| Registrering av paketering och skapande av lead | \<1 timme  | Erbjudandets tekniska till gångar är paketerade för kund användning och lead-systemen konfigureras och konfigureras. |
|  Publisher-signering             |  -        | Slutlig utgivar granskning och bekräftelse innan erbjudandet går live. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för att erbjuda information) för att kontrol lera att det uppfyller alla krav.  |
| Etablering                   | 4 dagar | Det slutliga virtuella dator erbjudandet replikeras i produktions system och regioner i Marketplace. | 
| Live                           | 4 dagar | VM-erbjudandet släpps, replikeras till de regioner som krävs och görs tillgängliga för allmänheten. |
|  |  |

Tillåt att den här processen slutförs i upp till 16 dagar.  När du har gått igenom dessa publicerings steg visas ditt virtuella dator erbjudande i [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

