---
title: Krav för Azure Application erbjudandet | Azure Marketplace
description: Krav för att publicera ett erbjudande om Azure-program på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826145"
---
# <a name="azure-application-prerequisites"></a>Krav för Azure-program

Den här artikeln beskriver de tekniska och affärs kraven för att publicera ett hanterat program erbjudande på Azure Marketplace.  Om du inte redan har gjort det går du igenom följande informations källor:
- Beroende på din SKU-typ, antingen [Azure-program: lösnings mal len erbjuder publicerings guide](../../marketplace-solution-templates.md) eller [Azure-program: publicerings guide för hanterade program erbjudanden](../../marketplace-managed-apps.md)
- [Skapa Solution-mallar och hanterade program för Azure Marketplace-](https://channel9.msdn.com/Events/Build/2018/BRK3603) videon


## <a name="technical-requirements"></a>Tekniska krav

De tekniska kraven omfattar följande objekt:

*   Azure Resource Manager mallar mer information finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). I den här artikeln beskrivs strukturen för en Azure Resource Manager-mall. Den visar de olika avsnitten i en mall och de egenskaper som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution. 
* Azure snabb starts mallar.<br> Mer information finns i:

  * [Azure-snabbstartmallar](https://azure.microsoft.com/documentation/templates/). Få mer gjort genom att distribuera Azure-resurser via resurshanteraren i Azure med mallar som communityt har bidragit med. Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.
  * [GitHub: Azure Resource Manager snabb starts mallar](https://github.com/azure/azure-quickstart-templates). Den här lagrings platsen innehåller alla tillgängliga Azure Resource Manager mallar som communityn bidragit till. Ett sökbart Template index underhålls på https://azure.microsoft.com/documentation/templates/.
* Skapa UI-definition<br>
Mer information finns i [skapa Azure Portal användar gränssnitt för ditt hanterade program](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). I den här artikeln beskrivs de grundläggande begreppen i filen createUiDefinition. JSON. Azure Portal använder den här filen för att generera användar gränssnittet för att skapa ett hanterat program.


## <a name="business-requirements"></a>Affärskrav

Affärs kraven omfattar följande procedurer, avtal och juridiska skyldigheter:

* Du måste vara registrerad Cloud Marketplace-utgivare. Om du inte har registrerat dig följer du stegen i artikeln [bli en moln Marketplace-utgivare](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Du bör använda samma registrerings konto för Microsoft Developer Center för att logga in på Cloud Partner Portal. Du bör bara ha ett Microsoft-konto för dina Azure Marketplace-erbjudanden. Detta konto bör inte vara specifikt för enskilda tjänster eller erbjudanden.

* Ditt företag (eller dess dotter bolag) måste finnas i en försäljnings-från-land/-region som stöds av Azure Marketplace. En aktuell lista över dessa länder/regioner finns i [Microsoft Azure Marketplace deltagar principer](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Produkten måste vara licensierad på ett sätt som är kompatibelt med fakturerings modeller som stöds av Azure Marketplace. Mer information finns i [fakturerings alternativ](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) i Azure Marketplace.
* Du är ansvarig för att göra teknisk support tillgänglig för kunderna på ett kommersiellt rimligt sätt. Detta stöd kan vara kostnads fritt, betalas eller via community-metoder.
* Du ansvarar för att licensiera din program vara och program vara från tredje part.
* Du måste ange innehåll som uppfyller kriterierna för att ditt erbjudande ska visas på Azure Marketplace och i Azure Portal.
* Du måste samtycka till villkoren i principerna för Microsoft Azure Marketplace deltagande och utgivarens avtal.
* Du måste följa användnings villkoren för Microsoft Azure webbplatsen, Microsofts sekretess policy och Microsoft Azure certifierade program avtal.


## <a name="publishing-requirements"></a>Publicerings krav

Om du vill publicera ett nytt erbjudande för Azure-program måste du uppfylla följande krav:

* Ha dina metadata redo att användas. Följande lista (icke-uttömmande) visar ett exempel på följande metadata:
  * En rubrik
  * En beskrivning (i HTML-format)
  * En logo typ bild (i PNG-format) och i dessa fasta bild storlekar: 40 x 40 pixlar, 90 x 90 pixlar, 115 x 115 pixlar och 255 x 115 bild punkter.
* *Användnings villkor* och dokument för *sekretess policy*
* Program dokumentation
* Support kontakter


## <a name="next-steps"></a>Nästa steg

När du har uppfyllt alla krav är det dags att [skapa ett erbjudande för Azure-program](./cpp-create-offer.md). 
 
