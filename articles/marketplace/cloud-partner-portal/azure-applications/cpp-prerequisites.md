---
title: Krav för Azure Application-erbjudande | Azure Marketplace
description: Krav för att publicera ett Azure-program erbjuder på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 296f843f9d09b57e12e867700f456126eca128b3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942888"
---
# <a name="azure-application-prerequisites"></a>Krav för Azure-program

Den här artikeln beskriver de tekniska och affärsmässiga krav för att publicera ett erbjudande om hanterat program på Azure Marketplace.  Om du inte redan har gjort det, kan du granska följande källor till information:
- Beroende på din SKU-typ, antingen [Azure-program: Lösningsmallen erbjuder publicera Guide](../../marketplace-solution-templates.md) eller [Azure-program: Erbjudande om hanterat program publicera Guide](../../marketplace-managed-apps.md)
- [Att skapa mallar för lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) video


## <a name="technical-requirements"></a>Tekniska krav

De tekniska kraven är följande:

*   Azure Resource Manager-mallar för mer information finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Den här artikeln beskriver strukturen för en Azure Resource Manager-mall. Den anger de olika avsnitten i en mall och egenskaperna som är tillgängliga i dessa avsnitt. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution. 
* Azure Quickstart-mallar.<br> Mer information finns i:

  * [Azure-snabbstartmallar](https://azure.microsoft.com/documentation/templates/). Få mer gjort genom att distribuera Azure-resurser via resurshanteraren i Azure med mallar som communityt har bidragit med. Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.
  * [GitHub: Azure Resource Manager-Snabbstartsmallar](https://github.com/azure/azure-quickstart-templates). Den här lagringsplatsen innehåller alla tillgängliga Azure Resource Manager-mallar som tillförts av communityn. En mall för sökbart index hålls på https://azure.microsoft.com/documentation/templates/.
* Skapa UI-Definition<br>
Mer information finns i [skapa Azure portal användargränssnittet för det hanterade programmet](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Den här artikeln beskriver grundläggande begrepp i filen createUiDefinition.json. Azure-portalen använder den här filen för att generera användargränssnittet för att skapa ett hanterat program.


## <a name="business-requirements"></a>Affärskrav

Affärskraven omfattar följande procedur, avtalsenliga och juridiska skyldigheter:

* Du måste vara ett registrerat moln Marketplace Publisher. Om du inte är registrerad, följer du stegen i artikeln [blir molnet Marketplace utgivare](../../become-publisher.md).

>[!NOTE]
>Du bör använda samma konto för registrering av Microsoft Developer Center för att logga in på partnerportalen i molnet. Du bör ha endast en Microsoft-konto för alla dina Azure Marketplace-erbjudanden. Det här kontot får inte vara specifika för enskilda tjänster eller erbjudanden.

* Ditt företag (eller dess filial) måste vara i sälj-från-land/region som stöds av Azure Marketplace. En aktuell lista över dessa länder/regioner, se [Deltagandepolicyer för Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Produkten måste licensieras på ett sätt som är kompatibel med faktureringsmodellerna som stöds av Azure Marketplace. Mer information finns i [faktureringsalternativ](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) på Azure Marketplace.
* Du är ansvarig för att göra teknisk support tillgänglig för kunder på ett kommersiellt rimligt sätt. Detta stöd kan vara kostnadsfria, betald, eller via community-metoder.
* Du är ansvarig för att licensiera ditt program och eventuella beroenden för programvara från tredje part.
* Du måste ange innehåll som uppfyller villkoren för ditt erbjudande ska visas på Azure Marketplace och i Azure-portalen.
* Du måste godkänna villkoren i Deltagandepolicyer för Microsoft Azure Marketplace och avtalet för utgivare.
* Du måste vara kompatibel med Microsoft Azure av användningsvillkor för webbplatsen, sekretesspolicy för Microsoft och Microsoft Azure Certified-programavtalet.


## <a name="publishing-requirements"></a>Krav för publicering

Om du vill publicera ett nytt erbjudande för Azure-program, måste du uppfylla följande krav:

* Ha dina metadata som är redo att användas. I följande lista (ofullständig) visas ett exempel på dessa metadata:
  * En rubrik
  * En beskrivning (i HTML-format)
  * En logotyp (i PNG-format) och fasta bildstorleken i den här: 40 x 40 bildpunkter, 90 x 90 bildpunkter, 115 x 115 bildpunkter och 255 x 115 bildpunkter.
* En *användningsvillkor* och en *sekretesspolicy* dokument
* Dokumentation om Application
* Supportkontakter


## <a name="next-steps"></a>Nästa steg

När du har uppfyllt alla krav, måste du vara redo att [skapa ett Azure-program-erbjudande](./cpp-create-offer.md). 
 
