---
title: Skapa ett labb i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln vägleder dig genom processen att skapa ett labb med hjälp av Azure Portal och Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 5cd675823b85e975dcb8dfe152c27b2d30463c1c
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897042"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Skapa ett labb i Azure DevTest Labs
Ett labb i Azure DevTest Labs är den infrastruktur som omfattar en grupp med resurser, till exempel virtuella datorer (VM), som låter dig hantera resurserna genom att ange gränser och kvoter. Den här artikeln beskriver steg för steg hur du skapar ett labb med hjälp av Azure CLI.

## <a name="prerequisites"></a>Krav
Du behöver följande om du vill skapa ett labb:

* En Azure-prenumeration. Mer information om köpalternativ för Azure finns i [Så här köper du Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [Kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). För att kunna skapa labbet måste du vara prenumerationens ägare.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Steg för att skapa ett labb i Azure DevTest Labs
Följande steg illustrerar hur du använder Azure-portalen för att skapa ett labb i Azure DevTest Labs. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. På huvudmenyn på vänster sida väljer du **Alla tjänster** (längst ned i listan). Välj * (Star) bredvid **DevTest Labs** i avsnittet **DEVOPS** . Den här åtgärden lägger till **DevTest Labs** i den vänstra navigerings menyn så att du enkelt kan komma åt dem nästa gång. 

    ![Alla tjänster – Välj DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Välj nu **DevTest Labs** i den vänstra navigerings menyn. Välj **Lägg till** i verktygsfältet. 
   
    ![Lägga till ett labb](./media/devtest-lab-create-lab/add-lab-button.png)
1. Gör följande på sidan **skapa en DevTest-labb** åtgärd: 
    1. Ange ett **namn** på labbet.
    2. Välj den **prenumeration** som du vill koppla till labbet.
    3. Ange ett **namn på resurs gruppen** för labbet. 
    4. Välj en **plats** där du vill lagra labbet.
    4. Välj **Automatisk avstängning** för att ange om du vill aktivera, och definiera parametrar för, automatisk avstängning av alla labbets virtuella datorer. Funktionen automatisk avstängning är främst en kostnadfunktion där du kan ange om du vill att den virtuella datorn ska stängas automatiskt. Du kan ändra inställningarna för automatisk avstängning när du har skapat labbet genom att följa stegen som beskrivs i artikeln [hantera alla principer för ett labb i Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Ange **NAME** (namn) och **VALUE** (värde) för **Tags** (taggar) om du vill skapa anpassade taggar som läggs till i alla resurser som du skapar i labbet. Taggar är användbara för att hantera och ordna labbresurser efter kategori. Mer information om taggar, inklusive hur du lägger till taggar när du har skapat labbet, finns i [Lägga till taggar i ett labb](devtest-lab-add-tag.md).
    6. Välj **Automatiseringsalternativ** för att hämta Azure Resource Manager-mallar för konfigurationsautomatisering. 
    7. Välj **Skapa**. Du kan övervaka statusen för labbprocessen genom att titta på **meddelande**området. 
    
        ![Skapa ett labbavsnitt för DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. När du är klar väljer **du gå till resurs** i meddelandet. Du kan också uppdatera **DevTest Labs** -sidan om du vill se det nyligen skapade labbet i listan över labb.  Välj labbet i listan. Du ser start sidan för ditt labb. 

        ![Start sida för labbet](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat labbet kan du fundera på följande steg:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange labb principer](devtest-lab-set-lab-policy.md)
* [Skapa en labb-mall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)

