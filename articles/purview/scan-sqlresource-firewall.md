---
title: Genomsök Azure SQL DB bakom en brand vägg
description: Lär dig hur du genomsöker resurser bakom en brand vägg med hjälp av Azure avdelningens kontroll-portalen.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553813"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Genomsök Azure SQL DB bakom en brand vägg i Azure avdelningens kontroll

I den här artikeln får du lära dig hur du använder Azure-avdelningens kontroll för att skanna en resurs bakom en brand vägg.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Din egen [Azure Active Directory klient](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Konfigurera SQL-lagring bakom en brand vägg

Det första steget är att lägga till katalogen MSI i en Azure SQL DB via [registret och skanna en Azure SQL Database](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Genomsök SQL DB från avdelningens kontroll

1. Bläddra till start sidan för avdelningens kontroll.

1. Välj **hanterings Center** i det vänstra navigerings fältet.

1. Välj **data källor** under **källor och genomsökning**.

1. Välj **+ ny** för att lägga till data källan.

1. Välj **Azure SQL Database**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Skärm bild för att välja SQL Server.":::

1. Ange ett namn för den nya data källan, Välj **från Azure-prenumeration** och välj din prenumeration och Server namn i list rutan.

   Slutför registreringen genom att klicka på **Slutför** . 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Skärm bild för att slutföra markeringen":::

1. Välj **Konfigurera genomsökning** för lagringen som ligger bakom brand väggen och testa anslutningen. Anslutningen visar att den lyckades. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Skärm bild av valet t0 set-scanning.":::

1. Ställ in genomsöknings frekvensen och välj **Fortsätt**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Skärm bild av valet för att starta SQL-genomsökningen.":::

1.  Sökningen slutförs och statusen uppdateras i listan över data källor.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Skärm bild av meddelande sökningen slutförd":::
   
## <a name="next-steps"></a>Nästa steg

Sedan kan du konfigurera en skannings regel uppsättning [skapa en skannings regel uppsättning](create-a-scan-rule-set.md) för att gruppera skanningar tillsammans.
