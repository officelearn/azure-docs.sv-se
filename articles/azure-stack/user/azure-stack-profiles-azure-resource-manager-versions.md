---
title: Resource provider API-versioner som stöds av profiler i Azure Stack | Microsoft Docs
description: Läs mer om Azure Resource Manager-version som stöds av profiler i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 2769b78632e1a7f776359f2a4d768154c224aab5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264622"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Resource provider API-versioner som stöds av profiler i Azure Stack

Du hittar resursprovidern och versionsnummer för varje API-profil som används av Azure Stack i den här artikeln. Tabellerna i den här artikeln innehåller de versioner som stöds för varje resursprovider och API-versioner av profilerna. Varje resursprovider innehåller en uppsättning resurstyper och specifika versionsnummer.

API-profilen använder tre namngivningskonventioner:

 - **latest**
 - **yyyy-mm-dd-hybrid**
 - **yyyy-mm-dd-profile**

En förklaring av API-profiler och version frisläppningstakt för Azure Stack finns i [hantera API-versionsprofiler i Azure Stack](azure-stack-version-profiles.md).

> [!Note]  
> Den **senaste** API-profilen innehåller den senaste versionen av API för resursprovider och visas inte i den här artikeln.

## <a name="overview-of-2018--03-01-hybrid"></a>Översikt över 2018-03-01-hybrid

| Resursprovider | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN-Gateway kommer att 2017-03-01 |
| Microsoft.Storage (Data Plane) | 2017-04-17 |
| Microsoft.Storage (kontrollplanet) | 2016-01-01 |
| Microsoft. Webb | 2016-08-01<br>vilket är senast (från och med nu) i Azure |
| Microsoft.KeyVault | 2016-10-01 (ändra inte) |
| Microsoft.Resources (Azure Resource Manager själva) | 2016-02-01 |
| Microsoft.Authorization (åtgärder) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Princip | 2016-10-01 |
| Resurser | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Prenumerationer | 2016-10-01 |

Läs mer en lista över versioner för varje resurstyp för leverantörer i API-profilen, [information för 2018-03-01-hybrid](#details-for-the-2018-03-01-hybrid) profil.

## <a name="overview-of-2018-03-01-hybrid"></a>Översikt över 2018-03-01-hybrid

| Resursprovider | Api-version |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (Data Plane) | 2015-04-05  |
| Microsoft.Storage (kontrollplanet) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Ändra inte) |
| Microsoft.Resources<br>(Azure Resource Manager själva) | 2016-02-01 |
| Microsoft.Authorization<Br>(åtgärder) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Princip | 2015-10-01-preview |
| Resurser | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Prenumerationer | 2016-06-1 |

En lista med versioner av varje resurstyp för leverantörer i API-profilen finns i nästa avsnitt.

## <a name="details-for-the-2018-03-01-hybrid"></a>Information om 2018-03-01-hybrid

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Du kan använda rollbaserad åtkomstkontroll för att hantera de åtgärder som användare i din organisation kan utföra på resurser. Den här uppsättningen åtgärder kan du definiera roller, tilldela roller till användare eller grupper och få information om behörigheter. Mer information finns i [auktorisering](/rest/api/authorization/).

| Resurstyper | API-versioner |
|---------------------|--------------------|
| Lås | 2017-04-01 |
| Åtgärder | 2015-07-01 |
| Behörigheter | 2015-07-01 |
| Principtilldelningar | 2016-12-01 (2017-06-01-preview) |
| Principdefinitioner | 2016-12-01 |
| Åtgärder för resursprovider | 2015-07-01-preview |
| Rolltilldelningar | 2015-07-01 |
| Rolldefinitioner | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Resurstyp | API-version |
|----------------------------------|----------------------|
| Delegerad Providerprenumerationer | 2015-06-01 - förhandsversion |
| Delegerad Användningsmängder | 2015-06-01 - förhandsversion |
| Uppskattningen Resource utgifter | 2015-06-01 – förhandsgranskning |
| Åtgärder | 2015-06-01 - förhandsversion |
| Prenumerant Användningsmängder | 2015-06-01 - förhandsversion |
| Användningsmängder | 2015-06-01 - förhandsversion |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure Compute-API: erna får du programmeringsbaserad åtkomst till virtuella datorer och deras stödresurser. Mer information finns i [Azure Compute](/rest/api/compute/).

