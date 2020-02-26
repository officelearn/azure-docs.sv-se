---
title: Installera chefs klienten från Azure Portal
description: Lär dig hur du distribuerar och konfigurerar din chefs klient från Azure Portal
keywords: Azure, chef, DevOps, klient, installation, Portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586367"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installera chefs klienten från Azure Portal
Du kan lägga till chefs klient tillägget direkt på en Linux-eller Windows-dator från Azure Portal. Den här artikeln vägleder dig genom processen med en ny virtuell Linux-dator.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Chef**: om du inte har ett aktivt chefs konto kan du registrera dig för en [kostnads fri utvärderings version av Host chef](https://manage.chef.io/signup). För att följa anvisningarna i den här artikeln behöver du följande värden från ditt chefs konto:
  - organization_validation nyckel
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installera chefs tillägget på en ny virtuell Linux-dator
I det här avsnittet ska du först använda Azure Portal för att skapa en Linux-dator. Under processen ser du också hur du installerar chefs tillägget på den nya virtuella datorn.

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. I menyn till vänster väljer du alternativet **virtuella datorer** . Om alternativet **virtuella datorer** inte finns väljer du **alla tjänster** och väljer sedan **virtuella datorer**.

1. På fliken **virtuella datorer** väljer du **Lägg till**.

    ![Lägg till en ny virtuell dator i Azure Portal](./media/chef-extension-portal/add-vm.png)

1. På fliken **Compute** väljer du önskat operativ system. **Ubuntu-servern** är markerad för den här demon.

1. På fliken **Ubuntu Server** väljer du **Ubuntu Server 16,04 LTS**.

    ![När du skapar en virtuell Ubuntu-dator anger du den version du behöver](./media/chef-extension-portal/ubuntu-server-version.png)

1. På fliken **Ubuntu Server 16,04 LTS** väljer du **skapa**.

    ![Ubuntu innehåller ytterligare information om deras produkter](./media/chef-extension-portal/create-vm.png)

1. På fliken **Skapa virtuell dator** väljer du **grunderna**.

1. På fliken **grundläggande** anger du följande värden och väljer sedan **OK**.

   - **Namn** – ange ett namn för den nya virtuella datorn.
   - **Typ av virtuell hård disk** – ange antingen **SSD** eller **HDD** för lagrings disk typen. Mer information om disk typer för virtuella datorer i Azure finns i artikeln [Välj typ av disk](../virtual-machines/windows/disks-types.md).
   - **Användar namn** – ange ett användar namn som har behörighet för administratörs behörighet på den virtuella datorn.
   - **Autentiseringstyp** – Välj **lösen ord**. Du kan också välja **Offentlig SSH-nyckel**och ange ett värde för en offentlig SSH-nyckel. För den här demonstrationen (och i skärm bilderna) är **lösen ordet** markerat.
   - **Lösen** ord och **Bekräfta lösen ord** – ange ett lösen ord för användaren.
   - **Logga in med Azure Active Directory** – Välj **inaktive rad**.
   - **Prenumeration** – Välj önskad Azure-prenumeration om du har fler än en.
   - **Resurs grupp** – ange ett namn för resurs gruppen.
   - **Plats** – Välj **USA, östra**.

     ![Fliken grunder för att skapa en virtuell dator](./media/chef-extension-portal/add-vm-basics.png)

1. På fliken **Välj en storlek** väljer du storleken på den virtuella datorn och väljer sedan **Välj**.

1. På fliken **Inställningar** fylls de flesta värdena i automatiskt baserat på de värden som du valde i föregående flikar. Välj **Tillägg**.

     ![Tillägg läggs till i virtuella datorer via fliken Inställningar](./media/chef-extension-portal/add-vm-select-extensions.png)

1. På fliken **tillägg** väljer du **Lägg till tillägg**.

     ![Välj Lägg till tillägg om du vill lägga till ett tillägg i en virtuell dator](./media/chef-extension-portal/add-vm-add-extension.png)

1. På fliken **Ny resurs** väljer du **Linux chefs tillägg (1.2.3)** .

     ![Chef har tillägg för virtuella Linux-och Windows-datorer](./media/chef-extension-portal/select-linux-chef-extension.png)

1. På fliken **Linux chef-tillägg** väljer du **skapa**.

1. På fliken **installations tillägg** anger du följande värden och väljer sedan **OK**.

    - **Chefs server-URL** – ange chefs serverns URL som innehåller organisations namnet, till exempel *https://api.chef.io/organization/mycompany* .
    - **Chefens nodnamn** – ange namnet på chefens nod.
    - **Kör lista** – ange chefs körnings listan som har lagts till i datorn. Det här värdet kan lämnas tomt.
    - **Verifierings klient namn** – ange klient namnet chefs verifiering. Till exempel `tarcher-validator`.
    - **Validerings nyckel** – Välj en fil som innehåller den verifierings nyckel som används för att starta datorerna.
    - **Klient konfigurations fil** – Välj en konfigurations fil för chef-klient. Det här värdet kan lämnas tomt.
    - **Chefs klient version** – ange den version av chefs klienten som ska installeras. Det här värdet kan lämnas tomt, vilket installerar den senaste versionen.
    - **SSL-verifierings läge** – Välj antingen **ingen** eller **peer**. *Ingen* har valts för demonstrationen.
    - **Chefs miljö** – ange chefs miljön som den här noden ska vara medlem i. Det här värdet kan lämnas tomt.
    - **Hemlighet för krypterade data väskor** – Välj en fil som innehåller hemligheten för den krypterade data uppsättningen som den här datorn behöver åtkomst till. Det här värdet kan lämnas tomt.
    - **Chefs Server SSL-certifikat** – Välj det SSL-certifikat som har tilldelats din chefs Server. Det här värdet kan lämnas tomt.

      ![Installera chefs servern på en virtuell Linux-dator](./media/chef-extension-portal/install-extension.png)

1. När fliken **tillägg** visas väljer du **OK**.

1. När fliken **Inställningar** visas väljer du **OK**.

1. När fliken **skapa** visas visas en sammanfattning av de alternativ som du har valt och angett. Verifiera informationen och **användningsvillkor**och välj **skapa**.

När processen för att skapa och distribuera den virtuella datorn med chefs tillägget är klar visar ett meddelande om att åtgärden lyckades eller misslyckades. Dessutom öppnas resurs sidan för den nya virtuella datorn automatiskt i Azure Portal när den har skapats.

![Installera chefs servern på en virtuell Linux-dator](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en virtuell Windows-dator på Azure med chef](chef-automation.md)