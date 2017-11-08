---
title: "Översikt över hur du skapar och distribuerar ett erbjudande på Marketplace | Microsoft Docs"
description: "Förstå de steg som krävs för att bli en godkända Microsoft developer och skapa och distribuera en avbildning av virtuell dator, mall, datatjänst eller utvecklare service i Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: fa00f176ff56c13033907e134fc236c291308028
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publicera och hantera ett erbjudande på Azure Marketplace
Den här artikeln för att hjälpa utvecklare skapa, distribuera och hantera sina lösningar som anges i Azure Marketplace för andra Azure-kunder och partner att köpa och använda.

## <a name="marketplace-publishing"></a>Marketplace-publicering
Som en Azure utgivare som du kan distribuera och sälja innovativa lösning eller tjänster till andra utvecklare ISV: er, och IT-proffs på Marketplace. Du kan nå kunder som vill snabbt utveckla sina molnbaserade program och mobila lösningar på marknadsplatsen. Om din lösning riktar sig till användare i verksamheten, kanske du vill överväga de [AppSource](http://appsource.microsoft.com) marketplace.


## <a name="supported-types-of-solutions"></a>Typer som stöds av lösningar
Det första som du vill göra som en utgivare är att definiera vilken typ av lösning för ditt företag erbjuder. Marketplace stöder följande typer av erbjudanden:

|Typ av lösning|Virtuell dator|Lösningsmall|
|---|---|---|
|**Definition**|Förkonfigurerade avbildningar med ett fullständigt installerat operativsystem och en eller flera program. En avbildning av virtuell dator innehåller informationen som behövs för att skapa och distribuera virtuella datorer i tjänsten Azure virtuella datorer.|En datastruktur som kan referera till en eller flera distinkta Azure-tjänster, inklusive tjänster publicerade av andra säljare. Azure-prenumeranter kan använda den för att distribuera ett eller flera erbjudanden i ett enda, samordnad sätt.|
|**Exempel**|Som en Azure utgivare du skapat och verifiera en virtuell dator med en innovativa database-tjänsten. Andra Azure-prenumeranter vill köpa och distribuera den här virtuella datorn i sina molnmiljöer för tjänsten.|Du har paketerade en uppsättning tjänster från i Azure som gör det snabbt distribuera molntjänster med belastningsutjämning, förbättrad säkerhet och hög tillgänglighet som en Azure utgivare. Andra Azure-prenumeranter kan spara tid genom upphandling mallen lösning som uppfyller sina mål. De behöver manuellt hitta, köpa, distribuera och konfigurera samma eller liknande Azure-tjänster.|

> [!NOTE]
> Vissa steg delas mellan de olika typerna av lösningar och andra är olika för respektive typ av lösning. Den här artikeln innehåller en kort översikt över de steg du måste utföra för någon typ av lösningen.

## <a name="publish-a-solution"></a>Publicera en lösning
![Utse, registrera, publicera](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Ange din lösning för före godkännande
Så här publicerar du en virtuell dator [lösning](https://createopportunity.azurewebsites.net) slutföra certifierade för Microsoft Azure Marketplace, **lösning kandidat formuläret**.

>[!NOTE]
> Om du arbetar med en Partner Kontoansvariga eller en DX Partner Manager kan du be dem att utse din lösning för programmet Azure Certified. Du kan även gå till den [Microsoft Azure-certifierad](http://createopportunity.azurewebsites.net) webbsidan och fylla i formuläret för programmet. Ange e-postadress till ditt Partner-kontoansvarige eller DX Partner Manager i den **Microsoft Sponsor Kontakta** rutan.

Om du uppfyller kriterierna i den [Azure Marketplace deltagande principer](http://go.microsoft.com/fwlink/?LinkID=526833) och din ansökan har godkänts, vi börja arbeta med dig att publicera din lösning till Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Registrera ditt konto som ett Microsoft-säljare
Registrera ditt Microsoft-konto som en [Microsoft Developer konto](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publicera din lösning
Följ anvisningarna nedan om du vill publicera en lösning på Marketplace
1. Uppfylla sökassistent krav.

    a. Uppfyller den [sökassistent krav](marketplace-publishing-pre-requisites.md).

    b. Uppfyller den [VM tekniska krav](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Uppfyller den [mall tekniska krav för lösningen](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Skapa erbjudandet.

    a. Skapa en [virtuella](marketplace-publishing-vm-image-creation.md) erbjuder.

    b. Skapa en [lösningsmall](marketplace-publishing-solution-template-creation.md) erbjuder.

3. Skapa erbjudandet [marknadsföring innehåll](marketplace-publishing-push-to-staging.md).

4. Testa erbjudandet i Förproduktion.

    a. Testa VM erbjudandet i [mellanlagring](marketplace-publishing-vm-image-test-in-staging.md).

    b. Testa din lösning mallen erbjudandet i [mellanlagring](marketplace-publishing-solution-template-test-in-staging.md).

5. Distribuera ditt erbjudande den [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Skapa och hantera en avbildning av virtuell dator
Skapa och hantera en VM-avbildning med hjälp av följande resurser:
* Skapa en VM-avbildning [lokalt](marketplace-publishing-vm-image-creation-on-premise.md).
* Skapa en virtuell dator som kör [Windows Azure-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Skapa en virtuell dator som kör [Linux i Azure portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Felsöka vanliga problem som påträffas under [VHD skapa](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Hantera din lösning
Hantera din lösning med hjälp från följande resurser:
* [Läsa guiden släppts för virtuell dator erbjudanden](marketplace-publishing-vm-image-post-publishing.md)
* [Uppdatera sökassistent information om ett erbjudande eller en SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Uppdatera de tekniska detaljerna för ett erbjudande eller en SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Lägg till en ny SKU under listan erbjudande](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Ändra listan SKU datadiskar](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Ta bort ett listade erbjudande från Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Ta bort en listade SKU från Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Ta bort den aktuella versionen av listan SKU från Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Återställa lista priset till produktion värden](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Återställa fakturering modellen till produktion värden](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Återställa inställningar för en listade SKU att produktionsvärdet för](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Ytterligare resurser
[Konfigurera Azure PowerShell](marketplace-publishing-powershell-setup.md)
