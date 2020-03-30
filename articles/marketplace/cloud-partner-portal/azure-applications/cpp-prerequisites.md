---
title: Förutsättningar för Azure-program | Azure Marketplace
description: Förutsättningarna för att publicera ett Azure-programerbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281756"
---
# <a name="azure-application-prerequisites"></a>Förutsättningar för Azure-program

I den här artikeln beskrivs de tekniska och affärsmässiga förutsättningarna för att publicera ett hanterat programerbjudande på Azure Marketplace.  Om du inte redan har gjort det läser du följande informationskällor:
- Beroende på din SKU-typ kan antingen [Azure Applications: Solution Template Offer Publishing Guide](../../marketplace-solution-templates.md) eller Azure [Applications: Managed Application Offer Publishing Guide](../../marketplace-managed-apps.md)
- [Skapa lösningsmallar och hanterade program för Azure Marketplace-videon](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="technical-requirements"></a>Tekniska krav

De tekniska kraven omfattar följande punkter:

*   Azure Resource Manager-mallar Mer information finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). I den här artikeln beskrivs strukturen för en Azure Resource Manager-mall. Den visar de olika avsnitten i en mall och de egenskaper som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att konstruera värden för distributionen. 
* Azure Snabbstartsmallar.<br> Mer information finns i:

  * [Snabbstartsmallar för Azure](https://azure.microsoft.com/documentation/templates/). Distribuera Azure-resurser via Azure Resource Manager med community-bidragade mallar för att få mer gjort. Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.
  * [GitHub: Snabbstartsmallar för Azure Resource Manager](https://github.com/azure/azure-quickstart-templates). Den här hanteraren innehåller alla tillgängliga Azure Resource Manager-mallar som communityn har bidragit med. Ett sökbart mallindex behålls på https://azure.microsoft.com/documentation/templates/.
* Skapa användargränssnittsdefinition<br>
Mer information finns i [Skapa Azure Portal-användargränssnitt för ditt hanterade program](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Den här artikeln introducerar de centrala begreppen i filen createUiDefinition.json. Azure-portalen använder den här filen för att generera användargränssnittet för att skapa ett hanterat program.


## <a name="business-requirements"></a>Affärskrav

Affärskraven omfattar följande processuella, avtalsenliga och rättsliga skyldigheter:

* Du måste vara registrerad Cloud Marketplace Publisher. Om du inte är registrerad följer du stegen i artikeln [Bli en Cloud Marketplace Publisher](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Du bör använda samma registreringskonto för Microsoft Developer Center för att logga in på Cloud Partner Portal. Du bör bara ha ett Microsoft-konto för dina Azure Marketplace-erbjudanden. Det här kontot ska inte vara specifikt för enskilda tjänster eller erbjudanden.

* Ditt företag (eller dess dotterbolag) måste bebetas i en sälj-från-land/region som stöds av Azure Marketplace. En aktuell lista över dessa länder/regioner finns i [Microsoft Azure Marketplace Participation Policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Din produkt måste vara licensierad på ett sätt som är kompatibelt med faktureringsmodeller som stöds av Azure Marketplace. Mer information finns i [faktureringsalternativ](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) på Azure Marketplace.
* Du är ansvarig för att göra teknisk support tillgänglig för kunder på ett kommersiellt rimligt sätt. Detta stöd kan vara gratis, betalas, eller genom gemenskap metoder.
* Du är ansvarig för licensiering av din programvara och eventuella programvaruberoenden från tredje part.
* Du måste ange innehåll som uppfyller kriterierna för att ditt erbjudande ska visas på Azure Marketplace och i Azure-portalen.
* Du måste godkänna villkoren i Microsoft Azure Marketplace Participation Policies och Publisher Agreement.
* Du måste följa användarvillkoren för Microsoft Azure-webbplatsen, Microsofts sekretesspolicy och Microsoft Azure Certified Program Agreement.


## <a name="publishing-requirements"></a>Publiceringskrav

Om du vill publicera ett nytt Azure-programerbjudande måste du uppfylla följande förutsättningar:

* Ha dina metadata redo att användas. Följande lista (ej uttömmande) visar ett exempel på dessa metadata:
  * En titel
  * En beskrivning (i HTML-format)
  * En logotypbild (i PNG-format) och i dessa fasta bildstorlekar: 40 x 40 pixlar, 90 x 90 pixlar, 115 x 115 pixlar och 255 x 115 pixlar.
* *Användningsvillkor* och *sekretesspolicydokument*
* Dokumentation av program
* Supportkontakter


## <a name="next-steps"></a>Nästa steg

När du har uppfyllt alla krav är du redo att [skapa ett Azure-programerbjudande](./cpp-create-offer.md). 
 
