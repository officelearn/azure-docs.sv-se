---
title: Skapa ett labb i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln går igenom processen att skapa ett labb med Azure-portalen och Azure DevTest Labs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759745"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Skapa ett labb i Azure DevTest Labs
Ett labb i Azure DevTest Labs är den infrastruktur som omfattar en grupp med resurser, till exempel virtuella datorer (VM), som låter dig hantera resurserna genom att ange gränser och kvoter. Den här artikeln beskriver steg för steg hur du skapar ett labb med hjälp av Azure CLI.

## <a name="prerequisites"></a>Krav
Du behöver följande om du vill skapa ett labb:

* En Azure-prenumeration. Mer information om köpalternativ för Azure finns i [Så här köper du Azure](https://azure.microsoft.com/pricing/purchase-options/) eller [Kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/). För att kunna skapa labbet måste du vara prenumerationens ägare.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Steg för att skapa ett labb i Azure DevTest Labs
Följande steg illustrerar hur du använder Azure-portalen för att skapa ett labb i Azure DevTest Labs. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. På huvudmenyn på vänster sida väljer du **Alla tjänster** (längst ned i listan). Välj * (stjärna) bredvid **DevTest Labs** i **DEVOPS-avsnittet.** Den här åtgärden lägger till **DevTest Labs** i menyn till vänster så att du enkelt kan komma åt den nästa gång. 

    ![Alla tjänster - välj DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Välj nu **DevTest Labs** på menyn till vänster. Välj **Lägg till** i verktygsfältet. 
   
    ![Lägga till ett labb](./media/devtest-lab-create-lab/add-lab-button.png)
1. Gör följande åtgärder på sidan **Skapa ett DevTest Lab:** 
    1. Ange ett **namn** på labbet.
    2. Välj den **prenumeration** som du vill koppla till labbet.
    3. Ange ett **namn för resursgruppen** för labbet. 
    4. Välj en **plats** där labbet ska lagras.
    4. Välj **Automatisk avstängning** för att ange om du vill aktivera, och definiera parametrar för, automatisk avstängning av alla labbets virtuella datorer. Funktionen automatisk avstängning är främst en kostnadfunktion där du kan ange om du vill att den virtuella datorn ska stängas automatiskt. Du kan ändra inställningarna för automatisk avstängning när du har skapat labbet genom att följa stegen i artikeln [Hantera alla principer för ett labb i Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Ange **NAME** (namn) och **VALUE** (värde) för **Tags** (taggar) om du vill skapa anpassade taggar som läggs till i alla resurser som du skapar i labbet. Taggar är användbara för att hantera och ordna labbresurser efter kategori. Mer information om taggar, inklusive hur du lägger till taggar när du har skapat labbet, finns i [Lägga till taggar i ett labb](devtest-lab-add-tag.md).
    6. Välj **Automatiseringsalternativ** för att hämta Azure Resource Manager-mallar för konfigurationsautomatisering. 
    7. Välj **Skapa**. Du kan övervaka statusen för labbprocessen genom att titta på **meddelande**området. 
    
        ![Skapa ett labbavsnitt för DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. När du är klar väljer du **Gå till resurs** i meddelandet. Du kan också uppdatera sidan **DevTest Labs** för att se det nyskapade labbet i listan över labb.  Välj labbet i listan. Du ser hemsidan för ditt labb. 

        ![Startsida för labbet](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat labbet kan du fundera på följande steg:

* [Säker åtkomst till ett labb](devtest-lab-add-devtest-user.md)
* [Ange labbprinciper](devtest-lab-set-lab-policy.md)
* [Skapa en labbmall](devtest-lab-create-template.md)
* [Skapa anpassade artefakter för dina virtuella datorer](devtest-lab-artifact-author.md)
* [Lägga till en virtuell dator i ett labb](devtest-lab-add-vm.md)

