---
title: Skapa en Azure-datafabrik med Azure-portalen | Microsoft Docs
description: "Skapa en Azure-datafabrik för att kopiera data från ett molndatalager (Azure Blob Storage) till ett annat molndatalager (Azure SQL Databse)."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: b884d7f08311cc60dc3af500f552d525d23b91e6
ms.contentlocale: sv-se
ms.lasthandoff: 09/26/2017

---
# <a name="create-a-data-factory-using-the-azure-portal"></a>Skapa en datafabrik med hjälp av Azure-portalen
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda. 

I den här snabbstartsguiden använder du Azure-portalen för att skapa en datafabrik. När du har skapat datafabriken behöver du använda PowerShell, .NET SDK, Python SDK eller REST API och en datapipeline som kopierar data från ett källdatalager till ett måldatalager, precis som i andra snabbstarter. För närvarande kan du inte skapa pipelines i en datafabrik med hjälp av Azure-portalen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Här är de steg du utför som en del av den här snabbstarten:
1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. På bladet **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Bladet Ny datafabrik](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Vissa av stegen i den här snabbstarten förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. För närvarande kan du endast skapa V2-datafabriker i regionen **USA, östra**. Men beräkningen och datalagren som används i datafabriker kan vara i andra regioner. 
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
      
      > [!IMPORTANT]
      > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
      > 
      > Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.             
3. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
1. När datafabriken har skapats visas **Datafabrik**-bladet som på bilden.
   
   ![Datafabrikens startsida](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)


## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
