---
title: Privata erbjudanden på Microsofts kommersiella marknads platser
description: Privata erbjudanden på Microsofts kommersiella marknads plats för app-och tjänst utgivare.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: 5396845e13e94b4d957f42618baaf19c408f559d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843782"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Privata erbjudanden på Microsofts kommersiella marknads plats

Privata erbjudanden i [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) ger utgivare möjlighet att skapa planer som endast är synliga för riktade kunder. I den här artikeln beskrivs alternativen och fördelarna med privata erbjudanden.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Lås upp företags erbjudanden med privata erbjudanden

Företags kunder kan använda marknads platser online för att hitta, prova och köpa moln lösningar. Nu med privata erbjudanden kan utgivare använda Marketplace för privat delning av anpassade lösningar med riktade kunder med funktioner som företag kräver:

- Med *förhandlad prissättning* kan utgivare utöka rabatter och priser utanför listan från offentligt tillgängliga erbjudanden.
- *Privata allmänna* villkor gör det möjligt för utgivare att skräddarsy villkor för en specifik kund.
- *Specialiserade konfigurationer* gör att utgivare skräddarsyr sina Virtual Machines, Azure-program och SaaS-appar till en enskild kunds behov. Det här alternativet gör det också möjligt för utgivare att tillhandahålla för hands versions åtkomst till nya produkt funktioner innan de lanseras för alla kunder.

Privata erbjudanden gör att utgivare kan dra nytta av skalbarheten och den globala tillgängligheten för en offentlig marknads plats, med flexibilitet och kontroll som behövs för att förhandla fram och leverera anpassade erbjudanden och konfigurationer. Tillsammans öppnar dessa funktioner dörren till starkt företags införande av moln marknads platser. Företag kan nu köpa och sälja på det sätt de förväntar sig och efter frågan.

Privata erbjudanden är nu tillgängliga för virtuell dator, Azure Application (implementeras som lösningsfiler eller hanterade program) och SaaS-appar erbjuder.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Skapa privata erbjudanden med hjälp av planer

För *nya eller befintliga erbjudanden med planer* kan utgivare enkelt skapa nya, privata variationer genom att skapa nya planer (tidigare kallade SKU: er) och markera dem som privata. Varje erbjudande kan ha upp till 45 privata planer.

<!--- [Private SKUs]() --->

Privata planer är komponenter i ett erbjudande och är bara synliga och köpbara av de riktade kunderna. Privata planer kan återanvända bas avbildningar och/eller erbjudna metadata som redan har publicerats för ett offentligt abonnemang. Med det här alternativet kan utgivare skapa flera privata varianter av ett offentligt erbjudande utan att behöva publicera flera versioner av samma bas avbildning och erbjuda metadata. När en privat plan delar en bas avbildning med en offentlig plan för en virtuell dator och Azure-program, kommer eventuella ändringar i bas avbildningen för erbjudandet att spridas över alla offentliga och privata planer med den bas avbildningen.

För *nya erbjudanden som bara omfattar privata planer* kan utgivare skapa sina erbjudanden som ett annat erbjudande och sedan markera planerna som privata. Erbjudanden som bara har privata planer kommer inte att kunna identifieras eller nås via Microsofts kommersiella marknads plats eller [Azure Portal](https://azure.microsoft.com/features/azure-portal/) av kunder som inte är associerade med erbjudandet.

>[!NOTE]
>Ett erbjudande som bara innehåller privata planer visas inte i den offentliga Azure Marketplace eller AppSource.

## <a name="targeting-customers-with-private-offers"></a>Rikta kunder mot privata erbjudanden

För både nya och befintliga privata erbjudanden kan utgivare rikta kunder med prenumerations-ID. Utgivare som använder en virtuell dator eller Azure Application erbjudande kan begränsa tillgängligheten för en privat plan till ett enskilt Azure-prenumerations-ID eller ladda upp en CSV med upp till 20 000 Azure-prenumerations-ID. När du använder ett privat erbjudande i SaaS-appen kan utgivare associera antingen ett klient-ID för att begränsa tillgängligheten för en privat plan med hjälp av antingen manuell eller CSV-överföring.

När ett erbjudande har certifierats och publicerats kan kunder uppdateras eller tas bort från planen inom några minuter med hjälp av funktionen synkronisera privata prenumerationer. Den här funktionen gör det möjligt för utgivare att snabbt och enkelt uppdatera listan över kunder som den privata planen presenteras i utan att du behöver certifiera eller publicera erbjudandet igen.

>[!NOTE]
>Tillämplighet i Azure gov: privata erbjudanden (eller mer korrekt: privata planer) är tillgängliga för gov-moln som alla andra erbjudanden. En plan kan också byta status från privat till offentlig och vice versa. Den kontroll på vilken moln erbjudandet kommer att vara tillgängligt finns i partnerns händer och kan konfigureras via partner Center. 

## <a name="deploying-private-offers"></a>Distribuera privata erbjudanden

Privata erbjudanden kan bara upptäckas via [Azure Portal](https://azure.microsoft.com/features/azure-portal/) och visas inte i [Microsoft AppSource](https://appsource.microsoft.com/) eller [Azure Marketplace](https://azuremarketplace.microsoft.com). Mer information om hur du publicerar till de olika onlinebutiker för kommersiella platser finns i [bestämma publicerings alternativ](./determine-your-listing-type.md).

När du har loggat in på Azure Portal kan kunderna välja navigerings element för Marketplace för att få åtkomst till sina privata erbjudanden. Privata erbjudanden visas också i Sök Resultat och kan distribueras via kommando raden och Azure Resource Manager mallar som andra erbjudanden.

![[Privata erbjudanden]](./media/marketplace-publishers-guide/private-offer.png)

Privata erbjudanden visas också i Sök resultaten. Leta bara efter det **privata** märket.

>[!Note]
>Privata erbjudanden stöds inte med prenumerationer som upprättats via en åter försäljare av CSP-programmet (Cloud Solution Provider).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
