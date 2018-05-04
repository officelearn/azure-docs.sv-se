---
title: Resursen tjänstproviderns API-versioner som stöds av profiler i Azure-stacken | Microsoft Docs
description: Läs mer om Azure Resource Manager-version som stöds av profiler i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 54de948597a5eddfcc808371c61e36d45089abc4
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Resursen tjänstproviderns API-versioner som stöds av profiler i Azure-stacken

Du kan hitta resursprovidern och versionsnummer för varje API-profil som används av Azure-stacken i den här artikeln. Tabellerna i den här artikeln innehåller de versioner som stöds för varje resursprovider och API-versioner av profilerna. Varje resursprovider innehåller en uppsättning resurstyper och specifika versionsnummer.

API-profilen använder tre namngivningsregler:
 - senaste
 - åååå-mm-dd-hybrid
 - åååå-mm-dd-profil

En förklaring av API-profiler och version versionen takt för Azure-Stack finns [hantera API-version-profiler i Azure-stacken](azure-stack-version-profiles.md).

> [!note]  
> Den **senaste** API profil innehåller den senaste versionen av resursen tjänstproviderns API-version och ingår inte i den här artikeln.

## <a name="overview-of-2018--03-01-hybrid"></a>Översikt över 2018-03-01-hybrid

| Resursprovider | API-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN-Gateway kommer att 2017-03-01 |
| Microsoft.Storage (dataplan) | 2017-04-17 |
| Microsoft.Storage (kontrollplan) | 2016-01-01 |
| Microsoft. Webb | 2016-08-01<br>vilket är senast (från och med nu) i Azure |
| Microsoft.KeyVault | 2016-10-01 (ändra inte) |
| Microsoft.Resources (Azure Resource Manager själva) | 2016-02-01 |
| Microsoft.Authorization (princip operations) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Princip | 2016-10-01 |
| Resurser | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Prenumerationer | 2016-10-01 |

Läs mer en lista över versioner för varje typ av providrar på api-profil, [information för 2018-03-01-hybrid](#details-for-the-2018-03-01-hybrid) profil.

## <a name="overview-of-2017-03-09-profile"></a>Översikt över 2017-03-09-profil

| Resursprovider | API-version |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (dataplan) | 2015-04-05  |
| Microsoft.Storage (kontrollplan) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Ändra inte) |
| Microsoft.Resources<br>(Azure Resource Manager själva) | 2016-02-01 |
| Microsoft.Authorization<Br>(princip operations) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Princip | 2015-10-01-preview |
| Resurser | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Prenumerationer | 2016-06-1 |

