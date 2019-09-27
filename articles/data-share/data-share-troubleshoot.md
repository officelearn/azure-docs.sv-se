---
title: Felsöka för hands version av Azure Data Share
description: Lär dig hur du felsöker problem med Azure Data repreview
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 62b386f4648074bbf1f002e162151253e5b43bc9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71320301"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Felsök vanliga problem i för hands versionen av Azure Data Share

Den här artikeln visar hur du felsöker vanliga problem för för hands versionen av Azure Data reshare. 

## <a name="azure-data-share-invitations"></a>Azure Data Share-inbjudningar 

I vissa fall, när en ny användare klickar på **acceptera inbjudan** från e-postinbjudan som skickades, kan de visas med en tom lista med inbjudningar. 

![Inga inbjudningar](media/no-invites.png)

Ovanstående fel är ett känt problem med tjänsten och håller på att åtgärdas. Som en lösning följer du stegen nedan. 

1. I Azure Portal navigerar du till **prenumerationer**
1. Välj den prenumeration som du använder för Azure Data Share
1. Klicka på **resurs leverantörer**
1. Sök efter Microsoft. DataShare
1. Klicka på **Registrera**

Du måste ha [rollen Azure CONTRIBUTOR RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) för att slutföra de här stegen. 

Om du fortfarande inte kan se en datadelnings-inbjudan kontaktar du din dataprovider och ser till att du har skickat inbjudan till din Azure-inloggnings-e-postadress och *inte* ditt e-postalias. 

> [!IMPORTANT]
> Om du redan har accepterat en Azure Data Share-inbjudan och avslutat tjänsten innan du konfigurerade lagringen, följer du anvisningarna i guiden [Konfigurera en data uppsättnings mappning](how-to-configure-mapping.md) för att lära dig hur du slutför konfigurationen av din mottagna data resurs och börja ta emot data. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Fel vid skapande eller mottagning av en ny data resurs

Fels Åtgärden returnerade en ogiltig status kod ' BadRequest ' "

Fels AuthorizationFailed"

"Fel: roll tilldelningen till lagrings kontot"

![Behörighets fel](media/error-write-privilege.png)

Om du får något av ovanstående felmeddelanden när du skapar en ny dataresurs eller när du tar emot en ny dataresurs, beror det på att det inte finns nödvändig behörighet till lagringskontot. Den behörighet som krävs är *Microsoft. auktorisering/roll tilldelningar/skrivning*, som finns i rollen lagrings ägare eller som kan tilldelas en anpassad roll. Även om du har skapat lagringskontot blir du inte automatiskt lagringskontots ägare. Följ stegen nedan för att göra dig själv till lagringskontots ägare. Du kan också skapa en anpassad roll med den här behörigheten som du kan lägga till själv i.  

1. Gå till Lagringskonto på Azure-portalen
1. Välj **åtkomst kontroll (IAM)**
1. Klicka på **Lägg till**
1. Lägg till dig själv i som ägare.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .

