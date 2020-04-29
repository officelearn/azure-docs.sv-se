---
title: Privata erbjudanden | Azure Marketplace
description: Privata erbjudanden i Azure Marketplace för app-och tjänst utgivare.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681436"
---
# <a name="private-offers"></a>Privata erbjudanden

Privata erbjudanden på [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) aktivera utgivare för att skapa SKU: er som endast är synliga för riktade kunder.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Lås upp företags erbjudanden med privata erbjudanden

Företags kunder kan använda marknads platser online för att hitta, prova och köpa moln lösningar. Nu med privata erbjudanden kan utgivare använda Marketplace för privat delning av anpassade lösningar med riktade kunder med funktioner som företag kräver:

- Med *förhandlad prissättning* kan utgivare utöka rabatter och priser utanför listan från offentligt tillgängliga erbjudanden.
- *Privata allmänna* villkor gör det möjligt för utgivare att skräddarsy villkor för en specifik kund.
- *Specialiserade konfigurationer* gör att utgivare skräddarsyr sina Virtual Machines, Azure-program och SaaS-appar till en enskild kunds behov. Det här alternativet gör det också möjligt för utgivare att tillhandahålla för hands versions åtkomst till nya produkt funktioner innan de lanseras för alla kunder.

Privata erbjudanden gör att utgivare kan dra nytta av skalbarheten och den globala tillgängligheten för en offentlig marknads plats, med flexibilitet och kontroll som behövs för att förhandla fram och leverera anpassade erbjudanden och konfigurationer. Tillsammans öppnar dessa funktioner dörren till starkt företags införande av moln marknads platser. Företag kan nu köpa och sälja på det sätt de förväntar sig och efter frågan.

Privata erbjudanden är nu tillgängliga för virtuell dator, Azure Application (implementeras som lösningsfiler eller hanterade program) och SaaS-appar erbjuder. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Skapa privata erbjudanden med SKU: er och planer

För *nya eller befintliga erbjudanden med offentliga SKU: er eller planer*kan utgivare enkelt skapa nya, privata variationer genom att skapa nya SKU: er eller planer och markera dem som privata.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

Privata SKU: er och planer är komponenter i ett erbjudande och är bara synliga och köpbara av de riktade kunderna. Privata SKU: er och planer kan återanvända bas avbildningar och/eller erbjudande-metadata som redan har publicerats för en offentlig SKU eller plan. Med det här alternativet kan utgivare skapa flera privata varianter av ett offentligt erbjudande utan att behöva publicera flera versioner av samma bas avbildning och erbjuda metadata. När en privat SKU delar en bas avbildning med en offentlig SKU för Virtual Machine och Azure-program, kommer eventuella ändringar i bas avbildningen av erbjudandet att spridas över alla offentliga och privata SKU: er som använder den bas avbildningen.

För *nya erbjudanden som bara innehåller privata SKU: er eller planer*kan utgivare skapa sina erbjudanden som ett annat erbjudande och sedan markera SKU: er eller planer som privata. Erbjudanden som bara har privata SKU: er eller planer kommer inte att kunna identifieras eller nås via Microsofts handels marknads plats eller [Azure Portal](https://azure.microsoft.com/features/azure-portal/) av kunder som inte är associerade med erbjudandet.

## <a name="targeting-customers-with-private-offers"></a>Rikta kunder mot privata erbjudanden

För både nya och befintliga privata erbjudanden kan utgivare rikta kunder med prenumerations-ID. Utgivare som använder en virtuell dator eller Azure Application erbjudande kan begränsa tillgängligheten för en privat SKU till ett individuellt Azure-prenumerations-ID eller ladda upp en CSV med upp till 20 000 Azure-prenumerations-ID. När du använder ett privat erbjudande i SaaS-appen kan utgivare associera antingen ett klient-ID för att begränsa tillgängligheten för en privat plan med hjälp av antingen manuell eller CSV-överföring.

När ett erbjudande har certifierats och publicerats kan kunder uppdateras eller tas bort från SKU: n eller planera inom några minuter med hjälp av funktionen synkronisera privata prenumerationer. Den här funktionen gör det möjligt för utgivare att snabbt och enkelt uppdatera listan över kunder som den privata SKU: n eller planen presenteras i utan att du behöver certifiera eller publicera erbjudandet igen.

## <a name="deploying-private-offers"></a>Distribuera privata erbjudanden

Privata erbjudanden kan bara upptäckas via [Azure Portal](https://azure.microsoft.com/features/azure-portal/) och visas inte i [Microsoft AppSource](https://appsource.microsoft.com/) eller [Azure Marketplace](https://azuremarketplace.microsoft.com). Mer information om hur du publicerar till de olika butiker för kommersiella marknads platser finns i [bestämma publicerings alternativ](./determine-your-listing-type.md).

När du har loggat in på Azure Portal kan kunderna välja navigerings element för Marketplace för att få åtkomst till sina privata erbjudanden. Privata erbjudanden visas också i Sök Resultat och kan distribueras via kommando raden och Azure Resource Manager mallar som andra erbjudanden.

![[Privata erbjudanden]](./media/marketplace-publishers-guide/private-offer.png)

Privata erbjudanden visas också i Sök resultaten. Leta bara efter "privat"-märket.

>[!Note]
>Privata erbjudanden stöds inte med prenumerationer som upprättats via en åter försäljare av Cloud Solution Provider-programmet (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->