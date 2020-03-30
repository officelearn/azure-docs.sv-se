---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 40ba5a935e78cd75c4fcd7729e44f1cdf6c2859b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772839"
---
Om du får problem när du utför åtgärder i delade bildgallerier, eller för bilddefinitioner och bildversioner, kan du köra det misslyckade kommandot igen i felsökningsläge. Felsökningsläget aktiveras genom att skicka **-debug-växeln** med CLI och **-Debug-växeln** med PowerShell. När du har hittat felet följer du det här dokumentet för att felsöka felen.


## <a name="unable-to-create-a-shared-image-gallery"></a>Det går inte att skapa ett delat bildgalleri

Möjliga orsaker:

*Gallerinamnet är ogiltigt.*

Tillåtna tecken för Gallerinamn är versaler eller gemener, siffror, punkter och punkter. Gallerinamnet får inte innehålla streck. Ändra galleriets namn och försök igen. 

*Gallerinamnet är inte unikt i din prenumeration.*

Välj ett annat gallerinamn och försök igen.


## <a name="unable-to-create-an-image-definition"></a>Det går inte att skapa en bilddefinition 

Möjliga orsaker:

*bilddefinitionsnamnet är ogiltigt.*

Tillåtna tecken för bilddefinition är versaler eller gemener, siffror, punkter, streck och punkter. Ändra bilddefinitionsnamnet och försök igen.

*De obligatoriska egenskaperna för att skapa en bilddefinition fylls inte i.*

Egenskaper som namn, utgivare, erbjudande, sku och OS-typ är obligatoriska. Kontrollera om alla egenskaper skickas.

Kontrollera att **OSType**, antingen Linux eller Windows, av avbildningsdefinitionen är samma som den källhanterade avbildningen som du använder för att skapa avbildningsversionen. 


## <a name="unable-to-create-an-image-version"></a>Det går inte att skapa en bildversion 

Möjliga orsaker:

*Bildversionsnamnet är ogiltigt.*

Tillåtna tecken för bildversion är siffror och punkter. Talen måste ligga inom intervallet för ett 32-bitars heltal. Format: *MajorVersion.MinorVersion.Patch*. Ändra bildversionsnamnet och försök igen.

*Källhanterad bild från vilken bildversionen skapas hittades inte.* 

Kontrollera om källbilden finns och är i samma område som avbildningsversionen.

*Den hanterade avbildningen görs inte.*

Kontrollera att etableringstillståndet för den hanterade avbildningen för källan **har slutförts**.

*Målområdeslistan innehåller inte källregionen.*

Målområdeslistan måste innehålla källregionen för bildversionen. Se till att du har inkluderat källregionen i listan över målområden där du vill att Azure ska replikera avbildningsversionen till.

*Replikering till alla målregioner har inte slutförts.*

Använd flaggan **--expand ReplicationStatus** för att kontrollera om replikeringen till alla angivna målområden har slutförts. Om inte, vänta upp till 6 timmar för jobbet att slutföra. Om det misslyckas kör du kommandot igen för att skapa och replikera avbildningsversionen. Om det finns många målområden som avbildningsversionen replikeras till bör du överväga att göra replikeringen i faser.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Det går inte att skapa en virtuell dator eller en skalningsuppsättning 

Möjliga orsaker:

*Användaren som försöker skapa en vm- eller virtuell datorskalauppsättning har inte läsbehörighet till avbildningsversionen.*

Kontakta prenumerationsägaren och be dem att ge läsbehörighet till bildversionen eller de överordnade resurserna (som det delade bildgalleriet eller bilddefinitionen) via [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*Det gick inte att hitta bildversionen.*

Kontrollera att den region som du försöker skapa en virtuell dator eller skala för virtuella datorer i ingår i listan över målområden i avbildningsversionen. Om regionen redan finns i listan över målområden kontrollerar du om replikeringsjobbet har slutförts. Du kan använda flaggan **-ReplicationStatus** för att kontrollera om replikeringen till alla angivna målområden har slutförts. 

*Det tar lång tid att skapa den virtuella datorn eller den virtuella datorns skaluppsättning.*

Kontrollera att **OSType** för den avbildningsversion som du försöker skapa den virtuella datorn eller skalningsuppsättningen för virtuella datorer från har samma **OSType** för källhanterad avbildning som du använde för att skapa avbildningsversionen. 

## <a name="unable-to-share-resources"></a>Det går inte att dela resurser

Delning av delade bildgalleri, bilddefinition och bildversionsresurser mellan prenumerationer är aktiverat med hjälp av [RBAC (Role-Based Access Control).](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) 

## <a name="replication-is-slow"></a>Replikeringen är långsam

Använd flaggan **--expand ReplicationStatus** för att kontrollera om replikeringen till alla angivna målområden har slutförts. Om inte, vänta i upp till 6 timmar för jobbet att slutföra. Om det misslyckas, utlösa kommandot igen för att skapa och replikera avbildningsversionen. Om det finns många målområden som avbildningsversionen replikeras till bör du överväga att göra replikeringen i faser.

## <a name="azure-limits-and-quotas"></a>Begränsningar och kvoter för Azure 

[Azure-gränser och kvoter](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) gäller för alla delade bildgalleri, bilddefinition och avbildningsversionsresurser. Kontrollera att du är inom gränserna för dina prenumerationer. 



