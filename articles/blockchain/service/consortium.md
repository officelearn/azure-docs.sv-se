---
title: Azure Blockchain-tjänsten Consortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027922"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain-tjänsten Consortium

Med Azure Blockchain-tjänsten kan kan du skapa privata consortium blockchain nätverk där varje blockchain-nätverket kan begränsas till specifika deltagare i nätverket. Endast deltagare i det privata consortium blockchain-nätverket kan visa och interagera med blockchain. Consortium nätverk i Azure Blockchain-tjänsten kan innehålla två typer av medlem deltagare roller:

* **Administratören** -privilegierad deltagare som kan utföra åtgärder för hantering av consortium och kan delta i blockchain transaktioner.

* **Användaren** -deltagare som kan inte vidta några åtgärder för hantering av consortium men kan delta i blockchain transaktioner.

Consortium nätverk kan vara en blandning av deltagare roller och kan ha ett valfritt antal varje rolltyp. Det måste finnas minst en administratör.

I följande diagram visas ett konsortienätverk med flera deltagare:

![Privata consortium nätverksdiagram](./media/consortium/network-diagram.png)

Med consortium management i Azure Blockchain Service kan hantera du deltagare i consortium network. Hantering av consortium baseras på konsensus-modell för nätverket. I den aktuella förhandsversionen tillhandahåller Azure Blockchain Service en centraliserad konsensus-modell för hantering av consortium. En privilegierad part med en administrera roll kan ta consortium hanteringsåtgärder som att lägga till eller ta bort deltagare från ett nätverk.

## <a name="roles"></a>Roller

Deltagare i ett konsortium kan vara enskilda personer och organisationer och kan tilldelas en roll eller en administratörsroll. I följande tabell visas de övergripande skillnaderna mellan de två rollerna:

| Åtgärd | Användarroll | Administratörsroll
|--------|:----:|:------------:|
| Skapa ny medlem | Ja | Ja |
| Bjuda in nya medlemmar | Nej | Ja |
| Ange eller ändra deltagare medlemsroller | Nej | Ja |
| Ändra visningsnamnet för medlem | Endast för egen medlem | Endast för egen medlem |
| Ta bort medlemmar | Endast för egen medlem | Ja |
| Delta i blockchain-transaktioner | Ja | Ja |

### <a name="user-role"></a>Användarroll

Användarna är consortium deltagare med inga administratörsfunktioner. De inte kan ingå i hanteringen av medlemmar till consortium. Användare kan ändra deras medlem visningsnamn och kan ta bort själva från en consortium.

### <a name="administrator"></a>Administratör

En administratör kan hantera medlemmar i consortium. En administratör kan bjuda in medlemmar, ta bort medlemmar eller uppdatera medlemmar roller i consortium.
Det måste alltid finnas minst en administratör i en consortium. Den sista administratören måste ange en annan part som administratörsroll innan de lämnar en consortium.

## <a name="managing-members"></a>Hantera medlemmar

Endast administratörer kan bjuda in andra deltagare till consortium. Administratörer bjuda in deltagare med sina Azure-prenumeration-ID.

När du uppmanas, deltagare kan ansluta till blockchain-consortium genom att distribuera en ny medlem i Azure Blockchain-tjänsten. Om du vill visa och delta i den inbjudna consortium, måste du ange samma ID för Azure-prenumeration som används i inbjudan av nätverksadministratören.

Administratörer kan ta bort alla deltagare från consortium, inklusive andra administratörer. Medlemmar kan bara avregistrera sig från en consortium.

## <a name="consortium-management-smart-contract"></a>Consortium management smarta kontrakt

Consortium hantering i Azure Blockchain Service görs via consortium management smarta kontrakt. Smarta kontrakt distribueras till noderna automatiskt när du distribuerar en ny blockchain-medlem.

Adressen till roten consortium management smarta kontrakt kan ses i Azure-portalen. Den **RootContract adress** finns i översiktsavsnittet för blockchain medlem.

![RootContract adress](./media/consortium/rootcontract-address.png)

Du kan interagera med det consortium management smarta kontrakt med consortium management [PowerShell-modulen](manage-consortium-powershell.md), Azure portal, eller direkt via den smarta kontrakt med hjälp av tjänsten Azure Blockchain genereras Ethereum konto.

## <a name="ethereum-account"></a>Ethereum-konto

När en medlem skapas, skapas en Ethereum-kontonyckel. Azure Blockchain Service använder nyckeln för att skapa transaktioner som rör hantering av consortium. Nyckeln till Ethereum hanteras automatiskt av Azure Blockchain-tjänsten.

Medlemskonton kan ses i Azure-portalen. Medlem-kontot finns i översiktsavsnittet för blockchain medlem.

![Medlemskonto](./media/consortium/member-account.png)

Du kan återställa ditt Ethereum-konto genom att klicka på din medlemskonto och ange ett nytt lösenord. Både den Ethereum-adressen för kontot och lösenordet återställs.  

## <a name="next-steps"></a>Nästa steg

[Hantera medlemmar i Azure Blockchain-tjänsten med hjälp av PowerShell](manage-consortium-powershell.md)
