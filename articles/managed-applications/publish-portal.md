---
title: Publicera ett Azure hanterat program via portalen | Microsoft Docs
description: Visar hur du använder Azure portal för att skapa en Azure hanterade program som är avsedd för medlemmar i din organisation.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 69d31a7199347574e8866b275ec17ba3997d80c2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicera ett tjänstprogram katalogen via Azure portal

Du kan använda Azure-portalen för att publicera [hanterade program](overview.md) som är avsedda för medlemmar i din organisation. Exempelvis kan en IT-avdelning publicera hanterade program som säkerställa efterlevnad med organisationens normer. Dessa hanterade program är tillgängliga via tjänstkatalog, inte på Azure marketplace.

## <a name="prerequisites"></a>Förutsättningar

När du publicerar ett hanterat program kan ange du en identitet för att hantera resurser. Vi rekommenderar att du anger en Azure Active Directory-användargrupp. Om du vill skapa ett Azure Active Directory-användargruppen finns [skapar en grupp och lägga till medlemmar i Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md). 

ZIP-filen som innehåller programdefinitionen av hanterade måste vara tillgänglig via en URI. Vi rekommenderar att du överför ZIP-filen till blob storage. 

## <a name="create-managed-application-with-portal"></a>Skapa hanterade program med portalen

1. I det övre vänstra hörnet väljer **+ ny**.

   ![Ny tjänst](./media/publish-portal/new.png)

1. Sök efter **tjänstkatalogen**.

1. Bläddra tills du hittar i sökresultaten **katalog hanterade program tjänstdefinitionen**. Markera den.

   ![Sök efter programdefinitioner för hanterade](./media/publish-portal/select-managed-apps-definition.png)

1. Välj **skapa** att starta processen med att skapa definition för hanterade program.

   ![Skapa definition för hanterade program](./media/publish-portal/create-definition.png)

1. Ange värden för namn, namn, beskrivning, plats, prenumeration och resursgrupp. Ange sökvägen till zip-filen som du skapade för paketfilen URI.

   ![Ange värden](./media/publish-portal/fill-application-values.png)

1. När du kommer till avsnittet autentisering och lås nivå, välja **lägga till tillståndet**.

   ![Lägg till auktorisering](./media/publish-portal/add-authorization.png)

1. Välj en Azure Active Directory-grupp som ska hantera resurser och välj **OK**.

   ![Lägg till grupp för auktorisering](./media/publish-portal/add-auth-group.png)

1. När du har angett alla värden, välja **skapa**.

   ![Skapa hanterat program](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Hanterat program exempel finns [exempelprojekten för Azure hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.