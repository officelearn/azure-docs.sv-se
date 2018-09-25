---
title: Förstå livscykeln för en Azure-skiss
description: Läs om livscykeln som passerar en skiss och information om varje steg.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991552"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Förstå livscykeln för en Azure-skiss

Precis som många resurser i Azure har en skiss i Azure skisser typisk och naturlig livscykelhantering. De är skapade, distribueras och slutligen bort när inte längre behövs eller relevanta.
Modeller stöder traditionella CRUD (skapa/läsa/Uppdatera/ta bort) livscykel-åtgärder, men också bygger på dem för att ge extra kontrollnivåer status som har stöd för vanliga kontinuerlig integrering / kontinuerlig distribution (CI/CD) pipelines för användning av de hantera sin infrastruktur med hjälp av kod – en nyckelfaktor i DevOps kallas infrastruktur som kod (IaC).

För att helt förstå en skiss och stegen kan går vi igenom en standard livscykel:

> [!div class="checklist"]
> - Skapa och redigera en skiss
> - Publicera skissen
> - Skapa och redigera en ny version av skissen
> - Publicera en ny version av skissen
> - Tar bort en specifik version av skissen
> - Tar bort skissen

## <a name="creating-and-editing-a-blueprint"></a>Skapa och redigera en skiss

När du skapar en skiss, Lägg till artefakter, spara till en hanteringsgrupp och tillhandahålls ett unikt namn och en unik version. Nu skissen är i en **Draft** läge och kan ännu inte tilldelas. Men även i den **Draft** läge kan fortsätta att uppdateras och ändras.

En skiss i **Draft** läge som aldrig har publicerats visas en annan ikon på den **Skissdefinitioner** sidan än de som har **publicerad**. Den **senaste versionen** visas också som **Draft** för dessa publicerade aldrig skisser.

Skapa och redigera en skiss med den [Azure-portalen](../create-blueprint-portal.md#create-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicera en skiss

När alla önskade ändringar har gjorts till en skiss i **Draft** läge, det kan vara **publicerad** och blir tillgängliga för tilldelning. Den **publicerad** version av skissen kan inte ändras.
En gång **publicerad**, skissen visas med en annan ikon än **Draft** skisser och visar det angivna versionsnumret i den **senaste versionen** kolumn.

Publicera en skiss med den [Azure-portalen](../create-blueprint-portal.md#publish-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Skapa och redigera en ny version av skissen

Även om en **publicerad** version av en skiss kan inte ändras, en ny version av skissen kan läggas till i befintliga skissen och ändras efter behov. Detta görs genom att ändra en befintlig skissen. Om skissen var redan **publicerad**när ändringarna sparas kommer de visas som **opublicerade ändringar** i listan över skissdefinitioner. Sparar ändringar sparar en **Draft** version av skissen.

Redigera en skiss med den [Azure-portalen](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicera en ny version av skissen

Precis som den första versionen av en skiss har **publicerad** om du vill tilldela den varje efterföljande version av den samma skissen måste vara **publicerad** innan den kan tilldelas. När **opublicerade ändringar** har gjorts i en skiss, men de har inte ännu har **publicerad**, **publicera skiss** knappen är tillgänglig på sidan Redigera skiss. Om knappen inte visas, skissen redan har **publicerad**, men inte har någon **opublicerade ändringar**.

> [!NOTE]
> En enda skiss kan ha flera **publicerad** versioner som kan varje tilldelas prenumerationer.

Stegen för att publicera en skiss med **opublicerade ändringar** är samma som stegen för att publicera en ny skiss som en ny version av en befintlig skissen.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Tar bort en specifik version av skissen

Varje version av en skiss är ett unikt objekt och kan vara individuellt **publicerad**. Det innebär att varje version av en skiss kan tas bort. Tar bort en version av en skiss har inte någon inverkan på andra versioner av den skissen.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva uppgifter. Ta bort tilldelningar först och sedan ta bort den version som du vill ta bort.

1. Starta tjänsten Azure skisser i Azure portal genom att klicka på **alla tjänster** och söka efter och välja **princip** i den vänstra rutan. På den **princip** klickar du på **skisser**.

1. Välj **Skissdefinitioner** från sidan till vänster och Använd filteralternativ för att hitta skissen som du vill ta bort en version av. Klicka på det för att öppna sidan Redigera.

1. Klicka på den **publicerade versioner** fliken och leta upp den version som du vill ta bort.

1. Högerklicka på versionen för att ta bort och välj **ta bort den här versionen**.

## <a name="deleting-the-blueprint"></a>Tar bort skissen

Core-skissen kan också tas bort. Tar bort skissen core raderas även eventuella skissen versioner av den skissen, oavsett **Draft** eller **publicerad** status. Som med att ta bort en version av en skiss, tar bort skissen core tas inte bort de befintliga tilldelningarna av skiss-versionerna.

> [!NOTE]
> Det går inte att ta bort en skiss som har aktiva uppgifter. Ta bort tilldelningar först och sedan ta bort den version som du vill ta bort.

Ta bort en skiss med den [Azure-portalen](../create-blueprint-portal.md#delete-a-blueprint) eller [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Tilldelningar

Det finns flera punkter under livscykeln som en skiss kan tilldelas till en prenumeration.
När läget för en version av skissen finns **publicerad**, och sedan på den här versionen kan tilldelas till en prenumeration. Även om det finns en **Draft** version av skiss, om det finns en eller flera skissen versioner i en **publicerad** läge och sedan var och en av de **publicerad** versioner är tillgängliga för tilldelning. På så sätt kan versioner av en skiss som ska användas och aktivt tilldelas när en nyare version utvecklas.

När versioner av skisser tilldelas, är det viktigt att förstå där de är tilldelade och med vilka parametrar som de har tilldelats. Parametrarna kan antingen vara statisk eller dynamisk. Mer information finns i [Statiska och dynamiska parametrar](parameters.md).

### <a name="updating-assignments"></a>Uppdatering av tilldelningar

När en skiss tilldelas kan tilldelningen uppdateras. Det finns flera orsaker till att uppdatera en befintlig tilldelning, inklusive:

- Lägg till eller ta bort [resource låsning](resource-locking.md)
- Ändra värdet för [dynamiska parametrar](parameters.md#dynamic-parameters)
- Uppgradera tilldelning till en nyare **publicerad** version av skissen

Läs hur genom att läsa [uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Nästa steg

- Förstå hur du använder [Statiska och dynamiska parametrar](parameters.md)
- Lär dig att anpassa den [skiss ordningsföljd](sequencing-order.md)
- Ta reda på hur du får använda [skiss resource låsning](resource-locking.md)
- Lär dig hur du [uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)