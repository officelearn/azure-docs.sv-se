---
title: Publicera ett erbjudande om en virtuell dator på Azure Marketplace
description: Visar de steg som krävs för att publicera ett befintligt erbjudande om en virtuell dator på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: bb875a5c4ab1b898b64fe22140414e5d5b7830b8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273791"
---
# <a name="publish-a-virtual-machine-offer"></a>Publicera ett erbjudande om en virtuell dator

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Virtual Machine-erbjudande](https://aka.ms/CreateAzureVMoffer) för att hantera dina migrerade erbjudanden.

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

