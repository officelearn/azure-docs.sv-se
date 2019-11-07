---
title: Azure blockchain service Consortium
description: Förstå hur Azure blockchain service använder ett privat konsortium
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ddb3f02662c0c71ebc90e1a740b4068d6fbcded4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577512"
---
# <a name="azure-blockchain-service-consortium"></a>Azure blockchain service Consortium

Med Azure blockchain-tjänsten kan du skapa privata konsortier blockchain nätverk där varje blockchain nätverk kan begränsas till särskilda deltagare i nätverket. Endast deltagare i det privata konsortiet blockchain-nätverket kan visa och interagera med blockchain. Konsortiet nätverk i Azure blockchain-tjänsten kan innehålla två typer av medlems deltagar roller:

* **Administratörer** – privilegierade deltagare som kan vidta hanterings åtgärder för konsortiet och kan delta i blockchain transaktioner.

* **Användare** – deltagare som inte kan vidta någon konsortiums hanterings åtgärd, men som kan delta i blockchain transaktioner.

Konsortier-nätverk kan vara en blandning av deltagar roller och kan ha ett godtyckligt antal varje roll typ. Det måste finnas minst en administratör.

Följande diagram visar ett konsortium-nätverk med flera deltagare:

![Nätverks diagram för privat konsortium](./media/consortium/network-diagram.png)

Med hantering av konsortier i Azure blockchain-tjänsten kan du hantera deltagare i konsortiet nätverk. Konsortiets hantering baseras på nätverkets vedertagna modell. I den aktuella för hands versionen tillhandahåller Azure blockchain-tjänsten en centraliserad konsensus modell för hantering av konsortiet. Alla privilegierade deltagare med en administrera-roll kan vidta hanterings åtgärder för konsortiet, till exempel lägga till eller ta bort deltagare från ett nätverk.

## <a name="roles"></a>Roller

Deltagare i ett konsortium kan vara individer eller organisationer och kan tilldelas en användar roll eller en administratörs roll. I följande tabell visas skillnaderna på hög nivå mellan de två rollerna:

| Åtgärd | Användar roll | Administratörs roll
|--------|:----:|:------------:|
| Skapa ny medlem | Ja | Ja |
| Bjud in nya medlemmar | Nej | Ja |
| Ange eller ändra rollen för medlems deltagare | Nej | Ja |
| Ändra medlems visnings namn | Endast för egen medlem | Endast för egen medlem |
| Ta bort medlemmar | Endast för egen medlem | Ja |
| Delta i blockchain transaktioner | Ja | Ja |

### <a name="user-role"></a>Användar roll

Användare är konsortiums deltagare utan Administratörs funktioner. De kan inte delta i hantering av medlemmar som är relaterade till konsortiet. Användare kan ändra sina medlems visnings namn och ta bort dem från ett konsortium.

### <a name="administrator"></a>Administratör

En administratör kan hantera medlemmar i konsortiet. En administratör kan bjuda in medlemmar, ta bort medlemmar eller uppdatera medlemmar i konsortiet.
Det måste alltid finnas minst en administratör i ett konsortium. Den senaste administratören måste ange en annan deltagare som administratörs roll innan du lämnar ett konsortium.

## <a name="managing-members"></a>Hantera medlemmar

Endast administratörer kan bjuda in andra deltagare till konsortiet. Administratörer bjuder in deltagare med sitt ID för Azure-prenumeration.

När de har bjudits in kan deltagarna delta i blockchain-konsortiet genom att distribuera en ny medlem i Azure blockchain-tjänsten. Om du vill visa och ansluta till det inbjudna konsortiet måste du ange samma ID för Azure-prenumerationen som används i inbjudan av nätverks administratören.

Administratörer kan ta bort alla deltagare från konsortiet, inklusive andra administratörer. Medlemmar kan bara ta bort sig själva från ett konsortium.

## <a name="consortium-management-smart-contract"></a>Smart kontrakt för konsortiums hantering

Hantering av konsortiet i Azure blockchain-tjänsten görs via de smarta avtalen för konsortiet Management. De smarta kontrakten distribueras automatiskt till noderna när du distribuerar en ny blockchain-medlem.

Du kan visa adressen till det smarta hanterings avtalet för rot konsortiet i Azure Portal. **RootContract-adressen** finns i avsnittet Översikt över blockchain-medlemmen.

![RootContract-adress](./media/consortium/rootcontract-address.png)

Du kan interagera med det smarta avtalet för konsortiet Management med PowerShell- [modulen](manage-consortium-powershell.md)för hantering av konsortiet, Azure Portal eller direkt via det smarta kontraktet med hjälp av det genererade Ethereum-kontot i Azure blockchain service.

## <a name="ethereum-account"></a>Ethereum-konto

När en medlem skapas skapas en konto nyckel för Ethereum. Azure blockchain-tjänsten använder nyckeln för att skapa transaktioner relaterade till hantering av konsortiet. Konto nyckeln för Ethereum hanteras automatiskt av Azure blockchain-tjänsten.

Medlems kontot kan visas i Azure Portal. Medlems kontot finns i avsnittet Översikt över blockchain-medlemmen.

![Medlems konto](./media/consortium/member-account.png)

Du kan återställa ditt Ethereum-konto genom att klicka på ditt medlems konto och ange ett nytt lösen ord. Både Ethereum-kontonamn och lösen ordet kommer att återställas.  

## <a name="next-steps"></a>Nästa steg

[Hantera medlemmar i Azure blockchain-tjänsten med hjälp av PowerShell](manage-consortium-powershell.md)
