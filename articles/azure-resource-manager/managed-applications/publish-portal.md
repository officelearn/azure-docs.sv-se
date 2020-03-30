---
title: Publicera hanterade appar via portalen
description: Visar hur du använder Azure-portalen för att skapa ett Azure-hanterat program som är avsett för medlemmar i din organisation.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651727"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicera ett tjänstkatalogprogram via Azure Portal

Du kan använda Azure-portalen för att publicera [hanterade program](overview.md) som är avsedda för medlemmar i din organisation. En IT-avdelning kan exempelvis publicera hanterade program som säkerställer efterlevnaden av organisationens standarder. Dessa hanterade program är tillgängliga via tjänstkatalogen, inte på Azure Marketplace.

## <a name="prerequisites"></a>Krav

När du publicerar ett hanterat program anger du en identitet för att hantera resurserna. Vi rekommenderar att du anger en Azure Active Directory-användargrupp. Information om hur du skapar en Azure Active Directory-användargrupp finns i [Skapa en grupp och lägga till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Zip-filen som innehåller den hanterade programdefinitionen måste vara tillgänglig via en URI. Vi rekommenderar att du överför ZIP-filen till en lagringsblob. 

## <a name="create-managed-application-with-portal"></a>Skapa hanterade program med portal

1. I det övre vänstra hörnet väljer du **+ Nytt**.

   ![Ny tjänst](./media/publish-portal/new.png)

1. Sök efter **servicekatalog**.

1. Bläddra tills du hittar **servicekatalogens hanterade programdefinition**. Välj den.

   ![Sök efter hanterade programdefinitioner](./media/publish-portal/select-managed-apps-definition.png)

1. Välj **Skapa** om du vill starta processen med att skapa den hanterade programdefinitionen.

   ![Skapa definierad av hanterade program](./media/publish-portal/create-definition.png)

1. Ange värden för namn, visningsnamn, beskrivning, plats, prenumeration och resursgrupp. För URI-paketfil anger du sökvägen till zip-filen som du skapade.

   ![Ange värden](./media/publish-portal/fill-application-values.png)

1. När du kommer till avsnittet Autentisering och låsnivå väljer du **Lägg till auktorisering**.

   ![Lägg till auktorisering](./media/publish-portal/add-authorization.png)

1. Välj en Azure Active Directory-grupp för att hantera resurserna och välj **OK**.

   ![Lägga till auktoriseringsgrupp](./media/publish-portal/add-auth-group.png)

1. När du har angett alla värden väljer du **Skapa**.

   ![Skapa hanterat program](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Exempel på hanterade program finns i [Exempel på projekt för Azure-hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.