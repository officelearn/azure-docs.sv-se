---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: c043cfb6cf437ab55cc4a48ced716798738adbef
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507188"
---
Om du får problem när du utför åtgärder i delade bildgallerier, eller för bilddefinitioner och bildversioner, kan du köra det misslyckade kommandot igen i felsökningsläge. Fel söknings läge aktive ras genom att skicka `--debug` växeln med CLI och `-Debug` växeln med PowerShell. När du har hittat felet följer du det här dokumentet för att felsöka felen.


## <a name="unable-to-create-a-shared-image-gallery"></a>Det går inte att skapa ett delat bildgalleri

Möjliga orsaker:

*Galleri namnet är ogiltigt.*

Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck. Ändra namnet på galleriet och försök igen. 

*Galleri namnet är inte unikt i din prenumeration.*

Välj ett annat namn på galleriet och försök igen.


## <a name="unable-to-create-an-image-definition"></a>Det går inte att skapa en bilddefinition 

Möjliga orsaker:

*bild definitions namnet är ogiltigt.*

Tillåtna tecken för bild definition är versaler eller gemener, siffror, punkter, streck och punkter. Ändra namnet på bild definitionen och försök igen.

*De obligatoriska egenskaperna för att skapa en avbildnings definition är inte ifyllda.*

Egenskaperna, till exempel namn, utgivare, erbjudande, SKU och OS-typ är obligatoriska. Kontrol lera om alla egenskaper har skickats.

Kontrol lera att **OSType**, antingen Linux eller Windows, för avbildnings definitionen är samma som den källa som du använder för att skapa avbildnings versionen. 


## <a name="unable-to-create-an-image-version"></a>Det går inte att skapa en bildversion 

Möjliga orsaker:

*Bild versionens namn är ogiltigt.*

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version. MinorVersion. patch*. Ändra avbildningens versions namn och försök igen.

*Käll hanterad avbildning från vilken avbildnings versionen skapas går inte att hitta.* 

Kontrol lera om käll avbildningen finns och är i samma region som avbildnings versionen.

*Den hanterade avbildningen har inte kon figurer ATS.*

Se till att etablerings statusen för den hanterade käll avbildningen har **slutförts**.

*Mål regions listan innehåller inte käll regionen.*

Mål regions listan måste innehålla bild versionens käll område. Se till att du har inkluderat käll regionen i listan över mål regioner där du vill att Azure ska replikera avbildnings versionen till.

*Replikeringen till alla mål regioner har inte slutförts.*

Använd flaggan **--Expand ReplicationStatus** för att kontrol lera om replikeringen till alla angivna mål regioner har slutförts. Om inte, vänta upp till 6 timmar tills jobbet har slutförts. Om det Miss lyckas kör du kommandot igen för att skapa och replikera avbildnings versionen. Om det finns många mål regioner som avbildnings versionen replikeras till, bör du utföra replikeringen i faser.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Det går inte att skapa en virtuell dator eller en skalningsuppsättning 

Möjliga orsaker:

*Användaren som försöker skapa en virtuell dator eller skalnings uppsättning för virtuell dator har inte Läs behörighet till avbildnings versionen.*

Kontakta Prenumerationens ägare och be dem att ge Läs behörighet till avbildnings versionen eller de överordnade resurserna (t. ex. galleriet för delad avbildning eller avbildnings definition) via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

*Det gick inte att hitta avbildnings versionen.*

Kontrol lera att den region som du försöker skapa en virtuell dator eller skalning av virtuella datorer i ingår i listan över mål regioner i avbildnings versionen. Om regionen redan finns i listan över mål regioner kontrollerar du om replikeringen har slutförts. Du kan använda flaggan **-ReplicationStatus** för att kontrol lera om replikeringen till alla angivna mål regioner har slutförts. 

*Skapandet av skalnings uppsättningen för virtuella datorer eller virtuella datorer tar lång tid.*

Kontrol lera att **OSType** för den avbildnings version som du försöker skapa den virtuella datorn eller den virtuella datorns skalnings uppsättning från har samma **OSType** som den källa som du använde för att skapa avbildnings versionen. 

## <a name="unable-to-share-resources"></a>Det går inte att dela resurser

Delning av delade avbildnings gallerier, avbildnings definitioner och avbildnings versions resurser över prenumerationer aktive ras med [rollbaserad Access Control](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>Replikeringen är långsam

Använd flaggan **--Expand ReplicationStatus** för att kontrol lera om replikeringen till alla angivna mål regioner har slutförts. Om inte, vänta i upp till 6 timmar tills jobbet har slutförts. Om det Miss lyckas utlöser du kommandot igen för att skapa och replikera avbildnings versionen. Om det finns många mål regioner som avbildnings versionen replikeras till, bör du utföra replikeringen i faser.

## <a name="azure-limits-and-quotas"></a>Begränsningar och kvoter för Azure 

[Azure-gränser och kvoter](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) gäller för alla delade avbildnings gallerier, avbildnings definitioner och avbildnings versions resurser. Se till att du är inom gränserna för dina prenumerationer. 
