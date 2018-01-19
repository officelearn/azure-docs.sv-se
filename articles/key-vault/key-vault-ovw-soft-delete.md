---
ms.assetid: 
title: Azure Key Vault mjuk borttagning | Microsoft Docs
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 09/25/2017
ms.openlocfilehash: 01357e4fdb9b6f27e9baf5f5c8e4c7d6b582ad35
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-soft-delete-overview"></a>Översikt för mjuk borttagning av Azure Key Vault

Key Vault mjuk borttagning funktionen kan återställning av borttagna valv och valvet objekt, kallas även mjuk borttagning. Mer specifikt adressera vi följande scenarier:

- Stöd för återställningsbara borttagningen av ett nyckelvalv
- Stöd för återställningsbara borttagning av nyckelvalv objekt (t.ex. nycklar, hemligheter, certifikat)

## <a name="supporting-interfaces"></a>Stöder gränssnitt

Funktionen för mjuk borttagning är är tillgängliga via RESTEN .NET / C#, PowerShell och CLI-gränssnitt.

Allmän information finns i referenserna för dessa mer information [valvet nyckelreferens](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Scenarier

Azure Key valv är spårade resurser som hanteras av Azure Resource Manager. Azure Resource Manager anger också en väldefinierad beteendet för borttagning, vilket kräver att en lyckad borttagningsåtgärd måste resultera i den här resursen inte är tillgänglig längre. Funktionen Mjuk borttagning adresser återställning av borttagna objekt om borttagningen har oavsiktligt eller avsiktligt.

1. I vanliga fall kan en användare ha oavsiktligt bort ett nyckelvalv eller ett nyckelvalv-objekt. Om nyckeln som valvet eller key vault objekt skulle återställas för en förutbestämd tid, användaren kan ångra borttagningen och återställa data.

2. I ett annat scenario kan en obehörig användare försöka ta bort ett nyckelvalv eller ett nyckelvalv objekt, t.ex en nyckel i ett valv kan orsaka ett avbrott i verksamheten. Avgränsa borttagningen av nyckelvalv eller nyckelvalv objekt från den faktiska borttagningen av underliggande data kan användas som en säkerhetsåtgärd genom att, exempelvis att begränsa behörigheter för Databorttagning till en annan betrodd roll. Den här metoden kräver i praktiken kvorum för en åtgärd som annars kan leda till en omedelbar dataförlust.

### <a name="soft-delete-behavior"></a>Beteende för mjuk borttagning

Med den här funktionen är DELETE-åtgärden på ett nyckelvalv eller nyckelvalv objekt en mjuk borttagning effektivt hålla resurser under en viss period, samtidigt som ger utseende att objektet tas bort. Tjänsten ytterligare tillhandahåller en mekanism för att återställa den borttagna objekt i stort sett ångra borttagningen. 

Mjuk borttagning är ett valfritt Key Vault-beteende och **inte aktiverad som standard** i den här versionen. 

### <a name="key-vault-recovery"></a>Nyckelvalv återställning

När du tar bort ett nyckelvalv skapar tjänsten en proxy-resurs under prenumerationen, lägga till tillräckligt metadata för återställning. Proxy-resurs är ett lagrade objekt finns på samma plats som borttagna nyckelvalvet. 

### <a name="key-vault-object-recovery"></a>Nyckelvalv objektåterställning

När du tar bort ett nyckelvalv objekt, t.ex en nyckel placerar tjänsten objektet i ett borttaget tillstånd, vilket gör den tillgänglig för alla åtgärder för hämtning. I det här tillståndet kan nyckelvalv-objekt bara visas, återställda eller tvång/tas bort permanent. 

På samma gång schemalägger Key Vault borttagningen av underliggande data som motsvarar det borttagna nyckelvalv eller nyckelvalv objektet för körning när ett förutbestämt Kvarhållningsintervall. DNS-post som motsvarar valvet bevaras också under Kvarhållningsintervall.

### <a name="soft-delete-retention-period"></a>Kvarhållningsperiod för mjuk borttagning

Ej permanent borttagna resurser bevaras under en angiven tidsperiod, 90 dagar. Under den mjuk borttagning Kvarhållningsintervall, gäller följande krav:

- Du kan visa alla nyckelvalv och nyckelvalv objekt i tillståndet mjuk borttagning för din prenumeration, samt komma åt tas bort och återställa information om dem.
    - Endast användare med särskilda behörigheter kan visa en lista över borttagna valv. Vi rekommenderar att våra användare skapar en anpassad roll med dessa särskilda behörigheter för hantering av bort valv.
- Nyckelvalv med samma namn kan inte skapas på samma plats; på motsvarande sätt kan ett nyckelvalv-objekt inte skapas i ett givet valv om att nyckelvalvet innehåller ett objekt med samma namn och som är i ett borttaget tillstånd 
- Endast en specifikt Privilegierade användare kan återställa en nyckelvalv eller ett nyckelvalv objekt genom att utfärda kommandot Återställ på motsvarande proxy-resurs.
    - Användaren medlem i den anpassade rollen som har behörighet för att skapa nyckelvalvet under resursgruppen kan återställa valvet.
- Bara en specifikt Privilegierade användare kan framtvinga ta bort ett nyckelvalv eller nyckelvalv objekt genom att utfärda ett borttagningskommando på motsvarande proxy-resurs.

Om ett nyckelvalv eller nyckelvalv objekt återställs utför tjänsten en rensning av ej permanent borttagna nyckelvalvet eller nyckelvalv objekt och dess innehåll i slutet av Kvarhållningsintervall. Ta bort resursen kan inte planeras.

### <a name="permitted-purge"></a>Tillåtna Rensa

Permanent ta bort, rensa, ett nyckelvalv går via en POST-åtgärden på resursen proxy och kräver särskilda behörigheter. I allmänhet kommer endast prenumerationsägaren att kunna rensa en key vault. POST-åtgärden utlöser omedelbart och oåterkalleligt borttagningen av det valvet. 

Ett undantag till detta är fallet när Azure-prenumerationen har markerats som *permanent*. I det här fallet endast tjänsten kan sedan att utföra den faktiska borttagningen och sker detta som en schemalagd process. 

## <a name="next-steps"></a>Nästa steg

Följande två guider erbjuder primära Användningsscenarier för mjuk borttagning.

- [Så här använder du Key Vault mjuk borttagning med PowerShell](key-vault-soft-delete-powershell.md) 
- [Så här använder du Key Vault mjuk borttagning med CLI](key-vault-soft-delete-cli.md)

