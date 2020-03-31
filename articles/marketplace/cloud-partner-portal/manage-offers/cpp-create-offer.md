---
title: Skapa Marketplace-erbjudanden | Azure Marketplace
description: Skapa erbjudanden på Azure- och AppSource Marketplaces med hjälp av Cloud Partner Portal
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: dsindona
ms.openlocfilehash: d14940eff569a80759e9db0bfa2d6d1541a01377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286480"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Skapa Azure Marketplace- och AppSource-erbjudanden

Ett viktigt syfte med Cloud Partner Portal är att göra det möjligt för utgivare att skapa (och sedan publicera) erbjudanden till Microsoft Azure och AppSource Marketplaces.  Den här åtgärden börjar alltid med att välja önskad erbjudandetyp på [menyn Nytt erbjudande](../portal-tour/cpp-new-offer-menu.md).  Som svar visas lämplig sida **för nytt erbjudande** för den erbjudandetypen.  Följande bild visar till exempel standardsidan för **nya erbjudanden** för en Azure-programtyp.

![Standardsida för nytt erbjudande](./media/new-offer-page.png)

Det finns två flikval i det vågräta menyradet som visas högst upp på den här sidan: 
- **Fliken Redigerare** - Aktiverar inmatning av information och uppladdning av tillgångar för den nya erbjudandeinstansen.  Den här fliken visas som standard.
- **Fliken Status** - Anger publiceringsstatus och visar eventuella validerings- och granskningsproblem. 

När du skapar ett erbjudande använder du fliken **Redigerare** för att ange information om erbjudandet. 

## <a name="editing-operations"></a>Redigera åtgärder

Det vågräta verktygsfältet, som finns ovanför inmatningsområdet för data, visar följande knappar:

|   Button (Knapp)    |   Syfte                                                          |
|   ------    |  --------                                                          |
| **Spara**    | Sparar alla ändringar av den senaste informationen.  Du måste spara ändringar manuellt innan du navigerar bort från sidan eller så går ändringarna förlorade. | 
| **Ta bort** | Ignorerar de senaste ändringarna av datainmatningen (sedan den senaste spara)             |
| **Jämför** | Jämför tillståndet för det aktuella erbjudandet med det publicerade erbjudandet.  Aktiveras först efter att ett erbjudande har publicerats.  |
| **Publicera** | Påbörjar publiceringsprocessen för det här erbjudandet                       |
| **Ta bort**  | Tar bort det här erbjudandet när det har skapats men innan det har publicerats. |
|   |   |


## <a name="editing-tabs"></a>Redigera flikar

När du skapar ett erbjudande anger du de data som krävs och valfria på varje flik som finns i den vänstra vertikala kolumnen på sidan **Nytt erbjudande.**  Standardkontroller för användargränssnitt – till exempel textrutor, nedrullningsbara menyer och kryssrutor – visas för datainsamling.  Även om den specifika samlingen redigeringsflikar beror på erbjudandetypen, visas några av de vanligaste flikarna i följande tabell.

|      Fliknamn       |   Syfte                                                            |
|      --------       |   -------                                                            |
| **Inställningar för erbjudande**  | Samlar in information om erbjudande och utgivarens identitet.                    |
| **SKU: er**            | Definierar de tekniska och affärsmässiga egenskaperna för varje SKU-version (Stock-Keeping Unit) av erbjudandet |
| **Test Drive**      | För de typer som stöder den här valfria funktionen definierar du en demonstration för ditt erbjudande.  Mer information finns i [Vad är Provkörning?](../test-drive/what-is-test-drive.md)  |
| **Marknadsplats** eller **skyltfönster** | Samlar in textsträngar, dokument och bilder som används för att lista erbjudandet på marknadsplatsen |
| **Support**         | Samlar in kontaktinformation för kund-, ingenjörs- och onlinesupport  |
|  |  |

Innehållet på de på samma sätt som namngivna flikarna kan skilja sig åt mellan olika erbjudandetyper.  Erbjudandespecifika uppgifter om dessa flikar finns i avsnittet "Skapa erbjudande" för varje erbjudandetyp.


## <a name="next-steps"></a>Nästa steg

När du har skapat och sparat ett erbjudande och före eller efter publiceringen kan du [visa dess status](./cpp-view-status-offer.md).
