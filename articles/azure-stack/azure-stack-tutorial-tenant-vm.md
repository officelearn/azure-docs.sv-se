---
title: Tillhandahålla virtuella datorer till Azure-stacken användarna | Microsoft Docs
description: Självstudiekursen för att tillhandahålla virtuella datorer på Azure-stacken
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Tillhandahålla virtuella datorer till Azure Stack-användare
Du kan skapa erbjudanden som användarna (kallas ibland klienter) kan prenumerera på som administratör Stack för Azure-molnet. Använda sin prenumeration kan kan användare använda Azure Stack-tjänster.

Den här artikeln visar hur du skapar ett erbjudande och sedan testa den. För testet, ska du logga in på portalen som en användare, prenumerera erbjudandet och sedan skapa en virtuell dator med prenumerationen.

Vad får du lära dig:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Lägga till en bild
> * Testa erbjudandet


Tjänster som levereras till användare med hjälp av prenumerationer, erbjudanden och planer i Azure-stacken. Användare kan prenumerera på flera erbjudanden. Erbjudanden som kan ha en eller flera planer och planer kan ha en eller flera tjänster.

![Prenumerationer, erbjudanden och planer](media/azure-stack-key-features/image4.png)

Läs mer i [nyckelfunktioner och koncept i Azure-stacken](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Skapa ett erbjudande

Nu kan du få saker redo för användarna. När du startar processen uppmanas du först skapa erbjudandet, och sedan en plan och slutligen kvoter.

3. **Skapa ett erbjudande**

   Erbjudanden är grupper med en eller flera serviceplaner providers presentera för användare att köpa eller prenumerera på.

   a. [Logga in](azure-stack-connect-azure-stack.md) till portalen som en molnadministratören och klicka sedan på **ny** > **erbjuder + planer** > **erbjuder**.
   ![Nya erbjudande](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. I den **nya erbjuder** avsnittet, Fyll i **visningsnamn** och **resursnamnet**, och välj en ny eller befintlig **resursgruppen**. Visningsnamnet är erbjudandets egna namn. Endast moln-operatorn kan se resursnamnet. Det är det namn som administratörer använder för att arbeta med erbjudandet som en Azure Resource Manager-resurs.

   ![Visningsnamn](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Klicka på **basera planer**, och i den **Plan** klickar du på **Lägg till** att lägga till en ny plan i erbjudandet.

   ![Lägg till en plan](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. I den **nya planera** avsnittet, Fyll i **visningsnamn** och **resursnamnet**. Visningsnamnet är planens eget namn som visas för användarna. Endast moln-operatorn kan se resursnamnet. Det är det namn som molnoperatörer använder för att arbeta med planen som en Azure Resource Manager-resurs.

   ![Planera visningsnamn](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Klicka på **Services**väljer **Microsoft.Compute**, **Microsoft.Network**, och **Microsoft.Storage**, och klicka sedan på **Välj**.

   ![Planera services](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Klicka på **kvoter**, och välj sedan den första tjänsten som du vill skapa en kvot. Följ dessa steg för beräkning, nätverk och lagring-tjänster för en IaaS-kvot.

   I det här exemplet skapar vi först en kvot för beräknings-tjänsten. Välj i listan namnområde i **Microsoft.Compute** namnområde och klicka sedan på **skapa nya kvoten**.
   
   ![Skapa ny kvot](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. På den **Skapa kvot** , Skriv ett namn för kvoten och ange de önskade parametrarna för kvoten och klickar på **OK**.

   ![Kvoten namn](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. Nu för **Microsoft.Compute**, Välj den kvot som du skapade.

   ![Välj kvot](media/azure-stack-tutorial-tenant-vm/image08.png)

   Upprepa dessa steg för nätverk och lagring och klicka sedan på **OK** på den **kvoter** avsnitt.

   i. Klicka på **OK** på den **ny plan** avsnitt.

   j. På den **Plan** väljer du den nya planen och klickar på **Välj**.

   k. På den **nya erbjudandet** klickar du på **skapa**. Du ser ett meddelande när erbjudandet har skapats.

   l. Klicka på menyn instrumentpanelen **erbjuder** och klicka sedan på det erbjudande som du skapade.

   m. Klicka på **Ändra status** och klicka därefter på **Offentlig**.

   ![Offentliga tillstånd](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Lägga till en bild

Innan du kan distribuera virtuella datorer, måste du lägga till en bild på stacken för Azure marketplace. Du kan lägga till en avbildning av du väljer, inklusive Linux bilder från Azure Marketplace.

Om du arbetar i ett scenario med anslutna och om du har registrerat din Azure Stack-instans med Azure, kan du ladda ned Windows Server 2016 VM-avbildning från Azure Marketplace med hjälp av stegen som beskrivs i den [Download marketplace objekt från Azure till Azure-stacken](azure-stack-download-azure-marketplace-item.md) avsnittet.

Information om att lägga till olika objekt i marketplace finns [i Azure-stacken Marketplace](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testa erbjudandet

Nu när du har skapat ett erbjudande, kan du testa den. Logga in som en användare och prenumerera på erbjudandet och Lägg sedan till en virtuell dator.

1. **Prenumerera på ett erbjudande**

   Nu kan du logga in på portalen som en användare att prenumerera på ett erbjudande.

   a. Logga in på användarportalen som en användare och klicka på **skaffa en prenumeration**.
   - För ett integrerat system URL: en varierar beroende på din operatorn region och externa domännamn och ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Om du använder Azure Stack Development Kit portal adressen är https://portal.local.azurestack.external.

   ![Skaffa en prenumeration](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. I den **visningsnamn** fältet, Skriv ett namn för din prenumeration, klickar du på **erbjuder**, klicka på någon av erbjudanden i den **väljer du ett erbjudande** avsnittet och klicka sedan på  **Skapa**.

   ![Skapa ett erbjudande](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Så här visar du skapade prenumerationen **fler tjänster**, klickar du på **prenumerationer**, klicka på den nya prenumerationen.  

   När du prenumererar på ett erbjudande uppdatera portalen om du vill se vilka tjänster som är en del av den nya prenumerationen.

2. **Etablera en virtuell dator**

   Nu kan du logga in på portalen som en användare att etablera en virtuell dator med prenumerationen. 

   a. Logga in på användarportalen som en användare.
      - För ett integrerat system URL: en varierar beroende på din operatorn region och externa domännamn och ska vara i formatet https://portal.&lt; *region*&gt;.&lt; *FQDN*&gt;.
   - Om du använder Azure Stack Development Kit portal adressen är https://portal.local.azurestack.external.

   b.  På instrumentpanelen, klickar du på **ny** > **Compute** > **Windows Server 2016 Datacenter Eval**, och klicka sedan på **skapa**.

   c. I den **grunderna** skriver en **namn**, **användarnamn**, och **lösenord**, Välj en **prenumeration**, Skapa en **resursgruppen** (eller välj ett befintligt) och klicka sedan på **OK**.

   d. I den **välja en storlek** klickar du på **A1 Standard**, och klicka sedan på **Välj**.  

   e. I den **inställningar** klickar du på **för virtuella nätverk**. I den **Välj virtuellt nätverk** klickar du på **Skapa nytt**. I den **skapa virtuellt nätverk** avsnittet acceptera alla standardvärden och klicka på **OK**. I den **inställningar** klickar du på **OK**.

   ![Skapa det virtuella nätverket](media/azure-stack-provision-vm/image04.png)

   f. I den **sammanfattning** klickar du på **OK** att skapa den virtuella datorn.  

   g. Klicka för att visa den nya virtuella datorn **alla resurser**, Sök sedan efter den virtuella datorn och klickar på dess namn.

    ![Alla resurser](media/azure-stack-provision-vm/image06.png)

Vad du lärt dig i den här självstudiekursen:

> [!div class="checklist"]
> * Skapa ett erbjudande
> * Lägga till en bild
> * Testa erbjudandet

> [!div class="nextstepaction"]
> [Göra webb-, mobil- och API apps tillgängliga för användarna i Azure-stacken](azure-stack-tutorial-app-service.md)