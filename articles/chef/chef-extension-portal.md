---
title: Installera Chef-klienten från Azure-portalen
description: Lär dig hur du distribuerar och konfigurerar din Chef-klient från Azure-portalen
keywords: azurblå,, devops, klient, installera, portal
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586367"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installera Chef-klienten från Azure-portalen
Du kan lägga till chefklienttillägget direkt på en Linux- eller Windows-dator från Azure-portalen. Den här artikeln går igenom processen med hjälp av en ny Virtuell Linux-dator.

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Chef:** Om du inte har ett aktivt Chef-konto, registrera dig för en [gratis testversion av Hosted Chef](https://manage.chef.io/signup). Om du vill följa instruktionerna i den här artikeln behöver du följande värden från ditt Chef-konto:
  - organization_validation nyckel
  - Rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installera chef-tillägget på en ny virtuell Linux-dator
I det här avsnittet ska du först använda Azure-portalen för att skapa en Linux-dator. Under processen får du också se hur du installerar chef-tillägget på den nya virtuella datorn.

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Välj alternativet **Virtuella datorer** på menyn till vänster. Om alternativet **Virtuella datorer** inte finns väljer du **Alla tjänster** och väljer sedan Virtuella **datorer**.

1. Välj **Lägg till**på fliken **Virtuella datorer** .

    ![Lägga till en ny virtuell dator i Azure-portalen](./media/chef-extension-portal/add-vm.png)

1. Välj önskat operativsystem på fliken **Beräkna.** För den här demon väljs **Ubuntu Server.**

1. Välj **Ubuntu Server 16.04 LTS**på fliken **Ubuntu Server** .

    ![När du skapar en virtuell Ubuntu-dator anger du den version du behöver](./media/chef-extension-portal/ubuntu-server-version.png)

1. Välj **Skapa**på fliken **Ubuntu Server 16.04 LTS** .

    ![Ubuntu ger ytterligare information om sin produkt](./media/chef-extension-portal/create-vm.png)

1. Välj **Grunderna**på fliken **Skapa virtuell dator** .

1. Ange följande värden på fliken **Grunderna** och välj sedan **OK**.

   - **Namn** - Ange ett namn för den nya virtuella datorn.
   - **VM-disktyp** - Ange antingen **SSD** eller **HDD** för lagringsdisktypen. Mer information om disktyper för virtuella datorer på Azure finns i artikeln [Välj en disktyp](../virtual-machines/windows/disks-types.md).
   - **Användarnamn** - Ange ett användarnamn som beviljas administratörsbehörighet på den virtuella datorn.
   - **Autentiseringstyp** - Välj **lösenord**. Du kan också välja **SSH-offentlig nyckel**och ange ett SSH-värde för offentlig nyckel. I den här demonstrationen (och i skärmbilderna) väljs **lösenord.**
   - **Lösenord** och **bekräfta lösenord** - Ange ett lösenord för användaren.
   - **Logga in med Azure Active Directory** - Välj **Inaktiverad**.
   - **Prenumeration** - Välj önskad Azure-prenumeration om du har mer än en.
   - **Resursgrupp** - Ange ett namn för resursgruppen.
   - **Plats** – Välj **USA, östra**.

     ![Fliken Grunderna för att skapa en virtuell dator](./media/chef-extension-portal/add-vm-basics.png)

1. På fliken **Välj en storlek** väljer du storleken för den virtuella datorn och väljer sedan **Välj**.

1. På fliken **Inställningar** fylls de flesta värdena i för dig baserat på de värden du valde på föregående flikar. Välj **Tillägg**.

     ![Tillägg läggs till i virtuella datorer via fliken Inställningar](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Välj **Lägg till tillägg**på fliken **Tillägg** .

     ![Välj Lägg till tillägg om du vill lägga till ett tillägg på en virtuell dator](./media/chef-extension-portal/add-vm-add-extension.png)

1. På fliken **Ny resurs** väljer du Linux Chef **Extension (1.2.3)**.

     ![Chef har tillägg för Virtuella Linux- och Windows-datorer](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Välj Skapa på fliken Tillägg **till** **Linux Chef** .

1. Ange följande värden på fliken **Installera tillägg** och välj sedan **OK**.

    - **Url till chefserver** – Ange url:en för Chef *https://api.chef.io/organization/mycompany*Server som innehåller organisationsnamnet, till exempel .
    - **Chef-nodnamn** - Ange chefnodens namn.
    - **Kör lista** - Ange chefkörningslistan som läggs till i datorn. Det här värdet kan lämnas tomt.
    - **Klientnamn för validering** - Ange chefvalideringsklientnamnet. till exempel `tarcher-validator`.
    - **Valideringsnyckel** - Välj en fil som innehåller valideringsnyckeln som används vid bootstrapping dina maskiner.
    - **Klientkonfigurationsfil** - Välj en konfigurationsfil för chef-klient. Det här värdet kan lämnas tomt.
    - **Chef Client version** - Ange den version av kockklienten att installera. Det här värdet kan lämnas tomt, som installerar den senaste versionen.
    - **SSL-verifieringsläge** - Välj **antingen Ingen** eller **Peer**. *Ingen* valdes för demon.
    - **Chef Miljö** - Ange chef miljö denna nod bör vara medlem i. Det här värdet kan lämnas tomt.
    - **Krypterad datapåse hemlighet** - Välj en fil som innehåller hemligheten för krypterad datapåse denna maskin måste komma åt. Det här värdet kan lämnas tomt.
    - **Chef Server SSL-certifikat** - Välj det SSL-certifikat som tilldelats din Chef Server. Det här värdet kan lämnas tomt.

      ![Installera Chef Server på en virtuell Linux-dator](./media/chef-extension-portal/install-extension.png)

1. När fliken **Tillägg** visas väljer du **OK**.

1. När fliken **Inställningar** visas väljer du **OK**.

1. När fliken **Skapa** visas visas en sammanfattning av de alternativ som du har markerat och angett. Verifiera informationen samt **användningsvillkoren**och välj **Skapa**.

När processen för att skapa och distribuera den virtuella datorn med cheftillägget är klar, anger ett meddelande att åtgärden lyckades eller misslyckas. Dessutom öppnas resurssidan för den nya virtuella datorn automatiskt i Azure-portalen när den har skapats.

![Installera Chef Server på en virtuell Linux-dator](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en virtuell Windows-dator på Azure med Chef](chef-automation.md)