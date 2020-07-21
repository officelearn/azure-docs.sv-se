---
title: Hanteringsåtgärder
titleSuffix: Azure SQL Managed Instance
description: Läs mer om drift tid och bästa praxis för hantering av Azure SQL-hanterade instanser.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531990"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Översikt över hanterings åtgärder för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>Vad är hanterings åtgärder?
Azure SQL-hanterad instans tillhandahåller hanterings åtgärder som du kan använda för att automatiskt distribuera nya hanterade instanser, uppdatera instans egenskaper och ta bort instanser när de inte längre behövs.

För att stödja [distributioner i virtuella Azure-nätverk](../../virtual-network/virtual-network-for-azure-services.md) och tillhandahålla isolering och säkerhet för kunder förlitar sig SQL-hanterade instanser på [virtuella kluster](connectivity-architecture-overview.md#high-level-connectivity-architecture). Virtuellt kluster representerar en dedikerad uppsättning isolerade virtuella datorer som distribueras i kundens virtuella nätverk under nät. Varje distribution av hanterade instanser i ett tomt undernät resulterar i grunden i en ny version av det virtuella klustret.

Efterföljande åtgärder på distribuerade hanterade instanser kan också ha effekter på det underliggande virtuella klustret. Dessa åtgärder påverkar varaktigheten för hanterings åtgärder, eftersom distribution av ytterligare virtuella datorer levereras med en kostnad som måste beaktas när du planerar nya distributioner eller uppdateringar till befintliga hanterade instanser.

Alla hanteringsåtgärder kan kategoriseras på följande sätt:

- Instans distribution (ny instans skapas).
- Instans uppdatering (ändring av instans egenskaper, till exempel virtuella kärnor eller reserverad lagring).
- Borttagning av instans.

## <a name="management-operations-duration"></a>Varaktighet för hanterings åtgärder
Åtgärder i virtuella kluster tar vanligt vis längst. Varaktigheten för åtgärder i virtuella kluster varierar – nedan visas de värden som du normalt förväntar dig, baserat på befintliga telemetridata för tjänsten:

- **Skapa virtuellt kluster**: skapandet är ett synkront steg i instans hanterings åtgärder. **90% av åtgärderna har slutförts på 4 timmar**.
- **Storleks ändring av virtuellt kluster (expandering eller krympning)**: utökning är ett synkront steg, medan krympningen utförs asynkront (utan inverkan på instans hanterings åtgärdernas varaktighet). **90% av kluster utökningar slutförs på mindre än 2,5 timmar**.
- **Borttagning av virtuellt kluster**: borttagning är ett asynkront steg, men det kan också [initieras manuellt](virtual-cluster-delete.md) på ett tomt virtuellt kluster, i vilket fall det körs synkront. **90% av de virtuella kluster borttagningarna slutförs om 1,5 timmar**.

Dessutom kan hantering av instanser också innehålla en av åtgärderna på värdbaserade databaser, vilket leder till längre varaktigheter:

- **Bifoga databasfiler från Azure Storage**: ett synkront steg, till exempel Compute (virtuella kärnor) eller lagrings skala upp eller ned i generell användning tjänst nivå. **90% av dessa åtgärder har slutförts på 5 minuter**.
- **Always on-tillgänglighets grupps dirigering**: ett synkront steg, till exempel Compute (virtuella kärnor) eller lagrings skalning på affärskritisk tjänst nivå samt i ändra tjänst nivån från Generell användning till affärskritisk (eller vice versa). Den här åtgärdens varaktighet är proportionerlig till den totala databas storleken samt den aktuella databas aktiviteten (antal aktiva transaktioner). Databas aktivitet vid uppdatering av en instans kan medföra betydande varianser för den totala varaktigheten. **90% av dessa åtgärder körs vid 220 GB/timme eller högre**.

I följande tabell sammanfattas åtgärder och typiska övergripande varaktigheter:

|Kategori  |Åtgärd  |Tids krävande segment  |Uppskattad varaktighet  |
|---------|---------|---------|---------|
|**Distribution** |Första instansen i ett tomt undernät|Skapa virtuellt kluster|90% av åtgärderna har slutförts på 4 timmar.|
|Distribution |Första instansen av en annan maskin varu generation i ett undernät som inte är tomt (till exempel första generation 5-instansen i ett undernät med generation 4 instanser)|Skapa virtuellt kluster *|90% av åtgärderna har slutförts på 4 timmar.|
|Distribution |Första instans skapandet av 4 virtuella kärnor i ett tomt eller icke-tomt undernät|Skapa virtuellt kluster * *|90% av åtgärderna har slutförts på 4 timmar.|
|Distribution |Efterföljande instans skapas i det icke-tomma under nätet (andra, tredje osv.)|Storleks ändring av virtuellt kluster|90% av åtgärderna har slutförts om 2,5 timmar.|
|**Uppdatera** |Ändring av instans egenskap (administratörs lösen ord, Azure AD-inloggning, Azure Hybrid-förmån flagga)|E.t.|Upp till 1 minut.|
|Uppdatera |Skalning av instans lagring upp/ned (Generell användning tjänst nivå)|Bifoga databasfiler|90% av åtgärderna har slutförts på 5 minuter.|
|Uppdatera |Skalning av instans lagring upp/ned (Affärskritisk tjänst nivå)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna har slutförts i 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme).|
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Generell användning)|-Storleks ändring av virtuellt kluster<br>-Bifoga databasfiler|90% av åtgärderna har slutförts om 2,5 timmar.|
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Affärskritisk)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna har slutförts i 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme).|
|Uppdatera |Instans tjänst nivå ändring (Generell användning till Affärskritisk och vice versa)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna har slutförts i 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme).|
|**Borttagning**|Borttagning av instans|Logg säkerhets kopiering för alla databaser|90% åtgärder har slutförts på upp till 1 minut.<br>OBS! om den sista instansen i under nätet tas bort, kommer den här åtgärden att schemalägga borttagning av virtuellt kluster efter 12 timmar. * *|
|Borttagning|Borttagning av virtuellt kluster (som användarinitierad åtgärd)|Borttagning av virtuellt kluster|90% av åtgärderna har slutförts på upp till 1,5 timmar.|

