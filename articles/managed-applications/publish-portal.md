---
title: Publicera en Azure hanterat program via portalen | Microsoft Docs
description: Visar hur du använder Azure-portalen för att skapa Azure-hanterat program som är avsett för medlemmar i din organisation.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: e52acd8587203c4729ac2bcd6e4bbc09620ead86
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35964004"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicera ett tjänstkatalogprogram via Azure-portalen

Du kan använda Azure-portalen för att publicera [hanterade program](overview.md) som är avsedda för medlemmar i din organisation. En IT-avdelning kan exempelvis publicera hanterade program som säkerställer efterlevnaden av organisationens standarder. Dessa hanterade program är tillgängliga via tjänstkatalogen, inte på Azure Marketplace.

## <a name="prerequisites"></a>Förutsättningar

När du publicerar ett hanterat program, kan du ange en identitet för att hantera resurserna. Vi rekommenderar att du anger en Azure Active Directory-användargrupp. Om du vill skapa en Azure Active Directory-användargrupp [skapar en grupp och Lägg till medlemmar i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

ZIP-filen som innehåller definitionen för hanterade programmet måste vara tillgänglig via en URI. Vi rekommenderar att du ladda upp ZIP-filen till en storage-blob. 

## <a name="create-managed-application-with-portal"></a>Skapa hanterat program med portalen

1. I det övre vänstra hörnet, väljer **+ ny**.

   ![Ny tjänst](./media/publish-portal/new.png)

1. Sök efter **tjänstkatalogen**.

1. Bläddra tills du hittar i resultatet **hanterade program Tjänstkatalogdefinition**. Markera den.

   ![Sök efter definitioner för hanterade program](./media/publish-portal/select-managed-apps-definition.png)

1. Välj **skapa** att starta processen med att skapa definitionen för hanterade programmet.

   ![Skapa definition för hanterade program](./media/publish-portal/create-definition.png)

1. Ange värden för namn, namn, beskrivning, plats, prenumeration och resursgrupp. Ange sökvägen till zip-filen som du skapade för paketfil URI.

   ![Ange värden](./media/publish-portal/fill-application-values.png)

1. När du kommer till avsnittet autentisering och låsnivå, välja **Lägg till auktorisering**.

   ![Lägg till auktorisering](./media/publish-portal/add-authorization.png)

1. Välj en Azure Active Directory-grupp som ska hantera resurser och välj **OK**.

   ![Lägg till grupp för auktorisering](./media/publish-portal/add-auth-group.png)

1. När du har angett alla värden, väljer **skapa**.

   ![Skapa hanterat program](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Hanterat program exempel finns i [exempelprojekten för Azure hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.