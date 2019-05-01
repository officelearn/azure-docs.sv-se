---
title: Publicera ett erbjudande för virtuell dator i Azure Marketplace
description: Visar de steg som krävs för att publicera en befintlig virtuell dator erbjuder på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 6796c2871cf8a5928beed2ab557cefdfe8ecaae9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938597"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicera ett erbjudande för virtuell dator

 Det sista steget är när du har definierat erbjudandet i portalen och skapade de associerade tekniska resurser att skicka erbjudande för publicering. Följande diagram visar huvudstegen inom publiceringsprocessen ”gå live”:

![Publicera stegen för den virtuella datorn erbjuder](./media/publishvm_013.png)

I följande tabell beskrivs de här stegen och ger en uppskattning för maximal tid för slutförande:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publiceringen**           | **Time**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera förutsättningar         | 15 min   | Ger information och erbjuder inställningarna verifieras.                        |
| Testa enheten verifiering (valfritt) | 2 timmar | Om du har valt för att aktivera Test Drive verifierar Microsoft Test Drive-konfiguration, dess distribution och replikering via valda regioner. |
| Certifiering                  | 3 dagar | Erbjudandet analyseras av Azure-certifiering-teamet. Det här steget utför genomsökningar efter virus, skadlig kod, säkerhet och säkerhetsproblem. Feedback tillhandahålls om ett problem hittas. |
| Etablering                   | fyra dagar   | Erbjudande för virtuell dator replikeras i marketplace produktionssystem.               |
| Paketering och registrering för lead-generering | \< 1 timme  | Erbjudandets tekniska resurser som är packade för kundens användning och lead-system har konfigurerats och konfigurera. |
|  Publisher-signering             |  -        | Sista publisher granska och bekräfta innan erbjudandet lanseras. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav.  |
| Etablering                   | fyra dagar | Slutförda erbjudande för virtuell dator replikeras i regionerna och marketplace produktionssystem. | 
| Live                           | fyra dagar | Erbjudande för virtuell dator är publicerat, replikeras till de nödvändiga regionerna och göras tillgängliga för allmänheten. |
|  |  |

Tillåt upp till 16 dagarna för den här processen skulle slutföras.  När du går igenom stegen publicera ditt erbjudande för virtuell dator visas i den [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

