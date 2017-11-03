---
title: "Skapa flera Virtuella miljöer och PaaS-resurser med Azure Resource Manager-mallar | Microsoft Docs"
description: "Lär dig att skapa flera Virtuella miljöer och PaaS-resurser i Azure DevTest Labs från en Azure Resource Manager-mall"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tarcher
ms.openlocfilehash: 4e1aae6c041e4572e7e2281203f969e7649e1480
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Skapa flera Virtuella miljöer och PaaS-resurser med Azure Resource Manager-mallar

Den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040) kan du enkelt [skapa och lägga till en virtuell dator i ett labb](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-add-vm). Detta fungerar bra för att skapa en virtuell dator i taget. Om miljön innehåller flera virtuella datorer, har varje virtuell dator skapas individuellt. För scenarier, till exempel en webbapp i flera nivåer eller en SharePoint-servergrupp krävs en mekanism för att tillåta för att skapa flera virtuella datorer i ett enda steg. Med hjälp av Azure Resource Manager-mallar kan du nu ange infrastrukturen och konfigurationen av din lösning för Azure och upprepade gånger distribuera flera virtuella datorer i ett konsekvent tillstånd. Den här funktionen ger följande fördelar:

- Azure Resource Manager-mallar har lästs in direkt från din källkontroll (GitHub eller Team Services Git).
- När du konfigurerat dina användare kan skapa en miljö genom att välja en Azure Resource Manager-mall från Azure portal som de kan göra med andra typer av [VM baser](./devtest-lab-comparing-vm-base-image-types.md).
- Azure PaaS-resurser kan etableras i en miljö med en Azure Resource Manager-mall förutom IaaS-VM.
- Kostnaden för miljöer kan spåras i labbet förutom enskilda virtuella datorer som skapats av andra typer av databaser.
- PaaS-resurser skapas och visas i Kostnadsuppföljning; automatisk avstängning på VM gäller dock inte för PaaS-resurser.
- Användare har samma VM-principkontroll för miljöer som de har för enskild lab virtuella datorer.

Mer information om många [fördelarna med att använda Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) för att distribuera, uppdatera eller ta bort alla dina lab resurser i en enda åtgärd.

> [!NOTE]
> När du använder en Resource Manager-mall som bas för att skapa mer labb virtuella datorer, finns det vissa skillnader som du bör tänka på om du skapar flera virtuella datorer eller enskild för virtuella datorer. Använd en virtuell dators Azure Resource Manager-mall beskrivs skillnaderna i detalj.
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Konfigurera databaser för Azure Resource Manager-mall

Som en bästa praxis med infrastruktur som koden och konfigurationskod som ska miljö mallar hanteras i källkontroll. Azure DevTest Labs följer den här övningen och läser in alla mallar för Azure Resource Manager direkt från GitHub eller VSTS Git-databaser. Resource Manager-mallar användas över hela versionen livscykel, från testmiljö till produktionsmiljön.

Det finns några regler för att ordna dina Azure Resource Manager-mallar i en databas:

