---
title: Resursen tjänstproviderns API-versioner som stöds av profiler i Azure-stacken | Microsoft Docs
description: Läs mer om Azure Resource Manager-version som stöds av profiler i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2E21C8DE-D540-4C1C-A0EF-1B7125DB7A6E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 8cc5ce1ec113df7ce92c54022dbe1b6219c8c235
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Resursen tjänstproviderns API-versioner som stöds av profiler i Azure-stacken

En Azure-resursprovider finns resurser som du kan distribuera och hantera via Azure Resource Manager. Varje provider erbjuder åtgärder för att arbeta med resurser. Några vanliga resursproviders inkluderar Microsoft.Compute som ger virtuella datorer, Microsoft.Storage som ger kontot lagringsresurser och Microsoft.Web som tillhandahåller resurser relaterade till webbprogram. Mer information finns i [Resursproviders och typer](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

I följande tabell för varje resursprovider anger versionen av API-versionen stöds för Azure-Stack när du använder profiler.

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

Azure Compute-API: erna ger programmatisk åtkomst till virtuella datorer och deras stödresurser. Mer information finns i [Azure Compute](https://docs.microsoft.com/en-us/rest/api/compute/).

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

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

Hantera din nyckel valv samt de nycklar och hemligheter certifikat inom ditt nyckelvalv. Mer information finns i [Azure Key Vault REST API-referensen](https://docs.microsoft.com/rest/api/keyvault/).

| Resurstyper | API-versioner |
|------------------|--------------------|
| Platser | 2017-02-01-preview |
| Platser-kvoter | 2017-02-01-preview |

### <a name="microsoftnetwork"></a>Microsoft.Network

Operations anropet resultatet är en representation av tillgängliga molnet operations listan över nätverk. Mer information finns i [åtgärden REST API](https://docs.microsoft.com/rest/api/operation/).

| Resurstyper | API-versioner |
|---------------------------|--------------|
| Anslutningar | 2015-06-15 |
| DNS-zoner | 2016-04-01 |
| Belastningsutjämning | 2015-06-15 |
| Lokal nätverksgateway | 2015-06-15 |
| Platser | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
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
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
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
| StorageAccounts | 2016-01-01 |
| Användningsområden | 2016-01-01 |

## <a name="next-steps"></a>Nästa steg

* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)  