Läs mer en lista över versioner för varje typ av providrar på api-profil, [information för 2017-03-09-profilen](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>Information om 2018-03-01-hybrid

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Du kan använda rollbaserad åtkomstkontroll för att hantera de åtgärder användare i din organisation kan vidta på resurser. Denna uppsättning åtgärder kan du definiera roller, tilldela roller till användare eller grupper och få information om behörigheter. Mer information finns i [auktorisering](https://docs.microsoft.com/rest/api/authorization/).

| Resurstyper | API-versioner |
|---------------------|--------------------|
| Lås | 2017-04-01 |
| Åtgärder | 2015-07-01 |
| Behörigheter | 2015-07-01 |
| Principtilldelningar | 2016-12-01 (2017-06-01-preview) |
| Principdefinitioner | 2016-12-01 |
| Provideråtgärder | 2015-07-01-preview |
| Rolltilldelningar | 2015-07-01 |
| Rolldefinitioner | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Resurstyp | API-Version |
|----------------------------------|----------------------|
| Delegerad providern prenumerationer | 2015-06-01 - preview |
| Delegerad användning mängder | 2015-06-01 - preview |
| Ägna uppskattning resurs | 2015-06-01 – förhandsgranskning |
| Åtgärder | 2015-06-01 - preview |
| Prenumeranten användning mängder | 2015-06-01 - preview |
| Användningsmängder | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure Compute-API: erna ger programmatisk åtkomst till virtuella datorer och deras stödresurser. Mer information finns i [Azure Compute](https://docs.microsoft.com/rest/api/compute/).

| Resurstyp | API-Version |
|---------------------------------------------------------------|-------------|
| Tillgänglighetsuppsättningar | 2016-03-30 |
| Platser | 2016-03-30 |
| Platser/operationer | 2016-03-30 |
| Platser/utgivare | 2016-03-30 |
| Platser/användningsområden | 2016-03-30 |
| Platser/vmSizes | 2016-03-30 |
| Åtgärder | 2016-03-30 |
| Virtuella datorer | 2016-03-30 |
| Virtuella datorer-tillägg | 2016-03-30 |
| Skalningsuppsättningar för Virtual Machines | 2016-03-30 |
| Anger/tillägg för virtuella datorn | 2016-03-30 |
| Anger/nätverksgränssnitt för virtuella datorn | 2016-03-30 |
| Virtual Machine Scale uppsättningar för virtuella datorer | 2016-03-30 |
| Virtuella datorer skala uppsättningar/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Resurstyp | API-Version |
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
| Varningsregler | 2016-03-01 |
| Kategorier | 2017-03-01-preview |
| Typer av händelse | 2017-03-01-preview |
| Måttdefinitioner | 2016-03-01 |
| Åtgärder | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Hantera din nyckel valv samt de nycklar och hemligheter certifikat inom ditt nyckelvalv. Mer information finns i [Azure Key Vault REST API-referensen](https://docs.microsoft.com/rest/api/keyvault/).

| Resurstyper | API-versioner |
|-------------------------|--------------|
| Åtgärder | 2016-10-01 |
| Valv | 2016-10-01 |
| Valv / åtkomstprinciper | 2016-10-01 |
| Valv/hemligheter | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Operations anropet resultatet är en representation av tillgängliga molnet operations listan över nätverk. Mer information finns i [åtgärden REST API](https://docs.microsoft.com/rest/api/operation/).

| Resurstyper | API-versioner |
|---------------------------|--------------|
| Anslutningar | 2015-06-15 |
| DNS-zoner | 2016-04-01 |
| Belastningsutjämning | 2015-06-15 |
| Lokal nätverksgateway | 2015-06-15 |
| Platser | 2016-04-01 |
| Plats/operationResults | 2016-04-01 |
| Platser/operationer | 2016-04-01 |
| Platser/användningsområden | 2016-04-01 |
| Nätverksgränssnitt | 2015-06-15 |
| Nätverkssäkerhetsgrupper | 2015-06-15 |
| Åtgärder | 2015-06-15 |
| Offentlig IP-adress | 2015-06-15 |
| Routningstabeller | 2015-06-15 |
| Virtuell nätverksgateway | 2015-06-15 |
| Virtuella nätverk | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager kan du distribuera och hantera infrastrukturen för din Azure-lösningar. Ordnar relaterade resurser i resursgrupper och distribuera resurser med JSON-mallarna. En introduktion till att distribuera och hantera resurser med Resource Manager finns [översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Resurstyper | API-versioner |
|-----------------------------------------|-------------------|
| Program-registreringar | 2015-01-01 |
| Kontrollera resursnamnet | 2015-012016-09-01 |
| Delegerad Providers | 2015-01-01 |
| Delegerad Providers-erbjudanden | 2015-01-01 |
| EstimatePrice-DelegatedProviders/erbjudanden | 2015-01-01 |
| Distributioner | 2016-0209-01 |
| Distributioner/operationer | 2016-0209-01 |
| Tillägg Metadata | 2015-01-01 |
| Länkar | 2015-012016-09-01 |
| Platser | 2015-01-01 |
| Erbjudande | 2015-01-01 |
| Åtgärder | 2015-01-01 |
| Leverantörer | 2015-012017-08-01 |
| Resursgrupper | 2015-012016-09-01 |
| Resurser | 2015-012016-09-01 |
| Prenumerationer | 2015-012016-09-01 |
| Prenumerationer eller plats | 2015-012016-09-01 |
| Resultat för prenumerationer/åtgärd | 2015-012016-09-01 |
| Prenumerationer-providers | 2015-012017-08-01 |
| Prenumerationer/resursgrupper | 2015-012016-09-01 |
| Prenumerationer-resursgrupper-resurser | 2015-012016-09-01 |
| Prenumerationer/resurser | 2015-012016-09-01 |
| Prenumerationer/tagNames | 2016-0609-01 |
| TagValues-prenumerationer/tagNames | 2016-0609-01 |
| Klienter | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

Storage Resource Provider (SRP) kan du hantera ditt lagringskonto och nycklar genom programmering. Mer information finns i [Azure Storage Resource Provider REST API-referens](https://docs.microsoft.com/rest/api/storagerp/).

| Resurstyper | API-versioner |
|-------------------------|--------------|
| Kontrollera namntillgänglighet | 2016-01-01 |
| Platser | 2016-01-01 |
| Platser-kvoter | 2016-01-01 |
| Åtgärder | 2016-01-01 |
| storageAccounts | 2016-01-01 |
| Användningsområden | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>Information om 2017-03-09-profil

### <a name="microsoft-authorization"></a>Microsoft-auktorisering

| Resurstyper | API-versioner |
|---------------------|---------------------------------|
| Lås | 2017-04-01 |
| Åtgärder | 2015-07-01 |
| Behörigheter | 2015-07-01 |
| Principtilldelningar | 2016-12-01 (2017-06-01-preview) |
| Principdefinitioner | 2016-12-01 |
| Provideråtgärder | 2015-07-01-preview |
| Rolltilldelningar | 2015-07-01 |
| Rolldefinitioner | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Resurstyp | API-Version |
|---------------------------------------------------------------|-------------|
| Tillgänglighetsuppsättningar | 2016-03-30 |
| Platser | 2016-03-30 |
| Platser/operationer | 2016-03-30 |
| Platser/utgivare | 2016-03-30 |
| Platser/användningsområden | 2016-03-30 |
| Platser/vmSizes | 2016-03-30 |
| Åtgärder | 2016-03-30 |
| Virtuella datorer | 2016-03-30 |
| Virtuella datorer-tillägg | 2016-03-30 |
| Skalningsuppsättningar för Virtual Machines | 2016-03-30 |
| Anger/tillägg för virtuella datorn | 2016-03-30 |
| Anger/nätverksgränssnitt för virtuella datorn | 2016-03-30 |
| Virtual Machine Scale uppsättningar för virtuella datorer | 2016-03-30 |
| Virtuella datorer skala uppsättningar/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Resurstyper | API-versioner |
|---------------------------|--------------|
| Anslutningar | 2015-06-15 |
| DNS-zoner | 2016-04-01 |
| Belastningsutjämning | 2015-06-15 |
| Lokal nätverksgateway | 2015-06-15 |
| Platser | 2016-04-01 |
| Plats/operationResults | 2016-04-01 |
| Platser/operationer | 2016-04-01 |
| Platser/användningsområden | 2016-04-01 |
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
| Program-registreringar | 2015-01-01 |
| Kontrollera resursnamnet | 2016-09-01 |
| Delegerad Providers | 2015-01-01 |
| Delegerad Providers-erbjudanden | 2015-01-01 |
| EstimatePrice-DelegatedProviders/erbjudanden | 2015-01-01 |
| Distributioner | 2016-09-01 |
| Distributioner/operationer | 2016-09-01 |
| Tillägg Metadata | 2015-01-01 |
| Länkar | 2016-09-01 |
| Platser | 2015-01-01 |
| Erbjudande | 2015-01-01 |
| Åtgärder | 2015-01-01 |
| Leverantörer | 2017-08-01 |
| Resursgrupper | 2016-09-01 |
| Resurser | 2016-09-01 |
| Prenumerationer | 2016-09-01 |
| Prenumerationer eller plats | 2016-09-01 |
| Resultat för prenumerationer/åtgärd | 2016-09-01 |
| Prenumerationer-providers | 2017-08-01 |
| Prenumerationer/resursgrupper | 2016-09-01 |
| Prenumerationer-resursgrupper-resurser | 2016-09-01 |
| Prenumerationer/resurser | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| TagValues-prenumerationer/tagNames | 2016-09-01 |
| Klienter | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Resurstyper | API-versioner |
|-------------------------|--------------|
| Kontrollera namntillgänglighet | 2016-01-01 |
| Platser | 2016-01-01 |
| Platser-kvoter | 2016-01-01 |
| Åtgärder | 2016-01-01 |
| storageAccounts | 2016-01-01 |
| Användningsområden | 2016-01-01 |

## <a name="next-steps"></a>Nästa steg

* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)  
