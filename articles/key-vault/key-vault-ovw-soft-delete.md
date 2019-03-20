---
ms.assetid: ''
title: Azure Key Vault mjuk borttagning | Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 09/25/2017
ms.openlocfilehash: 526b0b135c8d5c1741ddf5f3fe6fb32f259a3e2c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092998"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Översikt över mjuk borttagning i Azure Key Vault

Key Vault mjuk borttagning med funktionen kan återställning av borttagna valv och valv objekt, kallas mjuk borttagning. Mer specifikt bemöter vi följande scenarier:

- Stöd för återställa borttagningen av ett nyckelvalv
- Stöd för återställa borttagningen av nyckelvalvobjekt (ex.) nycklar, hemligheter, certifikat)

## <a name="supporting-interfaces"></a>Stöd för gränssnitt

Funktionen för mjuk borttagning är tillgängliga via REST, .NET / C#, PowerShell och CLI-gränssnitt.

Allmän information finns i referenserna för dessa mer information [Key Vault-referens](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Scenarier

Azure Key Vaults är spårade resurser som hanteras av Azure Resource Manager. Azure Resource Manager anger också ett väldefinierade beteende för borttagning, vilket kräver att en lyckad borttagningsåtgärd måste resultera i den här resursen inte är tillgänglig längre. Funktionen för mjuk borttagning adresser återställning av borttagna objekt om borttagningen har oavsiktligt eller avsiktligt.

1. Vanliga scenariot kan en användare ha oavsiktligt bort ett nyckelvalv eller ett nyckelvalv-objekt. Om nyckeln som vault eller key vault-objekt skulle gå att återställa under ett förinställt, du kan ångra borttagningen och återställa sina data.

2. I ett annat scenario kan en obehörig användare försöka ta bort ett nyckelvalv eller ett nyckelvalv-objekt, till exempel en nyckel i ett valv kan orsaka ett verksamhetsavbrott. Att separera borttagningen av key vault eller key vault-objektet från den faktiska borttagningen av underliggande data kan användas som en säkerhetsåtgärd av, till exempel att begränsa behörigheter för borttagning av data till en annan betrodd roll. Den här metoden kräver effektivt kvorum för en åtgärd som annars kan leda till en omedelbar dataförlust.

### <a name="soft-delete-behavior"></a>Beteende för mjuk borttagning

Med den här funktionen är åtgärden ta bort på ett nyckelvalv eller nyckelvalv objekt en mjuk borttagning effektivt innehåller resurserna under en viss period (90 dagar), samtidigt som det utseendet att objektet tas bort. Tjänsten ytterligare är en mekanism för att återställa det borttagna objektet, i stort sett ångra borttagningen. 

Mjuk borttagning är ett valfritt beteende för Key Vault och är **inte aktiverad som standard** i den här versionen. 

### <a name="purge-protection--flag"></a>Rensa skydd flagga
Rensa skydd (**– aktivera rensningsskydd** i Azure CLI) flaggan är inaktiverat som standard. När den här flaggan är aktiverad, ett valv eller ett objekt i tillståndet deleted går inte att tömma tills har kvarhållningsperiod på 90 dagar passerat. Sådana valv eller ett objekt kan fortfarande återställas. Den här flaggan ger extra trygghet för kunder att ett valv eller ett objekt kan aldrig tas bort permanent tills kvarhållningsperioden har passerat. Du kan aktivera flaggan Rensa protection endast om flaggan mjuk borttagning är på, eller på Skapa valv du slår på både mjuk borttagning och rensa skydd.

> [!NOTE]
>    Krav för aktivering av Rensa skydd är måste du ha mjuk borttagning aktiveras.
> Kommandot för att göra det i Azure CLI-2 är

```
az keyvault create --name "VaultName" --resource-group "ResourceGroupName" --location westus --enable-soft-delete true --enable-purge-protection true
```

### <a name="permitted-purge"></a>Tillåtna Rensa

Permanent borttagning, rensning, key vault går via en POST-åtgärd på resursen för proxy och kräver särskilda behörigheter. I allmänhet kan Prenumerationens ägare ta bort ett nyckelvalv. POST-åtgärd utlöser omedelbar och oåterkalleligt borttagningen av det valvet. 

Ett undantag till detta är
- Om Azure-prenumerationen har markerats som *permanent*. I det här fallet bara tjänsten kan sedan att utföra den faktiska borttagningen och sker detta som en schemalagd process. 
- När – Aktivera rensningsskydd flaggan är aktiverad på själva valvet. I så fall väntar Key Vault i 90 dagar från när det ursprungliga hemliga objektet har markerats för borttagning att permanent ta bort objektet.

### <a name="key-vault-recovery"></a>Nyckelvalv-återställning

När du tar bort ett nyckelvalv, skapar tjänsten en proxy-resurs under prenumerationen, lägga till tillräckligt med metadata för återställning. Proxy-resurs är ett lagrade objekt tillgängliga på samma plats som har tagits bort nyckelvalvet. 

### <a name="key-vault-object-recovery"></a>Objektåterställning för nyckelvalvet

När du tar bort ett nyckelvalv-objekt, till exempel en nyckel placerar tjänsten objektet i ett borttaget tillstånd, vilket gör det otillgängligt för hämtning av åtgärder. I det här tillståndet kan objektet nyckelvalvet bara visas, återställda eller kernelpaketet/har tagits bort permanent. 

På samma gång schemalägger Key Vault borttagningen av underliggande data för den borttagna nyckelvalvet eller det nyckelvalv objekt för körning när du har ett förbestämt Kvarhållningsintervall. DNS-posten för valvet sparas även under Kvarhållningsintervall.

### <a name="soft-delete-retention-period"></a>Kvarhållningsperiod för mjuk borttagning

Ej permanent borttagna resurser behålls under en angiven tidsperiod, 90 dagar. Under det mjuk borttagning Kvarhållningsintervall, följande gäller:

- Du kan visa alla viktiga valv och nyckelvalvobjekt tillståndet mjuk borttagning för din prenumeration, samt komma åt information om borttagning och återställning om dem.
    - Endast användare med särskilda behörigheter kan visa borttagna valv. Vi rekommenderar att våra användare skapar en anpassad roll med dessa särskilda behörigheter för hantering av bort valv.
- Att går inte skapa ett nyckelvalv med samma namn på samma plats; på motsvarande sätt kan ett nyckelvalv-objekt inte skapas i ett visst valv om nyckelvalvet innehåller ett objekt med samma namn och som är i ett borttaget tillstånd 
- Endast en specifikt privilegierad användare kan återställa en nyckelvalvet eller ett nyckelvalv objekt genom att utfärda ett kommando för återställning på motsvarande proxy-resurs.
    - Användaren, medlem i den anpassade rollen som har behörighet att skapa ett nyckelvalv under resursgrupp kan återställa valvet.
- Endast en specifikt privilegierad användare kan tvång ta bort ett nyckelvalv eller nyckelvalv objekt genom att utfärda ett borttagningskommando på motsvarande proxy-resurs.

Om inte en nyckelvalvet eller ett nyckelvalv objekt återställs, utför tjänsten i slutet av Kvarhållningsintervall som en rensning av ej permanent borttagna nyckelvalvet eller nyckelvalv objekt och dess innehåll. Ta bort resursen kan inte planeras.

### <a name="billing-implications"></a>Fakturering effekter

I allmänhet när ett objekt (key vault eller en nyckel eller en hemlighet) är i tillståndet deleted, finns bara två åtgärder möjligt: ”rensa” och ”återställa”. Alla andra åtgärder misslyckas. Därför även om objektet finns inga åtgärder kan utföras och därför ingen användning sker, så ingen faktura. Men det följande undantag:

- ”Rensa” och ”återställa” åtgärder räknas in i normala nyckelvalvsåtgärder och kommer att debiteras.
- Om objektet är en HSM-nyckel, gäller HSM-skyddad nyckel kostnad per nyckelversionen per månad kan kostnader om en nyckelversionen har använts under de senaste 30 dagarna. Efter det, eftersom objektet är i tillståndet deleted inga åtgärder kan utföras mot det, så ingen avgift gäller.

## <a name="next-steps"></a>Nästa steg

Följande två guider erbjuder primära Användningsscenarier för att använda mjuk borttagning.

- [Så här använder du Key Vault mjuk borttagning med PowerShell](key-vault-soft-delete-powershell.md) 
- [Så här använder du Key Vault mjuk borttagning med CLI](key-vault-soft-delete-cli.md)

