---
title: Migrera äldre Azure DNS Private Zones till den nya Resource Manager
description: Den här guiden innehåller stegvisa anvisningar om hur du migrerar äldre privata DNS-zoner till senaste modellen
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: e7ebbf35cd572601f02a69930b58811686a92c86
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276099"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrera äldre privata Azure DNS-zoner till den nya resource Manager

Vi skickade en ny API-resursen modell för privata Azure DNS-zoner som en del av förhandsversionen för uppdatering. Uppdatera förhandsvisning tillhandahåller nya funktioner och tar bort flera begränsningar och restriktioner för den första allmänna tillgängliga förhandsversionen. Dock dra nytta av funktionerna är inte tillgängliga för privata DNS-zoner som har skapats med hjälp av äldre API. Om du vill få fördelarna med den nya versionen, måste du migrera dina äldre privata DNS-zon resurser till den nya resursen modellen. Migreringsprocessen är enkel och tillhandahåller vi ett PowerShell.skript för att automatisera processen. Den här guiden innehåller stegvisa anvisningar för att migrera dina privata Azure DNS-zoner till nya resurs-modellen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har installerat senaste versionen av Azure PowerShell. Mer information om Azure PowerShell (Az) och hur du installerar den besök https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Kontrollera att du har Az.PrivateDns-modulen för Azure PowerShell installerad. Installera den här modulen, öppna en upphöjd PowerShell-fönster (administrativa läge) och ange följande kommando

```powershell
Install-Module -Name Az.PrivateDns -AllowPrerelease
```

>[!IMPORTANT]
>Migreringsprocessen automatiserad helt och förväntas inte orsaka några driftstopp. Men om du använder privata Azure DNS-zoner (förhandsversion) i en produktionsmiljö bör du köra under migreringsprocessen under ett tidsintervall för planerat underhåll. Se till att du inte ändrar konfiguration eller post-uppsättningar med en privat DNS-zoner när du kör migreringsskriptet.

## <a name="installing-the-script"></a>Installera skriptet

Öppna en upphöjd PowerShell-fönster (administrativa läge) och kör följande kommando

```powershell
install-script PrivateDnsMigrationScript
```

Ange ”A” när du uppmanas att installera skriptet

![Installera skriptet](./media/private-dns-migration-guide/install-migration-script.png)

Du kan också hämta den senaste versionen av PowerShell-skriptet i https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

## <a name="running-the-script"></a>Skriptet har körts

Kör följande kommando för att köra skriptet

```powershell
PrivateDnsMigrationScript.ps1
```

![Skriptet har körts](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Ange prenumerations-ID och logga in på Azure

Du uppmanas att ange prenumerations-ID som innehåller de privata DNS-zoner som du vill migrera. Du att ombes att logga in på ditt Azure-konto. Slutför inloggningen så att skriptet kan komma åt privata DNS-zon resurserna i prenumerationen.

![Logga in till Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Välj DNS-zoner som du vill migrera

Skriptet med hämta en lista över alla privata DNS-zoner i prenumerationen och be dig att bekräfta vilka som du vill migrera. Ange ”A” för att migrera alla privata DNS-zoner. När du kör det här steget, skriptet skapar nya privata DNS-zoner med nya resursmodell och kopiera sedan data till den nya DNS-zonen. Det här steget kommer inte att ändra din befintliga privata DNS-zoner i ändå.

![Välj DNS-zoner](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Växla DNS-matchningen som de nya DNS-zonerna

När zoner och poster har kopierats till den nya resurs-modellen, uppmanas du att växla DNS-matchning till den nya DNS-zoner i skriptet. Det här steget tar bort associationen mellan äldre privata DNS-zoner och dina virtuella nätverk. När zonen äldre är länkat till de virtuella nätverken, skulle de nya DNS-zoner i ovanstående steg automatiskt ta över DNS-matchning för dessa virtuella nätverk.

Välj ”A” för att växla DNS-matchning för alla virtuella nätverk.

![Växla namnmatchning](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Kontrollera DNS-matchning

Innan du fortsätter, kontrollera att DNS-matchningen på DNS-zoner fungerar som förväntat. Du kan logga in på ditt virtuella azure-datorer och problemet nslookup-frågor mot migrerade zonerna för att kontrollera att DNS-matchning fungerar.

![Verifiera namnmatchning](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Om du upptäcker att du inte matchar DNS-frågor, Vänta några minuter och försök frågorna. Om DNS-frågor fungerar som förväntat, anger du ”Y” när skriptet uppmanas du att ta bort det virtuella nätverket från den privata DNS-zonen.

![Bekräfta namnmatchning](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Om på grund av någon anledning DNS matchning mot migrerade zonerna inte fungerar som förväntat, anger du ”n” i steget ovan och DNS-matchningen övergår i skriptet tillbaka till äldre zoner. Skapa ett supportärende och vi kan hjälpa dig med migreringen av dina DNS-zoner.

## <a name="cleanup"></a>Rensa

Det här steget tar bort de äldre DNS-zonerna och att köras endast när du har kontrollerat att DNS-matchningen fungerar som förväntat. Du uppmanas att ta bort varje privata DNS-zonen. Ange ”Y” i varje Kommandotolken när du har verifierat att DNS-matchning för de zonerna fungerar korrekt.

![Rensa](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Uppdatera din automation

Om du använder automation, inklusive mallar, PowerShell-skript eller anpassad kod som utvecklats med SDK: N, måste du uppdatera ditt automation om du vill använda den nya resurs-modellen för privata DNS-zoner. Nedan finns länkar till nya privata DNS CLI/PS/SDK-dokumentation.
* [Azure DNS privata zoner REST API](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Azure privata DNS-zoner CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Azure privata DNS-zoner PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Azure DNS private zones SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Behöver du mer hjälp?

Skapa ett supportärende om du behöver ytterligare hjälp med processen eller på grund av någon anledning listade stegen ovan inte fungerar för dig. Innehålla avskrift-filen som genereras av PowerShell-skriptet med ditt supportärende.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs mer om några vanliga [privat zon scenarier](./private-dns-scenarios.md) som kan genomföras med privata zoner i Azure DNS.

* För vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteende kan du förväntar dig för vissa typer av åtgärder, finns i [privata DNS-vanliga frågor och svar](./dns-faq-private.md).

* Läs mer om DNS-zoner och poster genom att besöka [DNS-zoner och poster översikt](dns-zones-records.md).

* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
