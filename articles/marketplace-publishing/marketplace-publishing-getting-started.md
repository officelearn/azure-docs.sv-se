---
title: Översikt över hur du skapar och distribuerar ett erbjudande i Marketplace | Microsoft Docs
description: Förstå de steg som krävs för att bli en godkänd Microsoft-utvecklare och skapa och distribuera en avbildning av virtuell dator, mall, datatjänst eller developer-tjänsten på Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: 2c8c97d8f5477e7640df87030ed6ef27c4c7b979
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310087"
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publicera och hantera ett erbjudande på Azure Marketplace

> [!NOTE]
> Den här dokumentationen är inte längre aktuellt och är inte korrekt. I stället gå till Azure Marketplace [Säljguide för](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide) vägledning för att publicera ett erbjudande på Azure Marketplace.

Den här artikeln är som hjälper utvecklare att skapa, distribuera och hantera sina lösningar i Azure Marketplace för andra Azure-kunder och partner att köpa och använda.

## <a name="marketplace-publishing"></a>Marketplace-publicering
Som en Azure utgivare som du kan distribuera och sälja innovativ lösning eller tjänster till andra utvecklare, ISV: er, och IT-proffs på Marketplace. Du kan nå kunder som vill snabbt utveckla sina molnbaserade program och mobila lösningar via Marketplace. Om din lösning riktar sig till användare i verksamheten, kanske du vill att den [AppSource](https://appsource.microsoft.com) marketplace.


## <a name="supported-types-of-solutions"></a>Typer av lösningar som stöds
Det första som du vill göra som en utgivare är att definiera vilken typ av lösning för ditt företag erbjuder. Marketplace har stöd för följande typer av erbjudanden:

|Typ av lösning|Virtuell dator|Lösningsmall|
|---|---|---|
|**Definition**|Förkonfigurerade avbildningar med ett fullständigt installerat operativsystem och en eller flera program. En avbildning av virtuell dator innehåller informationen som behövs för att skapa och distribuera virtuella datorer i Azure Virtual Machines-tjänsten.|En datastruktur som kan hänvisa till en eller flera olika Azure-tjänster, inklusive tjänster som publicerats av andra säljare. Azure-prenumeranter kan använda den för att distribuera ett eller flera erbjudanden i ett enda, samordnad sätt.|
|**Exempel**|Du har skapat och verifierats av en virtuell dator med en innovativ database-tjänst som en Azure utgivare. Andra Azure-prenumeranter vill anskaffa och distribuera den här virtuella datorn i sina cloud service-miljöer.|Som en Azure utgivare har du paketeras en uppsättning tjänster från i Azure som gör det snabbt att distribuera cloud services med Utjämning av nätverksbelastning, förbättrad säkerhet och hög tillgänglighet. Andra Azure-prenumeranter kan spara tid genom inköp lösningsmallen som uppfyller deras mål. De behöver inte manuellt hitta, köpa, distribuera och konfigurera samma eller liknande Azure-tjänsterna.|

> [!NOTE]
> Vissa steg som delas mellan de olika typerna av lösningar och andra är olika för respektive typ av lösning. Den här artikeln innehåller en kort översikt över de steg du måste utföra för alla typer av lösningen.

## <a name="publish-a-solution"></a>Publicera en lösning
![Nominera, registrera, publicera](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Nominera din lösning för förhandsgodkännande
Publicera en virtuell dator [lösning](https://createopportunity.azurewebsites.net) på Marketplace, slutför du Microsoft Azure Certified **lösning Nomineringsformuläret**.

>[!NOTE]
> Om du arbetar med en Partner kontoansvarig eller en DX Partner Manager kan du be dem att nominera din lösning för Azure Certified-programmet. Du kan även gå till den [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) webbsidan och Fyll i formuläret för programmet. Ange e-postmeddelande med din Partner kontoansvarige eller DX Partner Manager i den **Microsoft Sponsor Kontakta** box.

Om du uppfyller kriterierna i den [deltagandepolicyer för Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=526833) och din ansökan har godkänts kan vi börja arbeta med dig att publicera din lösning på Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Registrera ditt konto som ett Microsoft-försäljning
Registrera ditt Microsoft-konto som en [Microsoft Developer-konto](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publicera din lösning
Om du vill publicera en lösning på Marketplace, Följ dessa steg:
1. Sökassistent kraven.

    a. Uppfyller den [sökassistent krav](marketplace-publishing-pre-requisites.md).

    b. Uppfyller den [VM tekniska krav](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Uppfyller den [teknisk lösning mallkraven](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Skapa ditt erbjudande.

    a. Skapa en [VM](marketplace-publishing-vm-image-creation.md) erbjuder.

    b. Skapa en [lösningsmallen](marketplace-publishing-solution-template-creation.md) erbjuder.

3. Skapa ditt erbjudande [marknadsföring innehåll](marketplace-publishing-push-to-staging.md).

4. Testa ditt erbjudande i mellanlagringen.

    a. Testa ditt erbjudande för virtuell dator i [mellanlagring](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testa din lösning mall-erbjudandet i [mellanlagring](marketplace-publishing-solution-template-test-in-staging.md).

5. Distribuera ditt erbjudande till den [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Skapa och hantera en avbildning av virtuell dator
Skapa och hantera en VM-avbildning med hjälp av dessa resurser:
* Skapa en virtuell datoravbildning [lokala](marketplace-publishing-vm-image-creation-on-premise.md).
* Skapa en virtuell dator som kör [Windows i Azure-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Skapa en virtuell dator som kör [Linux i Azure-portalen](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Felsöka vanliga problem som uppstod under [skapa en virtuell Hårddisk](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Hantera din lösning
Hantera din lösning med hjälp från följande resurser:
* [Läs guiden släppts för virtuell dator-erbjudanden](marketplace-publishing-vm-image-post-publishing.md)
* [Uppdatera annan information om ett erbjudande eller en SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Uppdatera de tekniska detaljerna för ett erbjudande eller en SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Lägg till en ny SKU under en listade erbjudande](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Ändra datadiskar för en listade SKU](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Ta bort en listade erbjudandet från Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Ta bort en listade SKU från Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Ta bort den aktuella versionen av en listade SKU från Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Återställ lista priset till produktionsvärden](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Återställ faktureringsmodell till produktionsvärden](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Återställa inställningar för en listade SKU för produktion-värden](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Ytterligare resurser
[Konfigurera Azure PowerShell](marketplace-publishing-powershell-setup.md)
