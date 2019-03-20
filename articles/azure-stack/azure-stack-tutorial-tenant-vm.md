---
title: Tillgängliggör virtuella datorer för Azure Stack-användare | Microsoft Docs
description: Lär dig att tillhandahålla virtuella datorer på Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: 34ef200ff96b098969c74937934d6178cfaf81ea
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117140"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Självstudie: tillgängliggör virtuella datorer för Azure Stack-användare

Du kan skapa erbjudanden som dina användare (kallas ibland klienter) kan prenumerera som administratör för Azure Stack-molnet. Genom att prenumerera på ett erbjudande, kan användare använda de Azure Stack-tjänster som tillhandahåller ett erbjudande.

Den här självstudiekursen visar hur du skapar ett erbjudande för en virtuell dator och sedan logga in som en användare att testa erbjudandet.

Vad du lära dig:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Lägga till en bild
> * Testa erbjudandet

Tjänster som levereras till användare med prenumerationer, erbjudanden och planer i Azure Stack. Användare kan prenumerera på flera erbjudanden. Ett erbjudande kan ha en eller flera planer och en plan kan ha en eller flera tjänster.

![Prenumerationer, erbjudanden och planer](media/azure-stack-key-features/image4.png)

Mer information finns i [viktiga funktioner och koncept i Azure Stack](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Skapa ett erbjudande

Erbjudanden är grupper med en eller flera planer som leverantörer presenterar för användarna att köpa eller prenumerera på. Processen för att skapa ett erbjudande har flera steg. Först uppmanas du att skapa erbjudandet, och sedan en plan och slutligen kvoter.

1. [Logga in](azure-stack-connect-azure-stack.md) på portalen som en administratör för moln och sedan väljer **+ skapa en resurs** > **erbjuder + planer** > **erbjuder**.

   ![Nytt erbjudande](media/azure-stack-tutorial-tenant-vm/image01.png)

1. I **nytt erbjudande**, ange en **visningsnamn** och **resursnamn**, och sedan väljer du en ny eller befintlig **resursgrupp**. Visningsnamnet är erbjudandets egna namn. Endast molnoperator kan se resursnamnet som det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

   ![Visningsnamn](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Välj **Basplaner**, och i den **Plan** väljer **Lägg till** att lägga till en ny plan till erbjudandet.

   ![Lägg till en plan](media/azure-stack-tutorial-tenant-vm/image03.png)

1. I den **ny plan** avsnittet, Fyll i **visningsnamn** och **resursnamn**. Visningsnamnet är planens eget namn som användarna ser. Endast molnoperator kan se resursnamnet som det namn som molnoperatörer använder för att arbeta med planen som en Azure Resource Manager-resurs.

   ![Planera visningsnamn](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Välj **Services**. Från listan över tjänster, Välj **Microsoft.Compute**, **Microsoft.Network**, och **Microsoft.Storage**. Välj **Välj** att lägga till dessa tjänster i planen.

   ![Planera services](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Välj **kvoter**, och välj sedan den första tjänsten som du vill skapa en kvot för. Använd följande exempel för en IaaS-kvot som en vägledning för att konfigurera kvoter för Compute, Network och Storage-tjänster.

   - Skapa först en kvot för tjänst för beräkning. Välj i listan namnområde **Microsoft.Compute** och välj sedan **Skapa ny kvot**.

     ![Skapa ny kvot](media/azure-stack-tutorial-tenant-vm/image06.png)

   - I **Skapa kvot**, ange ett namn för kvoten. Du kan ändra eller acceptera kvot-värden som visas för kvoten som du skapar. I det här exemplet vi accepterar du standardinställningarna och välj **OK**.

     ![Kvotnamn](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Välj **Microsoft.Compute** i namnområdet listan och välj sedan den kvot som du skapade. Detta länkar kvoten till tjänst för beräkning.

     ![Välj kvot](media/azure-stack-tutorial-tenant-vm/image08.png)

      Upprepa dessa steg för Nätverks- och -tjänster. När du är klar väljer du **OK** i **kvoter** att spara alla kvoter.

1. I **ny plan**väljer **OK**.

1. Under **Plan**, Välj den nya planen och sedan **Välj**.

1. I **nytt erbjudande**väljer **skapa**. Du ser ett meddelande när erbjudandet har skapats.

1. Välj på menyn instrumentpanelen **erbjuder** och sedan välja erbjudandet som du skapade.

1. Välj **ändra tillstånd**, och sedan väljer **offentliga**.

    ![Offentliga tillstånd](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Lägga till en bild

Innan du kan distribuera virtuella datorer, måste du lägga till en avbildning i Azure Stack marketplace. Du kan lägga till avbildningen föredrar, inklusive Linux-avbildningar från Azure Marketplace.

Om du arbetar i ett scenario med anslutna och om du har registrerat din instans av Azure Stack med Azure kan du ladda ned Windows Server 2016 VM-avbildning från Azure Marketplace med hjälp av stegen som beskrivs i den [Download marketplace objekt från Azure till Azure Stack](azure-stack-download-azure-marketplace-item.md) avsnittet.

Information om att lägga till olika objekt i Marketplace finns i [The Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testa erbjudandet

Nu när du har skapat ett erbjudande kan testa du den. Du ska logga in som en användare prenumererar på erbjudandet och Lägg sedan till en virtuell dator.

1. **Prenumerera på ett erbjudande**

   a. Logga in på användarportalen med ett användarkonto och välj den **skaffa en prenumeration** panelen.
   - För ett integrerat system URL: en varierar beroende på region och externa domännamn för din operatör och ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Om du använder Azure Stack Development Kit är Portaladress https://portal.local.azurestack.external.

   ![Skaffa en prenumeration](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. I **skaffa en prenumeration**, ange ett namn för prenumerationen i den **visningsnamn** fält. Välj **erbjuder**, och väljer sedan en av dina erbjudanden i den **Välj ett erbjudande** lista. Välj **Skapa**.

   ![Skapa ett erbjudande](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Om du vill visa prenumerationen, Välj **alla tjänster**, och sedan under den **Allmänt** kategori väljer **prenumerationer**. Välj din nya prenumeration för att se vilka tjänster som ingår i prenumerationen.

   >[!NOTE]
   >När du prenumererar på ett erbjudande kan du behöva uppdatera portalen för att se vilka tjänster som en del av den nya prenumerationen.

1. **Etablera en virtuell dator**

   Du kan etablera en virtuell dator med den nya prenumerationen från användarportalen.

   a. Logga in på användarportalen med ett användarkonto.
   - För ett integrerat system URL: en varierar beroende på region och externa domännamn för din operatör och ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
     - Om du använder Azure Stack Development Kit är Portaladress https://portal.local.azurestack.external.

   b.  På instrumentpanelen, väljer **+ skapa en resurs** > **Compute** > **Windows Server 2016 Datacenter utvärdering**, och välj sedan **Skapa**.

   c. I **grunderna**, ange följande information:
      - Ange en **namn**
      - Ange en **användarnamn**
      - Ange en **lösenord**
      - Välj en **prenumeration**
      - Skapa en **resursgrupp** (eller välj en befintlig.) 
      - Välj **OK** att spara den här informationen.

   d. I **väljer du en storlek**väljer **Standard A1**, och sedan **Välj**.  

   e. I **inställningar**väljer **virtuellt nätverk**.

   f. I **Välj ett virtuellt nätverk**väljer **Skapa ny**.

   g. I **skapa virtuellt nätverk**, godkänner du standardvärdena och välj **OK**.

   h. Välj **OK** i **inställningar** att spara nätverkskonfigurationen.

      i. I **sammanfattning**väljer **OK** att skapa den virtuella datorn.  

   j. Om du vill se den nya virtuella datorn, **alla resurser**. Sök efter den virtuella datorn och välja det från sökresultaten.

   
## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Lägga till en bild
> * Testa erbjudandet

Fortsätt till nästa självstudie och lär dig hur du:
> [!div class="nextstepaction"]
> [Tillgängliggöra SQL-databaser till Azure Stack-användare](azure-stack-tutorial-sql-server.md)