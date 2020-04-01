---
title: Migrera äldre Azure DNS Private Zones till ny resursmodell
titleSuffix: Azure DNS
description: Den här guiden innehåller steg för steg instruktioner om hur du migrerar äldre privata DNS-zoner till den senaste resursmodellen
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939355"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrera äldre privata Azure DNS-zoner till ny resursmodell

Under offentlig förhandsversion skapades privata DNS-zoner med hjälp av "dnszones"-resurs med egenskapen "zoneType" inställd på "Privat". Sådana zoner kommer inte att stödjas efter den 31 december 2019 och måste migreras till GA-resursmodell som använder sig av resurstypen "privateDnsZones" i stället för "dnszones". Migreringsprocessen är enkel och vi har tillhandahållit ett PowerShell-skript för att automatisera den här processen. Den här guiden innehåller steg för steg-instruktioner för att migrera dina privata Azure DNS-zoner till den nya resursmodellen.

Så här ta reda på de dnszones-resurser som kräver migrering. kör kommandot nedan i Azure CLI.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Krav

Kontrollera att du har installerat den senaste versionen av Azure PowerShell. Mer information om Azure PowerShell (Az) och hur du installerar det finns påhttps://docs.microsoft.com/powershell/azure/new-azureps-module-az

Kontrollera att du har Az.PrivateDns-modulen för Azure PowerShell installerat. Om du vill installera den här modulen öppnar du ett upphöjt PowerShell-fönster (administrativt läge) och anger följande kommando

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Migreringsprocessen är helt automatiserad och förväntas inte orsaka några driftstopp. Men om du använder Azure DNS privata zoner (förhandsversion) i en kritisk produktionsmiljö bör du köra följande migreringsprocess under ett planerat underhållstidsfönster. Se till att du inte ändrar konfigurationen eller postuppsättningarna för en privat DNS-zon när du kör migreringsskriptet.

## <a name="installing-the-script"></a>Installera skriptet

Öppna ett upphöjt PowerShell-fönster (administrativt läge) och kör följande kommando

```powershell
install-script PrivateDnsMigrationScript
```

Ange "A" när du uppmanas att installera skriptet

![Installera skriptet](./media/private-dns-migration-guide/install-migration-script.png)

Du kan också manuellt hämta den senaste versionen av PowerShell-skriptet påhttps://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Migreringsskriptet får inte köras i Azure-molnskalet och måste köras i en virtuell dator eller lokal dator som är ansluten till internet.

## <a name="running-the-script"></a>Köra skriptet

Kör följande kommando för att köra skriptet

```powershell
PrivateDnsMigrationScript.ps1
```

![Köra skriptet](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Ange prenumerations-ID och inloggning till Azure

Du uppmanas att ange prenumerations-ID som innehåller de privata DNS-zoner som du tänker migrera. Du blir ombedd att logga in på ditt Azure-konto. Slutför inloggningen så att skriptet kan komma åt de privata DNS-zonresurserna i prenumerationen.

![Logga in till Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Markera de DNS-zoner som du vill migrera

Skriptet med få listan över alla privata DNS-zoner i prenumerationen och uppmanar dig att bekräfta vilka du vill migrera. Ange "A" för att migrera alla privata DNS-zoner. När du har kör det här steget skapar skriptet nya privata DNS-zoner med hjälp av ny resursmodell och kopierar data till den nya DSN-zonen. Det här steget kommer inte att ändra dina befintliga privata DNS-zoner i alla fall.

![Välj DNS-zoner](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Växla DNS-upplösning till de nya DNS-zonerna

När zonerna och posterna har kopierats till den nya resursmodellen uppmanas du att byta DNS-matchning till nya DNS-zoner. Det här steget tar bort kopplingen mellan äldre privata DNS-zoner och dina virtuella nätverk. När den äldre zonen inte är länkad från de virtuella nätverken tar de nya DNS-zoner som skapats i ovanstående steg automatiskt över DNS-lösningen för dessa virtuella nätverk.

Välj "A" för att växla DNS-upplösningen för alla virtuella nätverk.

![Byta namnmatchning](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Verifiera DNS-upplösningen

Innan du fortsätter vidare kontrollerar du att DNS-lösningen på DNS-zonerna fungerar som förväntat. Du kan logga in på dina virtuella azure-datorer och utfärda nslookup-fråga mot de migrerade zonerna för att verifiera att DNS-lösningen fungerar.

![Verifiera namnmatchning](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Om du upptäcker att DNS-frågor inte lösers väntar du några minuter och försöker igen med frågorna. Om DNS-frågor fungerar som förväntat anger du "Y" när skriptet uppmanar dig att ta bort det virtuella nätverket från den privata DNS-zonen.

![Bekräfta namnmatchning](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Om DNS-lösningen mot de migrerade zonerna inte fungerar som förväntat anger du 'N' i ovanstående steg och skriptet växlar DNS-upplösningen tillbaka till äldre zoner. Skapa en supportbiljett så kan vi hjälpa dig med migrering av dina DNS-zoner.

## <a name="cleanup"></a>Rensa

Det här steget tar bort de äldre DNS-zonerna och bör köras först när du har verifierat att DNS-lösningen fungerar som förväntat. Du uppmanas att ta bort varje privat DNS-zon. Ange "Y" vid varje uppmaning efter att ha verifierat att DNS-upplösningen för de zonerna fungerar som den ska.

![Rensa](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Uppdatera din automatisering

Om du använder automatisering, inklusive mallar, PowerShell-skript eller anpassad kod som utvecklats med SDK, måste du uppdatera automatiseringen så att den nya resursmodellen används för de privata DNS-zonerna. Nedan finns länkar till nya privata DNS CLI / PS / SDK dokumentation.
* [AZURE DNS-REST-API FÖR privata zoner](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Azure DNS privata zoner CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Azure DNS privata zoner PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Azure DNS privata zoner SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Behöver du ytterligare hjälp

Skapa en supportbiljett om du behöver ytterligare hjälp med migreringsprocessen eller på grund av någon anledning att ovanstående steg inte fungerar för dig. Inkludera utskriftsfilen som genereras av PowerShell-skriptet med din supportbiljett.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata zoner](./private-dns-scenarios.md) som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteenden som du kan förvänta dig för vissa typer av åtgärder, finns i [Vanliga frågor och svar om privata DNS.](./dns-faq-private.md)

* Lär dig mer om DNS-zoner och poster genom att besöka [DNS-zoner och poster översikt](dns-zones-records.md).

* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