| Resurstyp | API-version |
|---------------------------------------------------------------|-------------|
| Tillgänglighetsuppsättningar | 2016-03-30 |
| Platser | 2016-03-30 |
| Platser/operations | 2016-03-30 |
| Platser/utgivare | 2016-03-30 |
| Platser/användningar | 2016-03-30 |
| Locations/vmSizes | 2016-03-30 |
| Åtgärder | 2016-03-30 |
| Virtuella datorer | 2016-03-30 |
| Virtuella datorer /-tillägg | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/tillägg | 2016-03-30 |
| Virtual Machine Scale Sets/nätverksgränssnitt | 2016-03-30 |
| VM Scale Sets/virtuella datorer | 2016-03-30 |
| VM Scale Sets/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Resurstyp | API-version |
|------------------|-------------|
| Hantering | 2015-04-01 |
| Hanterat innehåll | 2015-04-01 |
| Hanterad extrahering | 2015-04-01 |
| Galleriobjekt | 2015-04-01 |
| Åtgärder | 2015-04-01 |
| Portalen | 2015-04-01 |
| Search | 2015-04-01 |
| Föreslå | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Resurstyper | API-versioner |
|--------------------|--------------------|
| Åtgärder | 2015-04-01 |
| Typer av händelse | 2015-04-01 |
| Händelsekategorier | 2015-04-01 |
| Måttdefinitioner | 2018-01-01 |
| Mått | 2018-01-01 |
| Diagnostikinställningar | 2017-05-01-preview |
| Kategorier för diagnostikinställningar | 2017-05-01-preview |


### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Hantera dina valv samt vilka nycklar, hemligheter och certifikat i nyckelvalven. Mer information finns i den [Azure Key Vault REST API-referens](/rest/api/keyvault/).

| Resurstyper | API-versioner |
|-------------------------|--------------|
| Åtgärder | 2016-10-01 |
| Valv | 2016-10-01 |
| Valv / åtkomstprinciper | 2016-10-01 |
| Valv/hemligheter | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Samtalsresultat åtgärder är en representation av listan över tillgängliga nätverk molnet åtgärder. Mer information finns i [åtgärd REST API](/rest/api/operation/).

