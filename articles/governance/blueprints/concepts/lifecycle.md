---
title: Förstå livscykeln för en skiss
description: Läs mer om livscykel som passerar en skiss och information om varje steg.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: a57085fa37efd56a46b740d8cbc4278dc53cf39f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795431"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Förstå livscykeln för en Azure-skiss

Precis som många resurser i Azure har en skiss i Azure skisser en typisk och naturlig livscykel. De är skapade, distribueras och slutligen bort när inte längre behövs eller relevanta.
Skisser stöder standard livscykelåtgärder. Den sedan bygger på dem för att ge extra kontrollnivåer status som har stöd för vanliga kontinuerlig integrering och kontinuerlig distribution pipelines för organisationer som hanterar sin infrastruktur som kod – en nyckelfaktor i DevOps.

För att helt förstå en skiss och stegen kan går vi igenom en standard livscykel:

> [!div class="checklist"]
> - Skapa och redigera en skiss
> - Publicera skissen
> - Skapa och redigera en ny version av skissen
> - Publicera en ny version av skissen
> - Tar bort en specifik version av skissen
> - Tar bort skissen

## <a name="creating-and-editing-a-blueprint"></a>Skapa och redigera en skiss

När du skapar en skiss, Lägg till artefakter, spara till en hanteringsgrupp eller prenumeration och tillhandahålls ett unikt namn och en unik version. Skissen är nu i en **Draft** läge och kan ännu inte tilldelas. I den **Draft** läge, det kan fortsätta att uppdateras och ändras.

En publicerad aldrig skissen i **Draft** läge visar en annan ikon på den **Skissdefinitioner** sidan än de som har **publicerad**. Den **senaste versionen** visas som **Draft** för dessa publicerade aldrig skisser.

Skapa och redigera en skiss med den [Azure-portalen](../create-blueprint-portal.md#create-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicera en skiss

När alla planerade ändringar har gjorts till en skiss i **Draft** läge, det kan vara **publicerad** och blir tillgängliga för tilldelning. Den **publicerad** version av skissen kan inte ändras.
En gång **publicerad**, skissen visas med en annan ikon än **Draft** skisser och visar det angivna versionsnumret i den **senaste versionen** kolumn.

Publicera en skiss med den [Azure-portalen](../create-blueprint-portal.md#publish-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Skapa och redigera en ny version av skissen

En **publicerad** version av en skiss kan inte ändras. Dock kan en ny version av skissen har lagts till i befintliga skissen och ändras efter behov. Göra ändringar i en befintlig skissen genom att redigera den. När de nya ändringarna sparas skissen har nu **opublicerade ändringar**. Dessa ändringar är en ny **Draft** version av skissen.

Redigera en skiss med den [Azure-portalen](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicera en ny version av skissen

Varje redigerade version av en skiss måste vara **publicerad** innan den kan tilldelas. När **opublicerade ändringar** har gjorts i en skiss men inte **publicerad**, **publicera skiss** knappen är tillgänglig på sidan Redigera skiss. Om knappen inte visas, skissen redan har **publicerad** och inte har någon **opublicerade ändringar**.

> [!NOTE]
> En enda skiss kan ha flera **publicerad** versioner som kan varje tilldelas prenumerationer.

Publicera en skiss med **opublicerade ändringar**, använder du samma steg för att publicera en ny skiss.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Tar bort en specifik version av skissen

Varje version av en skiss är ett unikt objekt och kan vara individuellt **publicerad**. Varje version av en skiss kan därför också tas bort. Tar bort en version av en skiss har inte någon inverkan på andra versioner av den skissen.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva uppgifter. Ta bort tilldelningar först och sedan ta bort den version som du vill ta bort.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **skiss definitioner** från sidan till vänster och Använd filteralternativ för att hitta skissen som du vill ta bort en version av. Klicka på det för att öppna sidan Redigera.

1. Klicka på den **publicerade versioner** fliken och leta upp den version som du vill ta bort.

1. Högerklicka på versionen för att ta bort och välj **ta bort den här versionen**.

## <a name="deleting-the-blueprint"></a>Tar bort skissen

Core-skissen kan också tas bort. Tar core skissen även bort eventuella skissen versioner av den skiss, inklusive både **Draft** och **publicerad** skisser. Som med att ta bort en version av en skiss, tar bort skissen core tas inte bort de befintliga tilldelningarna av skiss-versionerna.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva uppgifter. Ta bort tilldelningar först och sedan ta bort den version som du vill ta bort.

Ta bort en skiss med den [Azure-portalen](../create-blueprint-portal.md#delete-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Tilldelningar

Det finns flera punkter under livscykeln en skiss kan tilldelas till en prenumeration. När läget för en version av skissen är **publicerad**, och sedan på den här versionen kan tilldelas till en prenumeration. Den här livscykeln gör det möjligt för versioner av en skiss som ska användas och aktivt tilldelas när en nyare version utvecklas.

När versioner av skisser tilldelas, är det viktigt att förstå där de är tilldelade och med vilka parametrar som de har tilldelats. Parametrarna kan antingen vara statisk eller dynamisk. Mer information finns i [Statiska och dynamiska parametrar](parameters.md).

### <a name="updating-assignments"></a>Uppdatering av tilldelningar

När en skiss tilldelas kan tilldelningen uppdateras. Det finns flera orsaker till att uppdatera en befintlig tilldelning, inklusive:

- Lägg till eller ta bort [resource låsning](resource-locking.md)
- Ändra värdet för [dynamiska parametrar](parameters.md#dynamic-parameters)
- Uppgradera tilldelning till en nyare **publicerad** version av skissen

Läs hur genom att läsa [uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Nästa steg

- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).