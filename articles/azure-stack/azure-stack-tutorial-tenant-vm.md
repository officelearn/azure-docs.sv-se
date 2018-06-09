---
title: Tillhandahålla virtuella datorer till Azure-stacken användarna | Microsoft Docs
description: Lär dig att tillhandahålla virtuella datorer på Azure-stacken
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248167"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Självstudier: tillgängliggöra virtuella datorer till Azure Stack-användare

Du kan skapa erbjudanden som användarna (kallas ibland klienter) kan prenumerera på som administratör Stack för Azure-molnet. Genom att prenumerera på ett erbjudande, kan användare använda Azure Stack-tjänster som ger ett erbjudande.

Den här kursen visar hur du skapar ett erbjudande om en virtuell dator och sedan logga in som en användare att testa erbjudandet.

Vad får du lära dig:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Lägga till en bild
> * Testa erbjudandet

Tjänster som levereras till användare med hjälp av prenumerationer, erbjudanden och planer i Azure-stacken. Användare kan prenumerera på flera erbjudanden. Ett erbjudande kan ha en eller flera planer och en plan kan ha en eller flera tjänster.

![Prenumerationer, erbjudanden och planer](media/azure-stack-key-features/image4.png)

Läs mer i [nyckelfunktioner och koncept i Azure-stacken](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Skapa ett erbjudande

Erbjudanden är grupper med en eller flera serviceplaner providers presentera för användare att köpa eller prenumerera på. Processen att skapa ett erbjudande har flera steg. Först, uppmanas du att skapa erbjudandet, och sedan en plan och slutligen kvoter.

1. [Logga in](azure-stack-connect-azure-stack.md) till portalen som en molnadministratören och välj sedan **ny** > **erbjuder + planer** > **erbjuder**.

   ![Nytt erbjudande](media/azure-stack-tutorial-tenant-vm/image01.png)

2. I **nya erbjuder**, ange en **visningsnamn** och **resursnamnet**, och välj en ny eller befintlig **resursgruppen**. Visningsnamnet är erbjudandets egna namn. Endast moln-operatorn kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

   ![Visningsnamn](media/azure-stack-tutorial-tenant-vm/image02.png)

3. Välj **basera planer**, och i den **Plan** väljer **Lägg till** att lägga till en ny plan i erbjudandet.

   ![Lägg till en plan](media/azure-stack-tutorial-tenant-vm/image03.png)

4. I den **nya planera** avsnittet, Fyll i **visningsnamn** och **resursnamnet**. Visningsnamnet är planens eget namn som visas för användarna. Endast moln-operatorn kan se resursnamnet. Det är det namn som molnoperatörer använder för att arbeta med planen som en Azure Resource Manager-resurs.

   ![Planera visningsnamn](media/azure-stack-tutorial-tenant-vm/image04.png)

5. Välj **Services**. Från listan över tjänster, Välj **Microsoft.Compute**, **Microsoft.Network**, och **Microsoft.Storage**. Välj **Välj** att lägga till dessa tjänster i planen.

   ![Planera services](media/azure-stack-tutorial-tenant-vm/image05.png)

6. Välj **kvoter**, och välj sedan den första tjänsten som du vill skapa en kvot för. Använd följande exempel för en IaaS-kvot som en vägledning för att konfigurera kvoter för beräkning, nätverk och lagring-tjänster.

   - Skapa först en kvot för beräknings-tjänsten. Välj i listan namnområde **Microsoft.Compute** och välj sedan **skapa nya kvoten**.

     ![Skapa ny kvot](media/azure-stack-tutorial-tenant-vm/image06.png)

   - I **Skapa kvot**, ange ett namn för kvoten. Du kan ändra eller acceptera kvoten värden som visas för den kvot som du skapar. I det här exemplet vi acceptera standardinställningarna och välj **OK**.

     ![Kvoten namn](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Välj **Microsoft.Compute** i listan namnområde och välj sedan den kvot som du skapade. Detta länkar kvoten till Compute-tjänsten.

     ![Välj kvot](media/azure-stack-tutorial-tenant-vm/image08.png)

      Upprepa dessa steg för nätverk och lagring. När du är klar väljer du **OK** i **kvoter** att spara alla kvoter.

7. I **ny plan**väljer **OK**.

8. Under **Plan**, Välj den nya planen och sedan **Välj**.

9. I **nya erbjudandet**väljer **skapa**. Ett meddelande visas när erbjudandet skapas.

10. Välj på menyn instrumentpanelen **erbjuder** och välj sedan det erbjudande som du skapade.

11. Välj **ändring av tillstånd**, och sedan valde **offentliga**.

    ![Offentliga tillstånd](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Lägga till en bild

Innan du kan distribuera virtuella datorer, måste du lägga till en bild på stacken för Azure marketplace. Du kan lägga till en avbildning av du väljer, inklusive Linux bilder från Azure Marketplace.

Om du arbetar i ett scenario med anslutna och om du har registrerat din Azure Stack-instans med Azure, kan du ladda ned Windows Server 2016 VM-avbildning från Azure Marketplace med hjälp av stegen som beskrivs i den [Download marketplace objekt från Azure till Azure-stacken](azure-stack-download-azure-marketplace-item.md) avsnittet.

Information om att lägga till olika objekt i marketplace finns [i Azure-stacken Marketplace](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testa erbjudandet

Nu när du har skapat ett erbjudande, kan du testa den. Du måste logga in som användare kan prenumerera på erbjudandet och Lägg sedan till en virtuell dator.

1. **Prenumerera på ett erbjudande**

   a. Logga in på användarportalen med ett användarkonto och välj den **skaffa en prenumeration** panelen.
   - För ett integrerat system URL: en varierar beroende på din operatorn region och externa domännamn och ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Om du använder Azure Stack Development Kit portal adressen är https://portal.local.azurestack.external.

   ![Skaffa en prenumeration](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. I **skaffa en prenumeration**, ange ett namn för din prenumeration i den **visningsnamn** fältet. Välj **erbjuder**, och välj sedan en erbjudanden i den **väljer du ett erbjudande** lista. Välj **Skapa**.

   ![Skapa ett erbjudande](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Om du vill visa prenumerationen **fler tjänster**, och välj sedan **prenumerationer**. Välj den nya prenumerationen för att se vilka tjänster som är en del av prenumerationen.

   >[!NOTE]
   >När du prenumererar på ett erbjudande kan behöva du uppdatera portalen om du vill se vilka tjänster som är en del av den nya prenumerationen.

2. **Etablera en virtuell dator**

   Du kan etablera en virtuell dator med den nya prenumerationen från användarportalen.

   a. Logga in på användarportalen med ett användarkonto.
      - För ett integrerat system URL: en varierar beroende på din operatorn region och externa domännamn och ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Om du använder Azure Stack Development Kit portal adressen är https://portal.local.azurestack.external.

   b.  På instrumentpanelen, väljer **ny** > **Compute** > **Windows Server 2016 Datacenter Eval**, och välj sedan **skapa**.

   c. I **grunderna**, ange följande information:
      - Ange en **namn**
      - Ange en **användarnamn**
      - Ange en **lösenord**
      - Välj en **prenumeration**
      - Skapa en **resursgruppen** (eller välj en befintlig.) 
      - Välj **OK** att spara informationen.

   d. I **välja en storlek**väljer **A1 Standard**, och sedan **Välj**.  

   e. I **inställningar**väljer **för virtuella nätverk**.

   f. I **Välj virtuellt nätverk**väljer **Skapa nytt**.

   g. I **skapa virtuellt nätverk**, acceptera alla standardvärden och välj **OK**.

   h. Välj **OK** i **inställningar** spara nätverkskonfigurationen.

   ![Skapa det virtuella nätverket](media/azure-stack-provision-vm/image04.png)

   i. I **sammanfattning**väljer **OK** att skapa den virtuella datorn.  

   j. Om du vill se den nya virtuella datorn, Välj **alla resurser**. Sök efter den virtuella datorn och välja det från sökresultaten.

   ![Alla resurser](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Lägga till en bild
> * Testa erbjudandet

Gå till nästa kurs att lära dig hur du:
> [!div class="nextstepaction"]
> [Tillgängliggöra SQL-databaser till Azure Stack-användare](azure-stack-tutorial-sql-server.md)