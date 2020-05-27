---
title: Förhindra oavsiktlig borttagning – Azure-filresurser
description: Lär dig mer om mjuk borttagning för Azure-filresurser och hur du kan använda det för att återställa data och förhindra oavsiktlig borttagning.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883074"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Förhindra oavsiktlig borttagning av Azure-filresurser

Azure Storage har nu mjuk borttagning för fil resurser. Med mjuk borttagning kan du återställa data när de tas bort av misstag av ett program eller annan lagrings konto användare.

## <a name="how-soft-delete-works"></a>Så här fungerar mjuk borttagning

När det är aktiverat kan du använda mjuk borttagning för att spara och återställa dina fil resurser när de tas bort. När data tas bort övergår de till ett mjukt borttaget tillstånd i stället för att raderas permanent. Du kan konfigurera hur lång tid det tar för mjuka borttagna data att återställas innan den tas bort permanent.

Mjuk borttagning kan aktive ras på nya eller befintliga fil resurser. Mjuk borttagning är också bakåtkompatibelt, så du behöver inte göra några ändringar i dina program för att dra nytta av skyddet av mjuk borttagning. 

För mjuk borttagning av Premium-filresurser används fil resurs kvoten (den allokerade storleken på en fil resurs) i den totala kvot beräkningen för lagrings kontot tills den förfallna resursen upphör att gälla när resursen har tagits bort helt.

### <a name="availability"></a>Tillgänglighet

Mjuk borttagning för Azure-filresurser är tillgängligt på alla lagrings nivåer, alla typer av lagrings konton och i varje region som Azure Files är tillgänglig i.

## <a name="configuration-settings"></a>Konfigurationsinställningar

Mjuk borttagning för fil resurser är aktiverat på lagrings konto nivå, inställningarna för mjuk borttagning gäller alla fil resurser i ett lagrings konto. När du skapar ett nytt lagrings konto är mjuk borttagning inaktiverat som standard. Mjuk borttagning är också inaktiverat som standard för befintliga lagrings konton. Du kan aktivera och inaktivera mjuk borttagning när som helst.

Om du aktiverar mjuk borttagning för fil resurser tar du bort vissa fil resurser och inaktiverar sedan mjuk borttagning, om resurserna har sparats under den tiden kan du fortfarande komma åt och återställa dessa fil resurser. När du aktiverar mjuk borttagning måste du också konfigurera kvarhållningsperioden.

Kvarhållningsperioden anger hur lång tid som en mjuk borttagen fil resurs lagras och är tillgänglig för återställning. För fil resurser som tas bort uttryckligen startar kvarhållningsperioden för kvarhållningsperioden när data tas bort. För närvarande kan du hålla mjuk borttagna resurser i mellan 1 och 365 dagar.

Du kan när som helst ändra lagrings perioden för mjuk borttagning. En uppdaterad kvarhållningsperiod gäller bara för resurser som tagits bort efter att kvarhållningsperioden har uppdaterats. Resurser som tagits bort innan uppdaterings perioden upphör att gälla baseras på den kvarhållningsperiod som konfigurerades när data togs bort.

Om du vill ta bort en fil resurs permanent i läget för mjuk borttagning innan dess förfallo tid, måste du ta bort resursen, inaktivera mjuk borttagning och sedan ta bort resursen igen. Sedan bör du aktivera mjuk borttagning igen eftersom andra fil resurser på lagrings kontot kommer att vara sårbara för oavsiktlig borttagning medan mjuk borttagning är avstängd.

## <a name="pricing-and-billing"></a>Priser och fakturering

Både standard-och Premium-filresurser debiteras på den använda kapaciteten när den är mjuk borttagen, i stället för en etablerad kapacitet. Dessutom debiteras Premium-filresurser enligt ögonblicks bilds taxan i läget för mjuk borttagning. Standard fil resurser debiteras enligt normal taxa i läget för mjuk borttagning. Du debiteras inte för data som tas bort permanent efter den konfigurerade kvarhållningsperioden.

Mer information om priser för Azure File Storage i allmänhet finns på sidan med [priser för azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/).

När du först aktiverar mjuk borttagning rekommenderar vi att du använder en liten kvarhållningsperiod för att bättre förstå hur funktionen påverkar din faktura.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du aktiverar och använder mjuk borttagning fortsätter du med att [Aktivera mjuk borttagning](storage-files-enable-soft-delete.md)