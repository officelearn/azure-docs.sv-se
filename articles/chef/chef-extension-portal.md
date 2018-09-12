---
title: Installera klienten Chef i Azure Portal
description: Lär dig att distribuera och konfigurera din Chef-klient från Azure portal
keywords: Azure, chef, devops, klient, installation, portal
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: e121cd038b8becee1e9c4c12659dbbee0696a9f1
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378660"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installera klienten Chef i Azure Portal
När du skapar eller ändrar en Linux eller Windows-dator från Azure-portalen kan du lägga till tillägget Chef till den virtuella datorn. Den här artikeln vägleder dig igenom processen med hjälp av en ny virtuell Linux-dator.

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Chef**: Om du inte har ett aktivt konto Chef kan registrera dig för en [kostnadsfri utvärderingsversion av värdbaserade Chef](https://manage.chef.io/signup). Om du vill följa anvisningarna i den här artikeln behöver du följande värden från din Chef-konto: 
    - organization_validation nyckel
    - RB
    - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installera tillägget Chef på en ny virtuell Linux-dator
I det här avsnittet ska du först använda Azure-portalen för att skapa en Linux-dator. Under processen visas också hur du installerar tillägget Chef på den nya virtuella datorn.

1. Bläddra till den [Azure-portalen](http://portal.azure.com).

1. På menyn till vänster, väljer den **virtuella datorer** alternativet. Om den **virtuella datorer** alternativet är inte tillgänglig, Välj **alla tjänster** och välj sedan **virtuella datorer**.

1. På den **virtuella datorer** fliken **Lägg till**.

    ![Lägg till en ny virtuell dator i Azure portal](./media/chef-extension-portal/add-vm.png)

1. På den **Compute** , Välj önskat operativsystem. Den här demonstrationen **Ubuntu Server** har valts.

1. På den **Ubuntu Server** fliken **Ubuntu Server 16.04 LTS**.

    ![När du skapar en Ubuntu-dator, anger du den version som du behöver](./media/chef-extension-portal/ubuntu-server-version.png)

1. På den **Ubuntu Server 16.04 LTS** fliken **skapa**.

    ![Ubuntu tillhandahåller ytterligare information om sina produkter](./media/chef-extension-portal/create-vm.png)

1. På den **Skapa virtuell dator** fliken **grunderna**.

1. På den **grunderna** fliken, ange följande värden och därefter **OK**.

    - **Namn på** -ange ett namn för den nya virtuella datorn.
    - **Typ av virtuell datordisk** -ange antingen **SSD** eller **HDD** för storage-disktyp. Mer information om disktyper för virtuell dator på Azure finns i artikeln [högpresterande Premium Storage och hanterade diskar för virtuella datorer](/azure/virtual-machines/windows/premium-storage).
    - **Användarnamn** -ange ett användarnamn som har beviljats administratörsbehörighet på den virtuella datorn.
    - **Autentiseringstyp** – Välj **lösenord**. Du kan också välja **offentlig SSH-nyckel**, och ange en SSH-offentliga nyckelvärde. Gäller för den här demon (och i skärmbilderna) **lösenord** har valts.
    - **Lösenordet** och **Bekräfta lösenord** -ange ett lösenord för användaren.
    - **Logga in med Azure Active Directory** – Välj **inaktiverad**.
    - **Prenumeration** – Välj den önskade Azure-prenumerationen om du har fler än en.
    - **Resursgrupp** -ange ett namn för resursgruppen.
    - **Plats** – Välj **USA, östra**.

    ![Fliken grunderna för att skapa en virtuell dator](./media/chef-extension-portal/add-vm-basics.png)

1. På den **väljer du en storlek** fliken, välja storlek för den virtuella datorn och välj sedan **Välj**.

1. På den **inställningar** fliken, de flesta av värdena som fylls i automatiskt baserat på de värden du valde på föregående flikar. Välj **tillägg**.

    ![Tillägg har lagts till virtuella datorer via fliken Inställningar](./media/chef-extension-portal/add-vm-select-extensions.png)

1. På den **tillägg** fliken **lägga till tillägget**.

    ![Välj Lägg till tillägg för att lägga till ett tillägg till en virtuell dator](./media/chef-extension-portal/add-vm-add-extension.png)

1. På den **ny resurs** fliken **Linux Chef-tillägget (1.2.3)**.

    ![Chef har tillägg för Linux och Windows-datorer](./media/chef-extension-portal/select-linux-chef-extension.png)

1. På den **Linux Chef tillägget** fliken **skapa**.

1. På den **installera tillägget** fliken, ange följande värden och därefter **OK**.

    - **Chef-Serveradress** – ange Webbadressen för Chef-Server som innehåller organisationsnamnet på, till exempel *https://api.chef.io/organization/hessco*.
    - **Chef nodnamnet** – ange namnet på Chef noden. Detta kan vara vilket värde som helst.
    - **Kör listan** -ange Chef kör-lista som har lagts till datorn. Detta kan vara tomt.
    - **Verifiering klientnamn** -ange namn för Chef verifiering klient. till exempel *tarcher verifieraren*.
    - **Valideringsnyckel** -Välj en fil som innehåller valideringsnyckel som används vid start av dina datorer. 
    - **Klientkonfigurationsfilen** -Välj en konfigurationsfil för chef-klient. Detta kan vara tomt.
    - **Chef-klientversionen** – ange vilken version av klienten chef att installera. Detta kan vara tomt. Ett tomt värde medför den senaste versionen installeras. 
    - **SSL-verifieringsläge** – Välj antingen **ingen** eller **Peer**. *Ingen* har valts för demon.
    - **Chef miljö** – ange den här noden ska vara medlem i Chef miljön. Detta kan vara tomt.
    - **Krypterad Databag hemlighet** -Välj en fil som innehåller hemligheten för den krypterade Databag den här datorn ska ha åtkomst till. Detta kan vara tomt.
    - **Chef serverns SSL-certifikat** -Välj SSL-certifikat som har tilldelats till din Chef-Server. Detta kan vara tomt.

    ![Installera Chef-servern på en Linux-dator](./media/chef-extension-portal/install-extension.png)

1. När du tillbaka till den **tillägg** fliken **OK**.

1. När du tillbaka till den **inställningar** fliken **OK**.

1. När tillbaka till den **skapa** fliken (detta representerar en sammanfattning av de alternativ du valt och skrivit), kontrollerar du informationen och **användningsvillkoren**, och välj **skapa**.

När processen för att skapa och distribuera den virtuella datorn med Chef-tillägget är klar, visar ett meddelande har lyckats eller misslyckats för åtgärden. Resurssidan för den nya virtuella datorn öppnas dessutom automatiskt i Azure-portalen när det har skapats.

![Installera Chef-servern på en Linux-dator](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Nästa steg
* [Skapa en virtuell Windows-dator på Azure med Chef](/azure/virtual-machines/windows/chef-automation)
