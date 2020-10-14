---
title: Skapa arbets ytor i portalen
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar, visar och tar bort Azure Machine Learning arbets ytor i Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: d0b5d3678c3d9c7e55eede13c630510df89d5128
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045575"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Skapa och hantera Azure Machine Learning arbets ytor i Azure Portal


I den här artikeln skapar du, visar och tar bort [**Azure Machine Learning arbets ytor**](concept-workspace.md) i Azure Portal för [Azure Machine Learning](overview-what-is-azure-ml.md).  Portalen är det enklaste sättet att komma igång med arbets ytor, men när du behöver ändra eller krav på Automation-höjning kan du också skapa och ta bort arbets ytor [med hjälp av CLI](reference-azure-machine-learning-cli.md), [med python-kod](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) eller [via vs Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Du behöver en Azure-prenumeration för att skapa en arbets yta. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration. 

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

      ![Skapa en ny resurs](./media/how-to-manage-workspace/create-workspace.gif)

1. Använd Sök fältet för att hitta **Machine Learning**.

1. Välj **Machine Learning**.

1. I fönstret **Machine Learning** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Fält|Beskrivning 
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra. Namnet på arbets ytan är Skift läges okänsligt.
   Prenumeration |Välj den Azure-prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i din prenumeration eller ange ett namn för att skapa en ny resursgrupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. Du behöver *deltagar* -eller *ägar* rollen för att använda en befintlig resurs grupp.  Mer information om åtkomst finns i [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md).
   Region | Välj den Azure-region som är närmast dina användare och data resurserna för att skapa din arbets yta.

1. När du är klar med konfigurationen av arbets ytan väljer du **Granska + skapa**. Du kan också använda avsnitten [nätverk](#networking) och [Avancerat](#advanced) för att konfigurera fler inställningar för arbets ytan.

1. Granska inställningarna och gör eventuella ytterligare ändringar eller korrigeringar. När du är nöjd med inställningarna väljer du **skapa**.

   > [!Warning] 
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

### <a name="networking"></a>Nätverk  

> [!IMPORTANT]  
> Mer information om hur du använder en privat slut punkt och ett virtuellt nätverk med din arbets yta finns i [nätverks isolering och sekretess](how-to-enable-virtual-network.md).
    
1. Standard nätverks konfigurationen är att använda en __offentlig slut punkt__som är tillgänglig på det offentliga Internet. Om du vill begränsa åtkomsten till din arbets yta till en Azure-Virtual Network du har skapat, kan du i stället välja __privat slut punkt__ som __anslutnings metod__och sedan använda __+ Lägg till__ för att konfigurera slut punkten. 
    
   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Val av privat slut punkt":::  

1. I formuläret __skapa privat slut punkt__ anger du den plats, det namn och det virtuella nätverk som ska användas. Om du vill använda slut punkten med en Privat DNS zon väljer du __integrera med privat DNS-zon__ och väljer zonen i fältet __privat DNS zon__ . Välj __OK__ för att skapa slut punkten.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Val av privat slut punkt":::   

1. När du är färdig med konfigurationen av nätverk kan du välja __Granska + skapa__eller gå vidare till den valfria __avancerade__ konfigurationen. 

    > [!WARNING]    
    > När du skapar en privat slut punkt skapas en ny Privat DNS zon med namnet __privatelink.API.azureml.MS__ . Innehåller en länk till det virtuella nätverket. Om du skapar flera arbets ytor med privata slut punkter i samma resurs grupp, kan endast det virtuella nätverket för den första privata slut punkten läggas till i DNS-zonen. Använd följande steg för att lägga till poster för de virtuella nätverk som används av ytterligare arbets ytor/privata slut punkter: 
    >   
    > 1. I [Azure Portal](https://portal.azure.com)väljer du den resurs grupp som innehåller arbets ytan. Välj sedan Privat DNS zon resurs med namnet __privatelink.API.azureml.MS__.    
    > 2. I __inställningarna__väljer du __virtuella nätverks länkar__. 
    > 3. Välj __Lägg till__. På sidan __Lägg till virtuell nätverks länk__ anger du ett unikt __länk namn__och väljer sedan det __virtuella nätverk__ som ska läggas till. Välj __OK__ för att lägga till nätverks länken.    
    >   
    > Mer information finns i [Azures DNS-konfiguration för privat slut punkt](/azure/private-link/private-endpoint-dns).   

### <a name="vulnerability-scanning"></a>Sårbarhets sökning

Azure Security Center erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Du bör tillåta Azure Security Center att söka igenom dina resurser och följa rekommendationerna. Mer information finns i  [Azure Container Registry avbildnings genomsökning av Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) och [integrering med Azure Kubernetes Services med Security Center](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

### <a name="advanced"></a>Avancerat    

Som standard lagras mått och metadata för arbets ytan i en Azure Cosmos DB-instans som Microsoft underhåller. Dessa data är krypterade med Microsoft-hanterade nycklar.  

Om du vill begränsa de data som Microsoft samlar in på din arbets yta väljer du __arbets ytan hög affärs påverkan__. Mer information om den här inställningen finns i [kryptering i vila](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]  
> Du kan bara välja hög påverkan på verksamheten när du skapar en arbets yta. Du kan inte ändra den här inställningen när du har skapat arbets ytan.   
Om du använder __Enterprise__ -versionen av Azure Machine Learning kan du i stället ange en egen nyckel. Om du gör det skapas Azure Cosmos DB-instansen som lagrar mått och metadata i din Azure-prenumeration. Använd följande steg för att använda din egen nyckel:    

> [!IMPORTANT]  
> Innan du följer de här stegen måste du först utföra följande åtgärder:   
>   
> 1. Auktorisera __Machine Learning-appen__ (i identitets-och åtkomst hantering) med deltagar behörigheter för din prenumeration.  
> 1. Följ stegen i [Konfigurera Kundhanterade nycklar](/azure/cosmos-db/how-to-setup-cmk) för att:   
>     * Registrera Azure Cosmos DB-providern   
>     * Skapa och konfigurera en Azure Key Vault 
>     * Generera en nyckel  
>   
>     Du behöver inte skapa Azure Cosmos DB-instansen manuellt, en skapas automatiskt när du skapar arbets ytan. Den här Azure Cosmos DB-instansen skapas i en separat resurs grupp med hjälp av ett namn baserat på det här mönstret: `<your-workspace-resource-name>_<GUID>` .   
>   
> Du kan inte ändra den här inställningen när du har skapat arbets ytan. Om du tar bort Azure Cosmos DB som används av din arbets yta, måste du också ta bort arbets ytan som använder den.

1. Välj __Kundhanterade nycklar__och välj sedan __knappen Klicka för att välja nyckel__.   

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Val av privat slut punkt":::   

1. I formuläret __Välj nyckel från Azure Key Vault__ väljer du en befintlig Azure Key Vault, en nyckel som den innehåller och nyckelns version. Den här nyckeln används för att kryptera data som lagras i Azure Cosmos DB. Använd slutligen knappen __Välj__ för att använda den här nyckeln. 

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Val av privat slut punkt":::

### <a name="download-a-configuration-file"></a>Hämta en konfigurations fil

1. Om du kommer att skapa en [beräknings instans](tutorial-1st-experiment-sdk-setup.md#azure)hoppar du över det här steget.

1. Om du planerar att använda kod i din lokala miljö som hänvisar till den här arbets ytan väljer du  **hämta config.jspå** i **översikts** avsnittet i arbets ytan.  

   ![Ladda ned config.jspå](./media/how-to-manage-workspace/configure.png)
   
   Placera filen i katalog strukturen med dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog. När du skapar en beräknings instans läggs den här filen till i rätt katalog på den virtuella datorn åt dig.
## <a name="find-a-workspace"></a><a name="view"></a>Hitta en arbets yta

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I det övre Sök fältet skriver du **Machine Learning**.  

1. Välj **Machine Learning**.

   ![Sök efter Azure Machine Learning arbets yta](./media/how-to-manage-workspace/find-workspaces.png)

1. Titta igenom listan med arbets ytor som har hittats. Du kan filtrera baserat på prenumeration, resurs grupper och platser.  

1. Välj en arbets yta för att visa dess egenskaper.

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

I [Azure Portal](https://portal.azure.com/)väljer du **ta bort**  överst i arbets ytan som du vill ta bort.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Val av privat slut punkt":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbets ytan

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Tar bort Azure Container Registry

I arbets ytan Azure Machine Learning används Azure Container Registry (ACR) för vissa åtgärder. En ACR-instans skapas automatiskt när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

I den fullständiga självstudien får du lära dig hur du använder en arbets yta för att skapa, träna och distribuera modeller med Azure Machine Learning.

> [!div class="nextstepaction"]
> [Självstudie: träna modeller](tutorial-train-models-with-aml.md)
