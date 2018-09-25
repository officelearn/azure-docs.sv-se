---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 2902fe53e7f3ffb86dc727ebcdc2200ba02ac203
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47048457"
---
Om du får problem när du utför några åtgärder på delade bildgallerier, bild-definitioner och avbildningsversioner, kör du kommandot misslyckas igen i felsökningsläge. Felsökningsläge aktiveras genom att skicka den **-felsöka** växla med CLI och **-felsöka** växla med PowerShell. När du har hittat felet följer du det här dokumentet för att felsöka felen.


## <a name="unable-to-create-a-shared-image-gallery"></a>Det går inte att skapa en delad bildgalleri

Möjliga orsaker:

*Namn på galleriet är ogiltig.*

Tillåtna tecken för namn på galleriet är versaler, gemener, siffror, punkter och punkter. Namn på galleriet kan inte ha bindestreck i den. Ändra namn på galleriet och försök igen. 

*Namn på galleriet är inte unikt inom prenumerationen.*

Välj ett annat namn på galleriet och försök igen.


## <a name="unable-to-create-an-image-definition"></a>Det går inte att skapa en definition för avbildning 

Möjliga orsaker:

*Avbildningsnamnet för definitionen är ogiltig.*

Tillåtna tecken för avbildningsdefinitionen är versaler, gemener, siffror, punkter, bindestreck och punkter. Ändra definitionen avbildningens namn och försök igen.

*De obligatoriska egenskaperna för att skapa en avbildningsdefinitionen fylls inte.*

Egenskaper, till exempel namn, utgivare, erbjudande, sku och OS-typ är obligatoriska. Kontrollera om alla egenskaper har skickats.

Se till att den **OSType**, Linux eller Windows för avbildningsdefinitionen är densamma som källan hanterad avbildning som du använder för att skapa versionsnumret för avbildningen. 


## <a name="unable-to-create-an-image-version"></a>Det går inte att skapa en Bildversion 

Möjliga orsaker:

*Avbildningens versionsnamn är ogiltigt.*

Tillåtna tecken för Avbildningsversion är siffror och punkter. Måste vara ett nummer i intervallet för ett 32-bitars heltal. Format: *MajorVersion.MinorVersion.Patch*. Ändra namnet på avbildningen version och försök igen.

*Hanterade Källavbildningen varifrån versionsnumret för avbildningen skapas hittades inte.* 

Kontrollera om Källavbildningen finns och är i samma region som versionsnumret för avbildningen.

*Hanterad avbildning är inte klar håller på att etableras.*

Kontrollera tillståndet för etablering av hanterade Källavbildningen är **lyckades**.

*Källregionen stöds inte ännu.*

Använd tabellen nedan för att se om det finns stöd för den avsedda källregionen:
<br>

| Skapa galleriet i eller ”källregionen”   | Replikera rersion till eller ”målregion” |
|----------------------------------------|-------------------------------------------|
| Västra centrala USA                        | Alla regioner i Azures offentliga moln            |
| Södra centrala USA                       |                                           |
| USA, östra 2                              |                                           |
| Sydostasien                         |                                           |
| Västra Europa                            |                                           |

<br>

Region mållistan måste innehålla källregionen av versionsnumret för avbildningen. Kontrollera att du har inkluderat källregionen i listan över målregioner där du vill att Azure för att replikera dina Avbildningsversion till.

*Replikering till alla målregioner som inte slutförts.*

Använd den **--Expandera ReplicationStatus** flagga för att kontrollera om replikeringen till alla angivna målregioner har slutförts. Annars kan du vänta i upp till 6 timmar för jobbet är klart. Om det misslyckas kör du kommandot igen för att skapa och replikera versionsnumret för avbildningen. Om det finns en massa målregioner versionsnumret för avbildningen replikeras till, Tänk replikering i faser.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Det går inte att skapa en virtuell dator eller en skalningsuppsättning 

Möjliga orsaker:

*Användaren som försöker skapa en skalningsuppsättning för virtuell dator eller virtuell dator har inte läsbehörighet till versionsnumret för avbildningen.*

Kontakta Prenumerationens ägare och be dem att ge läsbehörighet till versionsnumret för avbildningen eller överordnat resurser (till exempel delade bildgalleriet eller avbildningsdefinitionen) via [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*Versionsnumret för avbildningen hittades inte.*

Kontrollera att den region som du försöker skapa en virtuell dator eller virtuell dator skala i ska tas med i listan över målregioner av versionsnumret för avbildningen. Om regionen är redan i listan över målregioner, kontrollerar du om replikeringen har slutförts. Du kan använda den **- ReplicationStatus** flagga för att kontrollera om replikeringen till alla angivna målregioner har slutförts. 

*Virtuell dator eller VM scale Sets skapa tar lång tid.*

Kontrollera att den **OSType** bildens har version som du vill skapa den virtuella datorn eller VM-skalningsuppsättningen från samma **OSType** källans hanterad avbildning som du använde för att skapa versionsnumret för avbildningen. 

## <a name="unable-to-share-resources"></a>Det går inte att dela resurser

Delning av delade bildgalleriet avbildningsdefinitionen och version bildresurser mellan prenumerationer är aktiverat med hjälp av [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>Replikeringen går långsamt

Använd den **--Expandera ReplicationStatus** flagga för att kontrollera om replikeringen till alla angivna målregioner har slutförts. Annars kan du vänta i upp till 6 timmar för jobbet är klart. Om den inte utlösa kommandot igen för att skapa och replikera versionsnumret för avbildningen. Om det finns en massa målregioner versionsnumret för avbildningen replikeras till, Tänk replikering i faser.

## <a name="azure-limits-and-quotas"></a>Azure-gränser och kvoter 

[Azure-gränser och kvoter](https://docs.microsoft.com/azure/azure-subscription-service-limits) gäller för alla delade bildgalleriet avbildningsdefinitionen och bildresurser version. Kontrollera att du är inom gränserna för dina prenumerationer. 