\*Det virtuella klustret skapas per maskin varu generation.

\*\*12 timmar är den aktuella konfigurationen men som kan ändras i framtiden, så ta inte ett hårt beroende av den. Om du behöver ta bort ett virtuellt kluster tidigare (till exempel för att frigöra under nätet) kan du se [ta bort ett undernät när du har tagit bort en hanterad instans](virtual-cluster-delete.md).

## <a name="instance-availability-during-management-operations"></a>Tillgänglighet för instanser under hanterings åtgärder

SQL-hanterad instans **är tillgänglig under uppdaterings åtgärder**, förutom ett kort stillestånd som orsakas av redundansväxlingen som inträffar i slutet av uppdateringen. Det tar vanligt vis upp till 10 sekunder, även om tids krävande transaktioner har avbrutits, tack vare den [påskyndade databas återställningen](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Det rekommenderas inte att skala beräkning eller lagring av Azure SQL-hanterade instanser eller ändra tjänst nivån samtidigt med tids krävande transaktioner (data import, data bearbetnings jobb, index återuppbyggnad osv.). Redundansväxling av databasen som utförs i slutet av åtgärden avbryter alla pågående transaktioner.

SQL-hanterad instans är inte tillgänglig för klient program under distributions-och borttagnings åtgärder.

## <a name="management-operations-cross-impact"></a>Hanterings åtgärder över-påverkan

Hanterings åtgärder på en hanterad instans kan påverka andra hanterings åtgärder för de instanser som placeras i samma virtuella kluster:

- **Långvariga återställnings åtgärder** i ett virtuellt kluster sätter igång andra instanser av att skapa eller skala i samma undernät.<br/>**Exempel:** Om det finns en tids krävande återställnings åtgärd och det finns en begäran om att skapa eller skala i samma undernät, tar det längre tid att slutföra den här begäran eftersom den väntar på att återställningen ska slutföras innan den fortsätter.
    
- **En efterföljande instans skapas eller skalnings** åtgärd spärras av en tidigare initierad instans eller instans skala som initierade storleken på det virtuella klustret.<br/>**Exempel:** Om det finns flera skapa-och/eller skalnings begär anden i samma undernät under samma virtuella kluster, och en av dem initierar en storlek för virtuella kluster, så kommer alla begär Anden som har skickats 5 + minuter efter den som krävde storleken på det virtuella klustret att ändra storlek längre än förväntat, eftersom dessa begär Anden måste vänta på att storleken ska slutföras innan den återupptas

- **Create/Scale-åtgärder som skickas i en 5-minuters period** kommer att grupperas och köras parallellt.<br/>**Exempel:** Det går bara att ändra storlek på ett virtuellt kluster för alla åtgärder som skickas i ett fönster med 5 minuter (mätning från tidpunkten för att köra den första åtgärden). Om en annan begäran skickas mer än 5 minuter efter att den första har skickats, väntar det att det virtuella klustrets storlek ska slutföras innan körningen påbörjas.

> [!IMPORTANT]
> Hanterings åtgärder som spärras på grund av att en annan åtgärd pågår kommer att återupptas automatiskt när villkoren för att fortsätta är uppfyllda. Det krävs ingen användar åtgärd för att återuppta tillfälligt pausade hanterings åtgärder.

## <a name="canceling-management-operations"></a>Avbryta hanterings åtgärder

I följande tabell sammanfattas möjligheten att avbryta vissa hanterings åtgärder och typiska övergripande varaktigheter:

Kategori  |Åtgärd  |Avbrytbar  |Beräknad tids längd för avbrott  |
|---------|---------|---------|---------|
|Distribution |Skapa instans |Nej |  |
|Uppdatera |Skalning av instans lagring upp/ned (Generell användning) |Nej |  |
|Uppdatera |Skalning av instans lagring upp/ned (Affärskritisk) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Generell användning) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Uppdatera |Virtuella kärnor (Instance Compute) skalar upp och ned (Affärskritisk) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Uppdatera |Instans tjänst nivå ändring (Generell användning till Affärskritisk och vice versa) |Ja |90% av åtgärderna har slutförts på 5 minuter. |
|Ta bort |Borttagning av instans |Nej |  |
|Ta bort |Borttagning av virtuellt kluster (som användarinitierad åtgärd) |Nej |  |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill avbryta hanterings åtgärden går du till bladet översikt och klickar på meddelande rutan för den pågående åtgärden. På den högra sidan visas en skärm med den pågående åtgärden och det kommer att finnas en knapp för att avbryta åtgärden. Efter den första klickningen uppmanas du att klicka igen och bekräfta att du vill avbryta åtgärden.

