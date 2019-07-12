---
title: Felsöka Azure förhandsgranskning för resursen
description: Lär dig hur du felsöker problem med förhandsversionen av Azure Data resurs
services: data-share
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: c02f72d6a327c4dcb94ac8844005613cfe316986
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838385"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Felsöka vanliga problem i förhandsversionen av Azure Data resurs

Den här artikeln visar hur du felsöker vanliga problem för förhandsversion av Azure Data resurs. 

## <a name="azure-data-share-invitations"></a>Azure-Dataresursen inbjudningar 

I vissa fall, när en ny användare klickar på **acceptera inbjudan** från e-postinbjudan skickades, kan de presenteras med en tom lista över inbjudningar. 

![Inga inbjudningar](media/no-invites.png)

Ovanstående fel är ett känt problem med tjänsten och är för närvarande håller på att åtgärdas. Som en lösning kan du följa den stegen nedan. 

1. I Azure-portalen går du till **prenumerationer**
1. Välj den prenumeration som du använder för Azure Data filresurs
1. Klicka på **Resursprovidrar**
1. Sök efter Microsoft.DataShare
1. Klicka på **registrera**

Du måste ha den [Azure deltagare RBAC-roll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) att slutföra dessa steg. 

Om du fortfarande inte kan se en data dela inbjudan, kontakta dataleverantören och se till att de har skickat inbjudan till din Azure-inloggning e-postadress och *inte* e-postalias. 

> [!IMPORTANT]
> Om du har redan accepterat inbjudan Azure Dataresurs och avslutades tjänsten innan du konfigurerar lagringsutrymme, följ instruktionerna i den [konfigurerar du mappning av en datauppsättning](how-to-configure-mapping.md) instruktionsguide och lär dig att slutföra konfigurationen av din mottagna data dela och börja ta emot data. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Fel när du skapar eller tar emot en ny resurs för Data

”Fel: Åtgärden returnerade en ogiltig statuskod 'BadRequest' ”

”Fel: AuthorizationFailed ”

”Fel: rolltilldelning till storage-konto”

![Behörighet fel](media/error-write-privilege.png)

Om du får något av ovanstående fel när du skapar en ny resurs för data eller tar emot en ny resurs för data, är det eftersom det finns inte tillräckligt med behörigheter till storage-kontot. Behörigheten som krävs är *Microsoft.Authorization/role tilldelningar/skrivning*, som finns i rollen ägare för lagring eller kan tilldelas till en anpassad roll. Även om du har skapat lagringskontot kan gör den inte automatiskt du ägaren av storage-konto. Följ stegen nedan för att bevilja dig själv ägaren av storage-konto. Alternativt kan du skapa en anpassad roll med den här behörigheten som du kan lägga till dig själv i.  

1. Gå till Storage-konto i Azure-portalen
1. Välj **åtkomstkontroll (IAM)**
1. Klicka på **Lägg till**
1. Lägg till dig själv i som ägare.

## <a name="next-steps"></a>Nästa steg

Information om hur du kan börja dela data, fortsätter du till den [dela dina data](share-your-data.md) självstudien.