| Resurstyper | API-versioner |
|---------------------------|--------------|
| Anslutningar | 2015-06-15 |
| DNS-zoner | 2016-04-01 |
| Lastbalanserare | 2015-06-15 |
| Lokal nätverksgateway | 2015-06-15 |
| Platser | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Platser/operations | 2016-04-01 |
| Platser/användningar | 2016-04-01 |
| Nätverksgränssnitt | 2015-06-15 |
| Nätverkssäkerhetsgrupper | 2015-06-15 |
| Åtgärder | 2015-06-15 |
| Offentlig IP-adress | 2015-06-15 |
| Routningstabeller | 2015-06-15 |
| Virtuell nätverksgateway | 2015-06-15 |
| Virtuella nätverk | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager kan du distribuera och hantera infrastrukturen för dina Azure-lösningar. Du organiserar relaterade resurser i resursgrupper och distribuerar resurserna med JSON-mallar. En introduktion till distribution och hantering av resurser med Resource Manager finns i den [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

| Resurstyper | API-versioner |
|-----------------------------------------|-------------------|
| Programregistreringar | 2015-01-01 |
| Kontrollera resursnamnet | 2016-09-01 |
| Delegerade providrar | 2015-01-01 |
| Delegerade providrar/erbjudanden | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Distributioner | 2016-09-01 |
| Distributioner/operations | 2016-09-01 |
| Metadata för tillägg | 2015-01-01 |
| Länkar | 2016-09-01 |
| Platser | 2015-01-01 |
| Erbjudanden | 2015-01-01 |
| Åtgärder | 2015-01-01 |
| Leverantörer | 2017-08-01 |
| Resursgrupper | 2016-09-01 |
| Resurser | 2016-09-01 |
| Prenumerationer | 2016-09-01 |
| Prenumerationer/plats | 2016-09-01 |
| Prenumerationer/Åtgärdsresultat | 2016-09-01 |
| Prenumerationer/providers | 2017-08-01 |
| Prenumerationerna/resursgrupperna | 2016-09-01 |
| Prenumerationer/resourceGroups/resurser | 2016-09-01 |
| Prenumerationer/resurser | 2016-09-01 |
| Prenumerationer/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Klienter | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Storage Resource Provider (SRP) kan du hantera dina lagringskonton och nycklar programmässigt. Mer information finns i den [Azure Storage Resource Provider REST API-referens](/rest/api/storagerp/).

| Resurstyper | API-versioner |
|-------------------------|--------------|
| Kontrollera namntillgänglighet | 2016-01-01 |
| Platser | 2016-01-01 |
| Platser/kvoter | 2016-01-01 |
| Åtgärder | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Användningar | 2016-01-01 |

## <a name="details-for-the-2018-03-01-hybrid"></a>Information om 2018-03-01-hybrid

### <a name="microsoft-authorization"></a>Microsoft-auktorisering

| Resurstyper | API-versioner |
|---------------------|---------------------------------|
| Lås | 2017-04-01 |
| Åtgärder | 2015-07-01 |
| Behörigheter | 2015-07-01 |
| Principtilldelningar | 2016-12-01 (2017-06-01-preview) |
| Principdefinitioner | 2016-12-01 |
| Åtgärder för resursprovider | 2015-07-01-preview |
| Rolltilldelningar | 2015-07-01 |
| Rolldefinitioner | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Resurstyp | API-version |
|---------------------------------------------------------------|-------------|
| Tillgänglighetsuppsättningar | 2016-03-30 |
| Platser | 2016-03-30 |
| Platser/operations | 2016-03-30 |
| Platser/utgivare | 2016-03-30 |
| Platser/användningar | 2016-03-30 |
| Locations/vmSizes | 2016-03-30 |
| Åtgärder | 2016-03-30 |
| Virtuella datorer | 2016-03-30 |
| Virtuella datorer /-tillägg | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/tillägg | 2016-03-30 |
| Virtual Machine Scale Sets/nätverksgränssnitt | 2016-03-30 |
| VM Scale Sets/virtuella datorer | 2016-03-30 |
| VM Scale Sets/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Resurstyper | API-versioner |
|---------------------------|--------------|
| Anslutningar | 2015-06-15 |
| DNS-zoner | 2016-04-01 |
| Lastbalanserare | 2015-06-15 |
| Lokal nätverksgateway | 2015-06-15 |
| Platser | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Platser/operations | 2016-04-01 |
| Platser/användningar | 2016-04-01 |
| Nätverksgränssnitt | 2015-06-15 |
| Nätverkssäkerhetsgrupper | 2015-06-15 |
| Åtgärder | 2015-06-15 |
| Offentlig IP-adress | 2015-06-15 |
| Routningstabeller | 2015-06-15 |
| Virtuell nätverksgateway | 2015-06-15 |
| Virtuella nätverk | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Resurstyper | API-versioner |
|-----------------------------------------|--------------|
| Programregistreringar | 2015-01-01 |
| Kontrollera resursnamnet | 2016-09-01 |
| Delegerade providrar | 2015-01-01 |
| Delegerade providrar/erbjudanden | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Distributioner | 2016-09-01 |
| Distributioner/operations | 2016-09-01 |
| Metadata för tillägg | 2015-01-01 |
| Länkar | 2016-09-01 |
| Platser | 2015-01-01 |
| Erbjudanden | 2015-01-01 |
| Åtgärder | 2015-01-01 |
| Leverantörer | 2017-08-01 |
| Resursgrupper | 2016-09-01 |
| Resurser | 2016-09-01 |
| Prenumerationer | 2016-09-01 |
| Prenumerationer/plats | 2016-09-01 |
| Prenumerationer/Åtgärdsresultat | 2016-09-01 |
| Prenumerationer/providers | 2017-08-01 |
| Prenumerationerna/resursgrupperna | 2016-09-01 |
| Prenumerationer/resourceGroups/resurser | 2016-09-01 |
| Prenumerationer/resurser | 2016-09-01 |
| Prenumerationer/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Klienter | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Resurstyper | API-versioner |
|-------------------------|--------------|
| Kontrollera namntillgänglighet | 2016-01-01 |
| Platser | 2016-01-01 |
| Platser/kvoter | 2016-01-01 |
| Åtgärder | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Användningar | 2016-01-01 |

## <a name="next-steps"></a>Nästa steg

* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)  
