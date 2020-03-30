---
title: Azure Blockchain Service-konsortium
description: Översikt över hur Azure Blockchain-tjänsten implementerar konsortiekedjenätverk.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247623"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain Service-konsortium

Med Azure Blockchain Service kan du skapa privata konsortiekedjenätverk där varje blockchain-nätverk kan begränsas till specifika deltagare i nätverket. Endast deltagare i det privata konsortiet blockchain-nätverket kan visa och interagera med blockkedjan. Konsortienätverk i Azure Blockchain Service kan innehålla två typer av medlemsdeltagare:

* **Administratör** - Privilegierade deltagare som kan vidta konsortiehanteringsåtgärder och kan delta i blockchain-transaktioner.

* **Användare** - Deltagare som inte kan vidta några konsortiehanteringsåtgärder men som kan delta i blockchain-transaktioner.

Konsortienätverk kan vara en blandning av deltagarroller och kan ha ett godtyckligt antal av varje rolltyp. Det måste finnas minst en administratör.

Följande diagram visar ett konsortiumnätverk med flera deltagare:

![Nätverksdiagram för privat konsortium](./media/consortium/network-diagram.png)

Med konsortiumhantering i Azure Blockchain Service kan du hantera deltagare i konsortienätverket. Förvaltningen av konsortiet bygger på nätverkets konsensusmodell. I den aktuella förhandsversionen tillhandahåller Azure Blockchain Service en centraliserad konsensusmodell för konsortiehantering. Alla privilegierade deltagare med en administrerande roll kan vidta konsortiehanteringsåtgärder, till exempel lägga till eller ta bort deltagare från ett nätverk.

## <a name="roles"></a>Roller

Deltagare i ett konsortium kan vara individer eller organisationer och kan tilldelas en användarroll eller en administratörsroll. I följande tabell visas skillnaderna på hög nivå mellan de två rollerna:

| Åtgärd | Användarroll | Administratörsroll
|--------|:----:|:------------:|
| Skapa ny medlem | Ja | Ja |
| Bjud in nya medlemmar | Inga | Ja |
| Ange eller ändra medlemsdeltagareroll | Inga | Ja |
| Ändra medlemsvisningsnamn | Endast för egen medlem | Endast för egen medlem |
| Ta bort medlemmar | Endast för egen medlem | Ja |
| Delta i blockchain-transaktioner | Ja | Ja |

### <a name="user-role"></a>Användarroll

Användare är konsortiumdeltagare utan administratörsfunktioner. De kan inte delta i förvaltningen av medlemmar med anknytning till konsortiet. Användare kan ändra sitt medlemsvisningsnamn och ta bort sig själva från ett konsortium.

### <a name="administrator"></a>Administratör

En administratör kan hantera medlemmar inom konsortiet. En administratör kan bjuda in medlemmar, ta bort medlemmar eller uppdatera medlemsroller inom konsortiet.
Det måste alltid finnas minst en administratör inom ett konsortium. Den senaste administratören måste ange en annan deltagare som administratörsroll innan han lämnar ett konsortium.

## <a name="managing-members"></a>Hantera medlemmar

Endast administratörer kan bjuda in andra deltagare till konsortiet. Administratörer bjuder in deltagare med hjälp av sitt Azure-prenumerations-ID.

När deltagarna har bjudits in kan de gå med i blockchain-konsortiet genom att distribuera en ny medlem i Azure Blockchain Service. Om du vill visa och gå med i det inbjudna konsortiet måste du ange samma Azure-prenumerations-ID som används i inbjudan av nätverksadministratören.

Administratörer kan ta bort alla deltagare från konsortiet, inklusive andra administratörer. Medlemmar kan bara ta bort sig själva från ett konsortium.

## <a name="consortium-management-smart-contract"></a>Smart kontrakt för konsortiehantering

Konsortiehantering i Azure Blockchain Service sker via konsortiehanteringssmarta kontrakt. De smarta kontrakten distribueras automatiskt till dina noder när du distribuerar en ny blockchain-medlem.

Adressen till smart kontrakt för rotkonsortiehantering kan visas i Azure-portalen. **RootContract-adressen** finns i blockchain-medlemmens översiktsavsnitt.

![RootContract-adress](./media/consortium/rootcontract-address.png)

Du kan interagera med konsortiehanteringssmarta kontrakt med [powershell-modulen](manage-consortium-powershell.md)konsortiehantering, Azure-portalen eller direkt via det smarta kontraktet med azure blockchain-tjänsten som genererade Ethereum-kontot.

## <a name="ethereum-account"></a>Ethereum-konto

När en medlem skapas skapas en Ethereum-kontonyckel. Azure Blockchain Service använder nyckeln för att skapa transaktioner relaterade till konsortiehantering. Ethereum-kontonyckeln hanteras automatiskt av Azure Blockchain Service.

Medlemskontot kan visas i Azure-portalen. Medlemskontot finns i blockchain-medlemmens översiktsavsnitt.

![Medlemskonto](./media/consortium/member-account.png)

Du kan återställa ditt Ethereum-konto genom att klicka på ditt medlemskonto och ange ett nytt lösenord. Både Ethereum-kontoadressen och lösenordet återställs.  

## <a name="next-steps"></a>Nästa steg

Konsortiehanteringsåtgärder kan nås via PowerShell. Mer information finns [i Hantera konsortiemedlemmar i Azure Blockchain-tjänsten med PowerShell](manage-consortium-powershell.md).