[![Avbryt åtgärd](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

När en Cancel-begäran har skickats och bearbetats får du ett meddelande om att sändningen har slutförts eller inte.

Om den skickade Cancel-begäran lyckas avbryts hanterings åtgärden på några minuter, vilket resulterar i ett haveri.

![Avbryter åtgärds resultat](./media/management-operations-overview/canceling-operation-result.png)

Om Cancel-begäran Miss lyckas eller om Avbryt-knappen inte är aktiv, innebär det att hanterings åtgärden har angett ett icke-avbrotts tillstånd och att det kan slutföras på några minuter. Hanterings åtgärden fortsätter att köras tills den har slutförts.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du inte redan har Azure PowerShell installerat, se [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

Om du vill avbryta hanterings åtgärden måste du ange namnet på hanterings åtgärden. Därför ska du först använda Hämta kommando för att hämta åtgärds listan och sedan avbryta en speciell åtgärd.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Detaljerade kommando förklaringar finns i [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) och [Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte redan har installerat Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill avbryta hanterings åtgärden måste du ange namnet på hanterings åtgärden. Därför ska du först använda Hämta kommando för att hämta åtgärds listan och sedan avbryta en speciell åtgärd.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Detaljerade kommando förklaringar finns i [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest).

---

## <a name="next-steps"></a>Nästa steg
- Information om hur du skapar din första hanterade instans finns i [snabb starts guide](instance-create-quickstart.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](../database/features-comparison.md).
- Mer information om VNet-konfiguration finns i [konfiguration av SQL Managed instance VNet](connectivity-architecture-overview.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- En själv studie kurs om hur du använder Azure Database Migration Service för migrering finns i [migrering av SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
