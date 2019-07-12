---
title: Roller och krav för Azure förhandsgranskning för resursen
description: Roller och krav för Azure förhandsgranskning för resursen
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807523"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Roller och krav för Azure förhandsgranskning för resursen

Den här artikeln beskrivs de roller som krävs för att dela data med hjälp av förhandsversionen av Azure Data-delar, samt att acceptera och ta emot data med hjälp av Azure Data dela Preview. 

## <a name="roles-and-requirements"></a>Roller och krav

Om du vill dela eller ta emot data med hjälp av Azure Data delar, måste användarkontot som används för att logga in på Azure kunna bevilja dela Data till lagringskontot som du delning av data från eller tar emot data i att. Det här är vanligtvis en behörighet som finns i den **ägare** roll eller en anpassad roll med Microsoft.Authorization/role tilldelningar och skrivbehörighet tilldelad. 

Om du vill dela eller ta emot data från eller till ett Azure Storage-konto, måste du vara ägare av storage-konto. Även om du har skapat lagringskontot kan beviljar detta automatiskt du ägarskapet för lagringskontot. Följ dessa steg för att lägga till dig själv i rollen ägare i Azure Storage-kontot.

1. Gå till Storage-konto i Azure-portalen
1. Välj **åtkomstkontroll (IAM)**
1. Klicka på **Lägg till**
1. Lägg till dig själv i som ägare

Om du vill visa vilka behörigheter du har i prenumerationen öppnar du Azure-portalen, väljer användarnamnet i det övre högra hörnet och väljer sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. 

## <a name="resource-provider-registration"></a>Resursproviderregistrering 

När acceptera inbjudan Azure Dataresurs, måste du manuellt registrerar resursprovidern Microsoft.DataShare i din prenumeration. Följ stegen nedan för att registrera resursprovidern Microsoft.DataShare till din Azure-prenumeration. 

1. I Azure-portalen går du till **prenumerationer**
1. Välj den prenumeration som du använder för Azure Data filresurs
1. Klicka på **Resursprovidrar**
1. Sök efter Microsoft.DataShare
1. Klicka på **registrera**

## <a name="next-steps"></a>Nästa steg

- Läs mer om administratörsroller i Azure - [förstå rolldefinitioner](../role-based-access-control/role-definitions.md)

