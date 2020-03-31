---
title: Distribuera associationer för hanterade program med hjälp av principen
description: Lär dig mer om att distribuera associationer för ett hanterat program med Azure Policy-tjänsten.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650947"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Distribuera associationer för ett hanterat program med Azure Policy

Azure-principer kan användas för att distribuera associationer för att associera resurser till ett hanterat program. I den här artikeln beskriver vi en inbyggd princip som distribuerar associationer och hur du kan använda den principen.

## <a name="built-in-policy-to-deploy-associations"></a>Inbyggd princip för att distribuera associationer

Distribuera associationer för ett hanterat program är en inbyggd princip som kan användas för att distribuera associationer för att associera en resurs till ett hanterat program. Principen accepterar tre parametrar:

- Hanterat program-ID - Det här ID:t är resurs-ID för det hanterade programmet som resurserna måste associeras till.
- Resurstyper att associera - Dessa resurstyper är en lista över resurstyper som ska associeras till det hanterade programmet. Du kan associera flera resurstyper till ett hanterat program med samma princip.
- Associationsnamnprefix - Den här strängen är det prefix som ska läggas till namnet på den associationsresurs som skapas. Standardvärdet är "DeployedByPolicy".

Principen använder DeployIfNotExists utvärdering. Den körs efter att en resursprovider har hanterat en begäran om att skapa eller uppdatera resurser för de valda resurstyper och utvärderingen har returnerat en statuskod för lyckade uppgifter. Därefter distribueras associationsresursen med hjälp av en malldistribution.
Mer information om associationer finns i [Azure Custom Providers resurs introduktion](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Så här använder du den inbyggda distributionsprincipen 

### <a name="prerequisites"></a>Krav
Om det hanterade programmet behöver behörigheter till prenumerationen för att utföra en åtgärd, skulle principdistributionen av associationsresursen inte fungera utan att bevilja behörigheterna.

### <a name="policy-assignment"></a>Principtilldelning
Om du vill använda den inbyggda principen skapar du en principtilldelning och tilldelar distributionsassociationerna för en hanterad programprincip. När principen har tilldelats har tilldelats identifierar principen icke-kompatibla resurser och distribuerar association för dessa resurser.

![Tilldela inbyggd princip](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Få hjälp

Om du har frågor om utveckling av Azure Custom Resource Providers kan du prova att fråga dem på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kan redan ha besvarats, så kontrollera först innan du postar. Lägg till ```azure-custom-providers``` taggen för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om hur du använder inbyggd princip för att distribuera associationer. Se de här artiklarna om du vill veta mer:

- [Begrepp: Azure Custom Providers resurs introduktion](../custom-providers/concepts-resource-onboarding.md)
- [Självstudiekurs: Onboarding av resurser med anpassade leverantörer](../custom-providers/tutorial-resource-onboarding.md)
- [Självstudiekurs: Skapa anpassade åtgärder och resurser i Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Snabbstart: Skapa en anpassad resursleverantör och distribuera anpassade resurser](../custom-providers/create-custom-provider.md)
- [Så här lägger du till anpassade åtgärder i ett Azure REST API](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Så här lägger du till anpassade resurser i ett Azure REST API](../custom-providers/custom-providers-resources-endpoint-how-to.md)
