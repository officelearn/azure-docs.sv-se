---
title: Skapa Marketplace-erbjudanden | Azure Marketplace
description: Skapa erbjudanden på Azure-och AppSource-Marketplace med hjälp av Cloud Partner Portal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pabutler
ms.openlocfilehash: 4aa4e154c99f20963485a98d1a21af4ae2b7a1b1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828101"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Skapa Azure Marketplace-och AppSource-erbjudanden

Ett viktigt syfte med Cloud Partner Portal är att låta utgivare skapa (och publicera) erbjudanden till Microsoft Azure-och AppSource-Marketplace.  Den här åtgärden börjar alltid med att välja önskad erbjudande typ från [menyn ny erbjudande](../portal-tour/cpp-new-offer-menu.md).  Som svar visas lämplig **ny erbjudande** sida för den erbjudande typen.  Följande bild visar till exempel standard sidan **ny erbjudande** för en typ av Azure-program.

![Ny standard sida för erbjudande](./media/new-offer-page.png)

Det finns två tillgängliga flikar i den vågräta meny raden som visas längst upp på sidan: 
- Fliken **redigerare** – möjliggör inmatning av information och överföring av till gångar för den nya erbjudande instansen.  Den här fliken visas som standard.
- Fliken **status** – visar publicerings status och listar eventuella verifierings-och gransknings problem. 

När du skapar ett erbjudande använder du fliken **redigerare** för att ange information om erbjudandet. 

## <a name="editing-operations"></a>Redigerings åtgärder

Det vågräta verktygsfältet, som finns ovanför data inmatnings ytan, visar följande knappar:

|   Button (Knapp)    |   Syfte                                                          |
|   ------    |  --------                                                          |
| **Spara**    | Sparar alla nya ändringar av data poster.  Du måste spara ändringarna manuellt innan du navigerar bort från sidan, annars går ändringarna förlorade. | 
| **Avvisa** | Ignorerar de senaste data post ändringarna (sedan senaste sparande)             |
| **Compare** | Jämför status för det aktuella erbjudandet med det publicerade erbjudandet.  Endast aktive rad efter att ett erbjudande har publicerats.  |
| **Publicera** | Börjar publicerings processen för det här erbjudandet                       |
| **Ta bort**  | Tar bort det här erbjudandet när det har skapats, men innan det har publicerats. |
|   |   |


## <a name="editing-tabs"></a>Redigera flikar

När du skapar ett erbjudande anger du de obligatoriska och valfria data på varje flik som finns i den vänstra lodräta kolumnen på sidan **ny erbjudande** .  Standard användar gränssnitts kontroller – till exempel text rutor, nedrullningsbara menyer och kryss rutor – visas för data insamling.  Även om den speciella samlingen av redigerings flikar är beroende av erbjudande typen visas några av de vanliga flikarna i följande tabell.

|      Fliknamn       |   Syfte                                                            |
|      --------       |   -------                                                            |
| **Erbjudande inställningar**  | Samlar in information om erbjudandet och utgivarens identitet.                    |
| **SKU: er**            | Definierar de tekniska och affärs egenskaperna för varje lagerhållnings enhet (SKU)-version av erbjudandet |
| **Test enhet**      | För de typer som stöder denna valfria funktion definierar en demonstration för ditt erbjudande.  Mer information finns i [Vad är Test Drive?](../test-drive/what-is-test-drive.md)  |
| **Marketplace** eller **butik** | Samlar in text strängar, dokument och bilder som används för att Visa erbjudandet i Marketplace |
| **Support**         | Samlar in kontakt information för kund, teknik och onlinesupport  |
|  |  |

Innehållet i liknande namngivna flikar kan skilja sig åt mellan olika typer av erbjudanden.  Erbjudande-detaljerad information om dessa flikar finns i avsnittet "skapa erbjudande" för varje erbjudande typ.


## <a name="next-steps"></a>Nästa steg

När du har skapat och sparat ett erbjudande och innan eller efter att du har publicerat det kan du [Visa dess status](./cpp-view-status-offer.md).
