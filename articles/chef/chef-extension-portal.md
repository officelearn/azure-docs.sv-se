---
title: Installera klienten Chef från Azure-portalen
description: Lär dig hur du distribuerar och konfigurerar klientdatorerna Chef från Azure-portalen
keywords: Azure, chef, devops, klient, installation, portal
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 52f34361d7c1f3dff47f2571a714b8be7764cc6f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installera klienten Chef från Azure-portalen
När du skapar eller ändrar en Linux- eller Windows virtuell dator från Azure portal, kan du lägga till filnamnstillägget Chef till den virtuella datorn. Den här artikeln vägleder dig genom processen med hjälp av en ny virtuell Linux-dator.

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Chef**: Om du inte har ett aktivt Chef-konto kan du registrera dig för en [kostnadsfri utvärderingsversion av värdbaserade Chef](https://manage.chef.io/signup). Om du vill följa anvisningarna i den här artikeln behöver du följande värden från din Chef konto: 
    - organization_validation nyckel
    - RB
    - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installera tillägget Chef på en ny virtuell Linux-dator
I det här avsnittet ska du först använda Azure-portalen för att skapa en Linux-dator. Under processen kan också visas hur du installerar tillägget Chef på den nya virtuella datorn.

1. Bläddra till den [Azure-portalen](http://portal.azure.com).

1. Välj på menyn till vänster i **virtuella datorer** alternativet. Om den **virtuella datorer** alternativet är inte tillgänglig, Välj **alla tjänster** och välj sedan **virtuella datorer**.

1. På den **virtuella datorer** väljer **Lägg till**.

    ![Lägg till en ny virtuell dator i Azure-portalen](./media/chef-extension-portal/add-vm.png)

1. På den **Compute** väljer du önskat operativsystem. Den här demonstrationen **Ubuntu Server** är markerad.

1. På den **Ubuntu Server** väljer **Ubuntu Server 16.04 LTS**.

    ![När du skapar en virtuell Ubuntu-dator, ange den version du behöver](./media/chef-extension-portal/ubuntu-server-version.png)

1. På den **Ubuntu Server 16.04 LTS** väljer **skapa**.

    ![Ubuntu tillhandahåller ytterligare information om sina produkter](./media/chef-extension-portal/create-vm.png)

1. På den **Skapa virtuell dator** väljer **grunderna**.

1. På den **grunderna** , ange följande värden, och välj sedan **OK**.

    - **Namnet** -ange ett namn för den nya virtuella datorn.
    - **VM disktyp** -ange antingen **SSD** eller **Hårddisk** för disk lagringstyp. Mer information om disktyper som virtuell dator i Azure finns i artikeln [Premium-lagring med hög prestanda och hanterade diskar för virtuella datorer](/azure/virtual-machines/windows/premium-storage).
    - **Användarnamnet** -ange ett användarnamn som har beviljats administratörsbehörighet på den virtuella datorn.
    - **Autentiseringstypen** – Välj **lösenord**. Du kan också välja **offentliga SSH-nyckeln**, och ange ett SSH offentlig nyckel/värde. För tillämpning av den här demon (och i skärmbilderna) **lösenord** är markerad.
    - **Lösenordet** och **Bekräfta lösenord** -ange ett lösenord för användaren.
    - **Logga in med Azure Active Directory** – Välj **inaktiverade**.
    - **Prenumerationen** – Välj den önskade Azure-prenumerationen om du har mer än ett.
    - **Resursgruppen** -ange ett namn för resursgruppen.
    - **Plats** – Välj **östra USA**.

    ![Fliken grunderna för att skapa en virtuell dator](./media/chef-extension-portal/add-vm-basics.png)

1. På den **välja en storlek** , Välj storlek för den virtuella datorn och välj sedan **Välj**.

1. På den **inställningar** de flesta av värdena på fliken fylls i automatiskt baserat på de värden som du valde i föregående flikar. Välj **tillägg**.

    ![Tillägg läggs till virtuella datorer via fliken Inställningar](./media/chef-extension-portal/add-vm-select-extensions.png)

1. På den **tillägg** väljer **lägga till tillägget**.

    ![Välj Lägg till filnamnstillägg att lägga till ett tillägg till en virtuell dator](./media/chef-extension-portal/add-vm-add-extension.png)

1. På den **ny resurs** väljer **Linux Chef tillägg (1.2.3)**.

    ![Chef har tillägg för Linux och Windows-datorer](./media/chef-extension-portal/select-linux-chef-extension.png)

1. På den **Linux Chef tillägget** väljer **skapa**.

1. På den **installera tillägget** , ange följande värden, och välj sedan **OK**.

    - **Chef Serveradress** -ange Chef Server URL: en som innehåller organisationsnamnet på. Jag använde *https://api.chef.io/organization/hessco* för demonstrationen.
    - **Chef nodnamnet** -ange nodnamn för Chef. Detta kan vara ett värde.
    - **Kör listan** -ange Chef Kör listan som har lagts till datorn. Detta kan vara tom.
    - **Verifieringen klientnamn** -ange Chef validering klientnamn. Jag använde *tarcher verifieraren* för demonstrationen.
    - **Valideringsnyckel** -Välj en fil som innehåller valideringsnyckel som används när startprogram för dina datorer. 
    - **Klientkonfigurationsfilen** -Välj en konfigurationsfil för chef-klient. Detta kan vara tom.
    - **Chef klientversionen** – ange vilken version av chef-klienten för installation. Ett tomt värde medför den senaste versionen installeras. Detta kan vara tom.
    - **SSL-läge för verifiering** -väljer du antingen **ingen** eller **Peer**. Jag har valt *ingen* för demonstrationen.
    - **Chef miljö** -ange Chef miljön noden ska vara medlem i. Detta kan vara tom.
    - **Krypterad Databag hemlighet** -Välj en fil som innehåller hemligheten för den krypterade Databag den här datorn ska ha åtkomst till. Detta kan vara tom.
    - **Chef serverns SSL-certifikat** -Välj SSL-certifikat som har tilldelats servern Chef. Detta kan vara tom.

    ![Installera Chef-servern på en virtuell Linux-dator](./media/chef-extension-portal/install-extension.png)

1. När tillbaka till den **tillägg** väljer **OK**.

1. När tillbaka till den **inställningar** väljer **OK**.

1. När tillbaka till den **skapa** flik (som representerar en sammanfattning av de alternativ du valt och skrivit), kontrollerar du informationen samt de **användningsvillkoren**, och välj **skapa**.

När processen för att skapa och distribuera den virtuella datorn med filnamnstillägget Chef är klar, anger ett meddelande har lyckats eller misslyckats av åtgärden. Dessutom öppnas för den nya virtuella datorn automatiskt resurssidan i Azure-portalen när den skapas.

![Installera Chef-servern på en virtuell Linux-dator](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Nästa steg
* [Skapa en virtuell Windows-dator på Azure med hjälp av Chef](/azure/virtual-machines/windows/chef-automation)