---
title: Skapa Marketplace - erbjudanden i Azure Marketplace | Microsoft Docs
description: Skapa erbjudanden på Azure och AppSource marknadsplatser med partnerportalen i molnet
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pbutlerm
ms.openlocfilehash: af9b34d90098409135020fa8a45ecd0253f25b22
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454552"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Skapa erbjudanden för Azure Marketplace och AppSource

En viktig syftet med partnerportalen i molnet är att aktivera utgivare kan skapa (och sedan publicera) erbjuder till Microsoft Azure och AppSource marknadsplatser.  Den här åtgärden alltid börjar med att välja typ av önskad erbjudande från den [New erbjuder menyn](../portal-tour/cpp-new-offer-menu.md).  Rätt svar **nytt erbjudande** visas för den typ av erbjudande.  Följande bild visar exempelvis standard **nytt erbjudande** för en typ av Azure-program.

![Nytt erbjudande-standardsida](./media/new-offer-page.png)

Det finns två fliken Alternativ i vågräta menyraden visas överst på den här sidan: 
- **Redigeraren** fliken – gör det möjligt för inmatning av information och ladda upp med tillgångar för den nya instansen av erbjudandet.  Den här fliken visas som standard.
- **Status för** fliken – ger Publiceringsstatus och visar en lista över eventuella problem med att verifiera och granska. 

När du skapar ett erbjudande kan du använda den **redigeraren** flik för att ange information om erbjudandet. 

## <a name="editing-operations"></a>Redigera operations

Vågrät verktygsfältet ovanför området indata visar följande knappar:

|   Button (Knapp)    |   Syfte                                                          |
|   ------    |  --------                                                          |
| **Spara**    | Sparar ändringar post senaste data.  Manuellt måste du spara ändringarna innan du lämnar sidan eller dina ändringar försvinner. | 
| **Ta bort** | Ignorerar de senaste ändringarna av data post (sedan senaste gången)             |
| **Jämför** | Jämför tillståndet för det aktuella erbjudandet med publicerade erbjudande.  Endast aktiverad när ett erbjudande har publicerats.  |
| **Publicera** | Börjar publiceringsprocessen för det här erbjudandet                       |
| **Ta bort**  | Tar bort det här erbjudandet när den har skapats, men innan den har publicerats. |
|   |   |


## <a name="editing-tabs"></a>Redigera flikar

När du skapar ett erbjudande kan du ange obligatoriska och valfria data i varje flik finns i den vänstra lodräta kolumnen i den **nytt erbjudande** sidan.  Standard gränssnittskontroller – till exempel textrutor, nedrullningsbara menyer och kryssrutor--visas för insamling av data.  Även om specifik samling av redigering flikar är beroende av typ av erbjudande, visas i följande tabell några av de vanliga flikarna.

|      Fliknamn       |   Syfte                                                            |
|      --------       |   -------                                                            |
| **Erbjudandeinställningar**  | Samlar in erbjudandet och utgivare identitetsinformation.                    |
| **SKU: er**            | Definierar de tekniska och egenskaperna för varje lagerhållningsenhet (SKU) version av ditt erbjudande |
| **Testkör**      | För de typer som har stöd för denna valfria funktion, definierar du en demonstration för ditt erbjudande.  Mer information finns i [vad är en provkörning?](../test-drive/what-is-test-drive.md)  |
| **Marketplace** eller **butik** | Samlar in textsträngar, dokument och bilder som används för att lista erbjudandet i marketplace |
| **Support**         | Samlar in kontaktuppgifter för kunden, teknik och support på nätet  |
|  |  |

Innehållet i flikarna liknande namn kan variera mellan olika erbjudandetyper.  Erbjudande-specifik information om de här flikarna finns i avsnittet ”Skapa erbjudandet” för varje typ av erbjudande.


## <a name="next-steps"></a>Nästa steg

När du skapar och sparar ett erbjudande och innan eller efter att du publicerar den, kan du [visas dess status](./cpp-view-status-offer.md).
