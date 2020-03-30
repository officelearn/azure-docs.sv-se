---
title: Privata erbjudanden | Azure Marketplace
description: Privata erbjudanden på Azure Marketplace för app- och tjänstutgivare.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/1/2018
ms.author: dsindona
ms.openlocfilehash: 67aba077304117ad357d4e004ce7bdb25ac58352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285052"
---
# <a name="private-offers"></a>Privata erbjudanden

Privata erbjudanden på [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) gör det möjligt för utgivare att skapa SKU:er som bara är synliga för riktade kunder.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Lås upp företagserbjudanden med privata erbjudanden

Företagskunder använder i allt högre grad onlinemarknadsplatser för att hitta, prova och köpa molnlösningar. Nu med privata erbjudanden kan utgivare använda marketplace för att privat dela anpassade lösningar med riktade kunder med funktioner som företag behöver:

- *Med förhandlad prissättning* kan utgivare utöka rabatter och priser utanför listan från offentligt tillgängliga erbjudanden.
- *Privata villkor* gör det möjligt för utgivare att skräddarsy villkor för en viss kund.
- *Med specialiserade konfigurationer* kan utgivare skräddarsy sina virtuella datorer, Azure-program och SaaS-appar erbjuda efter en enskild kunds behov. Med det här alternativet kan utgivare också ge förhandsåtkomst till nya produktfunktioner innan de lanseras mer allmänt för alla kunder.

Privata erbjudanden gör det möjligt för utgivare att dra nytta av omfattningen och den globala tillgängligheten för en offentlig marknadsplats, med den flexibilitet och kontroll som krävs för att förhandla fram och leverera anpassade erbjudanden och konfigurationer. Tillsammans öppnar dessa funktioner dörren för stark företagsanvändning av molnmarknadsplatser.  Företag kan nu köpa och sälja på ett sätt de förväntar sig och efterfrågan.

Privata erbjudanden är nu tillgängliga för Virtual Machine, Azure Application (implementerat som lösningsmallar eller hanterade program) och SaaS Apps-erbjudanden. Precis som offentliga erbjudanden kan privata erbjudanden skapas och hanteras via [Cloud Partner Portal.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)  Kunder kan beviljas eller återkallas åtkomst till privata erbjudanden på några minuter.

## <a name="creating-private-offers-using-skus-and-plans"></a>Skapa privata erbjudanden med SKU:er och planer

För *nya eller befintliga erbjudanden med offentliga SKU:er eller planer*kan utgivare enkelt skapa nya, privata varianter genom att skapa nya SKU:er eller planer och markera dem som privata.  [Privata SKU:er](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) och planer är komponenter i ett erbjudande och är endast synliga och köpas av de riktade kunderna. Privata SKU:er och planer kan återanvända basbilder och/eller erbjuda metadata som redan publicerats för en offentlig SKU eller plan. Med det här alternativet kan utgivare skapa flera privata varianter av ett offentligt erbjudande utan att behöva publicera flera versioner av samma basavbildning och erbjuda metadata. För Virtual Machine och Azure-program erbjuder endast, när en privat SKU delar en basavbildning med en offentlig SKU, sprids alla ändringar i erbjudandets basavbildning över alla offentliga och privata SKU:er med basavbildningen.

För *nya erbjudanden som bara innehåller privata SKU:er eller planer*kan utgivare skapa sina erbjudanden som alla andra erbjudanden och sedan markera SKU:erna eller planerna som privata. De erbjudanden som bara har privata SKU:er eller abonnemang kan inte identifieras eller vara tillgängliga via [Azure Marketplace](https://azuremarketplace.microsoft.com) eller [Azure-portalen](https://azure.microsoft.com/features/azure-portal/) av kunder som inte är associerade med erbjudandet.

## <a name="targeting-customers-with-private-offers"></a>Rikta in dig på kunder med privata erbjudanden
För både nya och befintliga privata erbjudanden kan utgivare rikta in sig på kunder med hjälp av prenumerationsidentifierare. Utgivare som använder ett erbjudande om virtuell dator eller Azure-program kan begränsa tillgängligheten för en privat SKU till ett enskilt Azure-prenumerations-ID eller ladda upp en CSV på upp till 20 000 Azure-prenumerations-ID:n. När du använder ett privat SaaS-app-erbjudande kan utgivare associera antingen ett Azure-prenumerations-ID eller ett klient-ID för att begränsa tillgängligheten för en privat plan, med hjälp av antingen den manuella eller CSV-uppladdningsmetoden.

När ett erbjudande har certifierats och publicerats kan kunder uppdateras eller tas bort från SKU eller Plan inom några minuter med hjälp av funktionen Synkronisera privata prenumerationer. Med den här funktionen kan utgivare snabbt och enkelt uppdatera listan över kunder som den privata SKU-planen eller planen presenteras till utan att omcertifiera eller publicera om erbjudandet.

## <a name="deploying-private-offers"></a>Distribuera privata erbjudanden

Privata erbjudanden kan endast identifieras via [Azure-portalen](https://azure.microsoft.com/features/azure-portal/) och presenteras inte via [Azure Marketplace](https://azuremarketplace.microsoft.com). När du har loggat in på Azure-portalen kan kunderna välja Marketplace-navigeringselementet för att komma åt sina privata erbjudanden. Privata erbjudanden visas också i sökresultaten och kan distribueras via kommandorad och Azure Resource Manager-mallar som alla andra erbjudanden.

![[Privata erbjudanden]](./media/marketplace-publishers-guide/private-offer.png)

Privata erbjudanden visas också i sökresultaten. Se bara upp för "Private"-brickan.

>[!Note]
>Privata erbjudanden stöds inte med prenumerationer som upprättats via en återförsäljare av Cloud Solution Provider-programmet (CSP).

## <a name="next-steps"></a>Nästa steg

Om du vill börja använda privata erbjudanden följer du stegen i guiden [Privata SKU:er och abonnemang.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)
