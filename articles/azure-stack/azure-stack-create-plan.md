---
title: Skapa en plan i Azure-stacken | Microsoft Docs
description: "Skapa en plan som låter prenumeranter etablera virtuella datorer som en moln-administratör."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Skapa en plan i Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

[Planer](azure-stack-key-features.md) är grupperingar av en eller flera tjänster. Du kan skapa planer för att erbjuda användarna som en provider. I sin tur prenumerera användarna på dina erbjudanden använda planer och tjänster som de innehåller. Det här exemplet visar hur du skapar en plan som innehåller beräknings-, nätverks- och lagringsresurs-providers. Den här planen kan prenumeranter att etablera virtuella datorer.

1. Logga in på Azure Stack-administratörsportalen (https://adminportal.local.azurestack.external). Ange autentiseringsuppgifterna för kontot som du skapade i steg 5 i den [Kör PowerShell-skript](azure-stack-run-powershell-script.md) avsnitt.

2. Klicka för att skapa en plan och som användare kan prenumerera på **ny** > **klient erbjuder + planer** > **Plan**.

   ![](media/azure-stack-create-plan/image01.png)
3. I den **nya planera** bladet, Fyll i **visningsnamn** och **resursnamnet**. Visningsnamnet är planens eget namn som visas för användarna. Endast administratören kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med planen som en Azure Resource Manager-resurs.

   ![](media/azure-stack-create-plan/image02.png)
4. Skapa en ny **resursgruppen**, eller välj en befintlig som en behållare för planen.

   ![](media/azure-stack-create-plan/image02a.png)
5. Klicka på **Services**väljer **Microsoft.Compute**, **Microsoft.Network**, och **Microsoft.Storage**, och klicka sedan på **Välj**.

   ![](media/azure-stack-create-plan/image03.png)
6. Klicka på **kvoter**, klickar du på **Microsoft.Storage (lokal)**, och sedan välja standardkvoten som eller klicka på **skapa nya kvoten** att anpassa kvoten.

   ![](media/azure-stack-create-plan/image04.png)
7. Om du skapar en ny kvot, ange ett namn för kvoten > Ange kvotvärden för > klickar du på **OK** > Klicka på namnet på den nya kvoten.

   ![](media/azure-stack-create-plan/image06.png)
8. Klicka på **Microsoft.Network (lokal)**, och sedan välja standardkvoten som eller klicka på **skapa nya kvoten** att anpassa kvoten.

    ![](media/azure-stack-create-plan/image07.png)
9. Om du skapar en ny kvot, Skriv ett namn för kvoten > Ange kvotvärden för > klickar du på **OK** > Klicka på namnet på den nya kvoten.

    ![](media/azure-stack-create-plan/image08.png)
10. Klicka på **Microsoft.Compute (lokal)**, och sedan välja standardkvoten som eller klicka på **skapa nya kvoten** att anpassa kvoten.

    ![](media/azure-stack-create-plan/image09.png)
11. Om du skapar en ny kvot, Skriv ett namn för kvoten > Ange kvotvärden för > klickar du på **OK** > Klicka på namnet på den nya kvoten.

    ![](media/azure-stack-create-plan/image10.png)
12. I den **kvoter** bladet klickar du på **OK**, och klicka sedan på den **ny Plan** bladet, klickar du på **skapa** att skapa planen.

    ![](media/azure-stack-create-plan/image11.png)
13. Klicka för att se din nya plan **alla resurser**, Sök sedan efter planen och klickar på dess namn.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Nästa steg
[Skapa ett erbjudande](azure-stack-create-offer.md)