- Huvudmall-filen måste ha namnet `azuredeploy.json`. 

    ![Nyckeln Azure Resource Manager mallfilerna](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Om du vill använda parametervärden som definierats i en parameterfil parameterfilen måste ha namnet `azuredeploy.parameters.json`.
- Du kan använda parametrarna `_artifactsLocation` och `_artifactsLocationSasToken` så att DevTest Labs hantera automatiskt kapslade mallar för att skapa parametersLink URI-värdet. Se [hur Azure DevTest Labs underlättar kapslade Resource Manager mall-distributioner för testmiljöer](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/) för mer information.
- Metadata kan definieras för att ange Mallens visningsnamn och beskrivning. Dessa metadata måste vara i en fil med namnet `metadata.json`. Metadatafilen som följande exempel visar hur du kan ange namn och beskrivning: 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

Följande steg leder dig genom att lägga till en databas i ditt labb använder Azure portal. 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
1. Lista över labs, Välj önskade labbet.   
1. På den testmiljön bladet välj **konfiguration och principer**.

    ![Konfiguration och principer](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Från den **konfiguration och principer** inställningslistan markerar **databaser**. Den **databaser** bladet visar en lista över databaser som har lagts till i labbet. En databas med namnet `Public Repo` genereras automatiskt för alla testlabb och ansluter till den [DevTest Labs GitHub-repo-](https://github.com/Azure/azure-devtestlab) som innehåller flera Virtuella artefakter för din användning.

    ![Offentliga lagringsplatsen](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Välj **Lägg till +** att lägga till databasen för din Azure Resource Manager-mall.
1. När andra **databaser** blad öppnas, ange nödvändig information på följande sätt:
    - **Namnet** -ange databasnamn som används i labbet.
    - **URL för Git-klon** -ange klon-URL: en för GIT HTTPS från GitHub eller Visual Studio Team Services.  
    - **Branch** – ange namnet gren att få åtkomst till Azure Resource Manager mallen definitionerna. 
    - **Personlig åtkomsttoken** -personlig åtkomst-token används säker åtkomst till databasen. För att få din token från Visual Studio Team Services, Välj  **&lt;dittnamn >> min profil > Säkerhet > offentlig åtkomst-token**. För att få din token från GitHub, Välj din avatar följt genom att välja **Inställningar > offentlig åtkomst-token**. 
    - **Mappsökvägar** -med någon av de två inmatningsfält, ange sökvägen till mappen som börjar med ett snedstreck - / - och är i förhållande till din Git-klonen URI antingen dina artefaktdefinitioner (första inmatningsfältet) eller Azure Resource Manager mallen definitionerna.   
    
        ![Offentliga lagringsplatsen](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. När alla obligatoriska fält har angetts och skicka verifieringen, välja **spara**.

I nästa avsnitt får du hjälp med att skapa miljöer från en Azure Resource Manager-mall.

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>Skapa en miljö med en Azure Resource Manager-mall

När en mall för Azure Resource Manager-databasen har konfigurerats i labbet, kan lab-användare skapa en miljö med hjälp av Azure-portalen med följande steg:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
1. Lista över labs, Välj önskade labbet.   
1. På den testmiljön bladet välj **Lägg till +**.
1. Den **väljer du en bas** bladet visar de grundläggande bilder som du kan använda med Azure Resource Manager-mallar som visas först. Välj den önskade Azure Resource Manager-mallen.

    ![Välj en bas](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. På den **Lägg till** bladet anger du den **miljönamn** värdet. Namnet på miljön är det som visas för användarna i labbet. Återstående indatafält har definierats i Azure Resource Manager-mallen. Om standardvärden definieras i mallen eller `azuredeploy.parameter.json` -fil, standardvärden visas i dessa indatafälten. För parametrar av typen *säker sträng*, du kan använda hemligheter som lagras i testmiljön [hemliga datorarkivet](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store).

    ![Bladet Lägg till](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Det finns flera parametervärden som – även om angett - visas som tomma värden. Därför om användare tilldelar dessa värden för parametrar i en Azure Resource Manager-mall, visas DevTest Labs inte på värden. i stället visar tomt inmatningsfält där lab-användare måste ange ett värde när du skapar miljön.
    > 
    > - GEN UNIKT
    > - GEN - UNIKT-[N]
    > - GEN SSH-PUB-NYCKEL
    > - GEN-LÖSENORD 
 
1. Välj **Lägg till** att skapa miljön. Miljön startar etablering direkt med status visas i den **Mina virtuella datorer** lista. Labbet för att etablera alla resurser som definierats i mallen för Azure Resource Manager skapas automatiskt en ny resursgrupp.
1. När miljön har skapats, Välj miljön i den **Mina virtuella datorer** listan för att öppna bladet för resursgruppen och bläddra alla resurser som har etablerats i miljön.
    
    ![Min virtuella datorer.](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Du kan också expandera miljö för att visa bara listan över virtuella datorer som har etablerats i miljön.
    
    ![Min virtuella datorer.](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Klicka på någon av miljöerna för att visa tillgängliga åtgärder – till exempel använda artefakter, bifoga datadiskar, ändra automatisk avstängning tid och mycket mer.

    ![Åtgärder för miljö](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>Nästa steg
* När en virtuell dator har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** på bladet för den virtuella datorn.
* Visa och hantera resurser i en miljö genom att välja miljön i den **Mina virtuella datorer** lista i labbet. 
* Utforska den [Azure Resource Manager-mallar från Azure Quickstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates)
