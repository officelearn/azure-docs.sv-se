---
title: Förstå livscykeln för en skiss
description: Lär dig mer om livscykeln som en skissdefinition går igenom och information om varje steg, inklusive uppdatering och borttagning av skisstilldelningar.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: 46da42ec97f16af91ef8e41362a085d466bbb887
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677376"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Förstå livscykeln för en Azure Blueprint

Precis som många resurser i Azure har en skiss i Azure Blueprints en typisk och naturlig livscykel. De skapas, distribueras och tas slutligen bort när de inte längre behövs eller är relevanta. Azure Blueprints stöder vanliga livscykelåtgärder. Den bygger sedan på dem för att ge ytterligare statusnivåer som stöder gemensam kontinuerlig integrering och kontinuerlig distribution pipelines för organisationer som hanterar sin infrastruktur som kod - en viktig del i DevOps.

För att till fullo förstå en ritning och stadierna täcker vi en standardlivscykel:

> [!div class="checklist"]
> - Skapa och redigera en skiss
> - Publicera ritningen
> - Skapa och redigera en ny version av skissen
> - Publicera en ny version av skissen
> - Ta bort en viss version av skissen
> - Ta bort skissen

## <a name="creating-and-editing-a-blueprint"></a>Skapa och redigera en skiss

När du skapar en skiss lägger du till artefakter i den, sparar i en hanteringsgrupp eller prenumeration och tillhandahåller ett unikt namn och en unik version. Skissen är nu i ett **utkastläge** och kan ännu inte tilldelas. I **utkastläget** kan det fortsätta att uppdateras och ändras.

En aldrig publicerad skiss i **utkastläge** visar en annan ikon på sidan **Skissdefinitioner** än de som har **publicerats**. Den **senaste versionen** visas som **utkast** för dessa aldrig publicerade ritningar.

Skapa och redigera en skiss med [Azure-portalen](../create-blueprint-portal.md#create-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicera en skiss

När alla planerade ändringar har gjorts i en skiss i **utkastläge** kan den **publiceras** och göras tillgänglig för tilldelning. Den **publicerade** versionen av skissen kan inte ändras. När **skissen har publicerats**visas den med en annan ikon än **utkastritningar** och det angivna versionsnumret visas i kolumnen **Senaste version.**

Publicera en skiss med [Azure-portalen](../create-blueprint-portal.md#publish-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Skapa och redigera en ny version av skissen

Det går inte att ändra en **publicerad** version av en skiss. En ny version av skissen kan dock läggas till i den befintliga skissen och ändras efter behov. Gör ändringar i en befintlig skiss genom att redigera den. När de nya ändringarna sparas har skissen nu **Opublicerade ändringar**. Dessa ändringar är en ny **utkastversion** av skissen.

Redigera en skiss med [Azure-portalen](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicera en ny version av skissen

Varje redigerad version av en skiss måste **publiceras** innan den kan tilldelas. När **Opublicerade ändringar** har gjorts i en skiss men inte **publicerats**är knappen **Publicera skiss** tillgänglig på sidan redigera skiss. Om knappen inte visas har skissen redan **publicerats** och inga **opublicerade ändringar**.

> [!NOTE]
> En enda skiss kan ha flera **publicerade** versioner som var och en kan tilldelas prenumerationer.

Om du vill publicera en skiss med **Opublicerade ändringar**använder du samma steg för att publicera en ny skiss.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Ta bort en viss version av skissen

Varje version av en skiss är ett unikt objekt och kan **publiceras**individuellt. Därför kan varje version av en skiss också tas bort. Om du tar bort en version av en skiss påverkas inte andra versioner av skissen.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva tilldelningar. Ta bort tilldelningarna först och ta sedan bort den version som du vill ta bort.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Skissdefinitioner** från sidan till vänster och använd filteralternativen för att hitta skissen som du vill ta bort en version av. Markera den för att öppna redigeringssidan.

1. Välj fliken **Publicerade versioner** och leta reda på den version du vill ta bort.

1. Högerklicka på versionen för att ta bort och välj **Ta bort den här versionen**.

## <a name="deleting-the-blueprint"></a>Ta bort skissen

Kärnritningen kan också tas bort. Om du tar bort kärnritningen tas även alla skissversioner av skissen bort, inklusive både **utkast** **och publicerade** ritningar. Precis som när du tar bort en version av en skiss tas inte de befintliga tilldelningarna för någon av skissversionerna bort om du tar bort kärnritningen.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva tilldelningar. Ta bort tilldelningarna först och ta sedan bort den version som du vill ta bort.

Ta bort en skiss med [Azure-portalen](../create-blueprint-portal.md#delete-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Tilldelningar

Det finns flera punkter under livscykeln som en skiss kan tilldelas en prenumeration. När läget för en version av skissen **publiceras**kan den versionen tilldelas en prenumeration. Med den här livscykeln kan versioner av en skiss användas och aktivt tilldelas medan en nyare version utvecklas.

När versioner av ritningar tilldelas är det viktigt att förstå var de är tilldelade och med vilka parametrar de har tilldelats. Parametrarna kan antingen vara statiska eller dynamiska. Mer information finns i [statiska och dynamiska parametrar](parameters.md).

### <a name="updating-assignments"></a>Uppdatera tilldelningar

När en skiss har tilldelats kan tilldelningen uppdateras. Det finns flera orsaker till att uppdatera en befintlig tilldelning, bland annat:

- Lägga till eller ta bort [resurslåsning](resource-locking.md)
- Ändra värdet på [dynamiska parametrar](parameters.md#dynamic-parameters)
- Uppgradera tilldelningen till en nyare **publicerad** version av skissen

Mer information finns i [uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Ta bort tilldelningar

Om skissen inte längre behövs kan den inte tilldelas från hanteringsgruppen eller prenumerationen. Under skissanpassning inträffar följande:

- Borttagning av [skissresurslåsning](resource-locking.md)
- Borttagning av objekt för skisstilldelning
- (Villkorlig) Om en **systemtilldelad hanterad identitet** användes tas den också bort

> [!NOTE]
> Alla resurser som distribueras av skisstilldelningen finns kvar, men skyddas inte längre av Azure Blueprints.

## <a name="next-steps"></a>Nästa steg

- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).