---
title: Felsöka Azure Data Share
description: Lär dig hur du felsöker problem med inbjudningar och fel när du skapar eller tar emot data resurser med Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490558"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Felsök vanliga problem i Azure Data Share 

Den här artikeln visar hur du felsöker vanliga problem med Azure Data Share. 

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

Fel: åtgärden returnerade en ogiltig status kod BadRequest

"Fel: AuthorizationFailed"

"Fel: roll tilldelningen till lagrings kontot"

![Behörighets fel](media/error-write-privilege.png)

Om du får något av ovanstående felmeddelanden när du skapar en ny dataresurs eller när du tar emot en ny dataresurs, beror det på att det inte finns nödvändig behörighet till lagringskontot. Den behörighet som krävs är *Microsoft. auktorisering/roll tilldelningar/skrivning*, som finns i rollen lagrings ägare eller som kan tilldelas en anpassad roll. Även om du har skapat lagringskontot blir du inte automatiskt lagringskontots ägare. Följ stegen nedan för att göra dig själv till lagringskontots ägare. Du kan också skapa en anpassad roll med den här behörigheten som du kan lägga till själv i.  

1. Gå till Lagringskonto på Azure-portalen
1. Välj **åtkomst kontroll (IAM)**
1. Klicka på **Lägg till**
1. Lägg till dig själv i som ägare.

## <a name="troubleshooting-sql-based-sharing"></a>Felsöka SQL-baserad delning

"Fel: x data uppsättningar lades inte till eftersom du inte har de behörigheter som krävs för att dela."

Om du får det här felet när du lägger till en data uppsättning från en SQL-baserad källa kan det bero på att du inte har skapat en användare för Azure Data Share-MSI på din SQL Server.  Lös problemet genom att köra följande skript:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Observera att *< share_acc_name >* är namnet på ditt data resurs konto. Om du inte har skapat ett data resurs konto ännu kan du komma tillbaka till det här kravet senare.         

Se till att du har följt alla krav som anges i [dela din data](share-your-data.md) kurs.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .

