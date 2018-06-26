---
title: MySQL som värd för servrar på Azure-stacken | Microsoft Docs
description: Hur du lägger till MySQL-instanser för etablering via Resursprovidern MySQL nätverkskort
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938453"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Lägg till värdservrar för MySQL-resursprovidern

Du kan vara värd för en MySQL-instans på en virtuell dator (VM) i [Azure Stack](azure-stack-poc.md), eller på en virtuell dator utanför Azure-stacken miljö, så länge resursprovidern MySQL kan ansluta till instansen.

## <a name="connect-to-a-mysql-hosting-server"></a>Ansluta till en värd MySQL-server

Kontrollera att du har autentiseringsuppgifter för ett konto med systemadministratörsprivilegier. Följ dessa steg om du vill lägga till en värd server:

1. Logga in på Azure-stacken operatorn portalen som en tjänstadministratör
2. Välj **fler tjänster**.
3. Välj **administrativa resurser** > **MySQL som värd för servrar** > **+ Lägg till**. Då öppnas den **lägga till en värd MySQL-Server** dialogrutan som visas i följande skärmbild.

   ![Konfigurera en server som värd](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Ange anslutningsinformation för MySQL-Server-instansen.

   * För **MySQL värd servernamn**, ange det fullständigt kvalificerade domännamnet (FQDN) eller en giltig IPv4-adress. Använd inte det korta namnet för virtuell dator.
   * En standardinstans MySQL inte anges, så du måste ange den **storlek för värd för Server i GB**. Ange en storlek som ligger nära kapacitet för databasservern.
   * Behåll standardinställningen för **prenumeration**.
   * För **resursgruppen**, skapa en ny eller Använd en befintlig grupp.

   > [!NOTE]
   > Om MySQL-instans kan användas av klienten och Azure Resource Manager-administratören, kan du placera den under kontroll av resursprovidern. Men MySQL-instansen **måste** tilldelas enbart resursprovidern.

5. Välj **SKU: er** att öppna den **skapa SKU** dialogrutan.

   ![Skapa en MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU: N **namn** bör återspegla egenskaperna för SKU: N så att användarna kan distribuera sina databaser till lämplig SKU: N.

   >[!IMPORTANT]
   >Specialtecken, inklusive blanksteg och punkter, stöds inte i **namn** eller **nivå** när du skapar en SKU för MySQL-resursprovidern.

6. Välj **OK** att skapa SKU: N.
7. Under **lägga till en värd MySQL-Server**väljer **skapa**.

När du lägger till servrar, kan du tilldela dem till en ny eller befintlig SKU för att särskilja Tjänsterbjudanden. Du kan till exempel ha en MySQL enterprise-instans som ger ökad databasen och automatisk säkerhetskopiering. Du kan reservera högpresterande servern för olika avdelningar i organisationen.

## <a name="increase-backend-database-capacity"></a>Öka kapaciteten för backend-databas

Du kan öka kapaciteten för backend-databas genom att distribuera flera MySQL-servrar i Azure Stack-portalen. Lägg till dessa servrar till en ny eller befintlig SKU. Om du lägger till en server till en befintlig SKU se till att de server-egenskaperna är samma som de andra servrarna i SKU: N.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Göra MySQL database-servrar som är tillgängliga för användarna

Skapa planer och erbjudanden om du vill göra MySQL database-servrar som är tillgängliga för användare. Lägg till tjänsten Microsoft.MySqlAdapter planen, och Lägg till standard kvot eller skapa en ny kvot.

![Skapa planer och erbjudanden för databaser](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>Nästa steg

[Skapa en MySQL-databas](azure-stack-mysql-resource-provider-databases.md)