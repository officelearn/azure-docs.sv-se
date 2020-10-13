---
title: Rollbaserad åtkomst kontroll-Custom Vision
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du konfigurerar rollbaserad åtkomst kontroll för dina Custom Vision-projekt.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 4eea87d7b5ef543551b789ddc6b045358fa98631
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826933"
---
# <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Custom Vision stöder rollbaserad åtkomst kontroll i Azure (Azure RBAC), ett auktoriserings system för att hantera individuell åtkomst till Azure-resurser. Med RBAC tilldelar du olika team medlemmar olika nivåer av behörigheter för dina Custom Vision-projekt. Mer information om RBAC finns i [Azure RBAC-dokumentationen](https://docs.microsoft.com/azure/role-based-access-control/).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Lägg till roll tilldelning i Custom Vision resurs

Azure RBAC kan tilldelas till en Custom Vision-resurs. Om du vill bevilja åtkomst till en Azure-resurs lägger du till en roll tilldelning.
1. I [Azure Portal](https://ms.portal.azure.com/)väljer du **alla tjänster**. 
1. Välj sedan **Cognitive Services**och navigera till din speciella Custom vision utbildnings resurs.
   > [!NOTE]
   > Du kan också konfigurera RBAC för hela resurs grupper, prenumerationer eller hanterings grupper. Det gör du genom att välja önskad omfattnings nivå och sedan navigera till önskat objekt (till exempel välja **resurs grupper** och sedan klicka igenom till önskad resurs grupp).
1. Välj **åtkomst kontroll (IAM)** i det vänstra navigerings fönstret.
1. Välj fliken **roll tilldelningar** för att Visa roll tilldelningarna för det här omfånget.
1. Välj **Lägg till**  ->  **Lägg till roll tilldelning**.
1. I list rutan **roll** väljer du den roll som du vill lägga till.
1. Välj en användare, grupp, tjänstens huvud namn eller hanterad identitet i listan **Välj** . Om du inte ser säkerhets objekt i listan kan du skriva in rutan Välj för att söka i katalogen efter visnings namn, e-postadresser och objekt identifierare.
1. Välj **Spara** för att tilldela rollen.

Inom några minuter tilldelas målet den valda rollen i det valda omfånget.

## <a name="custom-vision-role-types"></a>Custom Vision roll typer

Använd följande tabell för att fastställa åtkomst behoven för dina Custom Vision-resurser.

|Roll  |Behörigheter  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Fullständig åtkomst till projekten, inklusive möjligheten att skapa, redigera eller ta bort ett projekt.        |
|`Cognitive Services Custom Vision Trainer`     | Fullständig åtkomst, förutom möjligheten att skapa eller ta bort ett projekt. Lärare kan visa och redigera projekt och träna, publicera, avpublicera eller exportera modeller.        |
|`Cognitive Services Custom Vision Labeler`     | Möjlighet att ladda upp, redigera eller ta bort utbildnings avbildningar och skapa, lägga till, ta bort eller ta bort taggar. Etiketter kan visa projekt, men kan inte uppdatera något annat än träna bilder och taggar.         |
|`Cognitive Services Custom Vision Deployment`     | Möjlighet att publicera, avpublicera eller exportera modeller. Distribuerare kan visa projekt, men kan inte uppdatera ett projekt, träna bilder eller taggar.        |
|`Cognitive Services Custom Vision Reader`     | Möjlighet att visa projekt. Läsarna kan inte göra några ändringar.        |
