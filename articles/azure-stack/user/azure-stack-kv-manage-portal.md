---
title: "Hantera Key Vault i Azure-stacken med hjälp av portalen | Microsoft Docs"
description: "Lär dig att hantera Key Vault i Azure-stacken med hjälp av portalen"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: d263cbcc81be37eaedfdb771436fd13ef25362f8
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Hantera Key Vault i Azure-stacken med hjälp av portalen

Du kan hantera Key Vault i Azure-stacken med hjälp av Azure Stack-portalen. Den här artikeln hjälper dig att komma igång med att skapa och hantera nyckelvalvet i Azure-stacken. 

## <a name="prerequisites"></a>Krav  

Du måste prenumerera på ett erbjudande som innehåller Azure Key Vault-tjänsten.
 
## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv 

1. Logga in på den [användarportalen](https://portal.local.azurestack.external).  

2. Från instrumentpanelen, Välj **ny** > **säkerhet + identitet** > **Nyckelvalvet**.  

    ![Key Vault skärmen](media/azure-stack-kv-manage-portal/image1.png)  

3. I den **skapa Nyckelvalvet** och tilldela en **namn** för ditt valv. Valvet namn får bara innehålla alfanumeriska tecken och bindestreck (-) specialtecken. De får inte börja med en siffra.  

4. Välj en **prenumeration** från listan över tillgängliga prenumerationer. Alla prenumerationer som erbjuder Key Vault-tjänsten visas i den nedrullningsbara listan.  

5. Välj en befintlig **resursgruppen** eller skapa en ny.  

6. Välj den **prisnivå**.  
    >[!NOTE]
    > Nyckeln valv i Azure-stacken Development Kit supporten **Standard** endast SKU: er.

7. Välj något av de befintliga **åtkomstprinciper** eller skapa en ny. En åtkomstprincip kan du bevilja behörigheter för en användare, program eller en säkerhetsgrupp att utföra åtgärder med det här valvet.  

8. Dessutom kan du välja en **avancerade åtkomstprincip** åtkomst till Resource Manager t.ex. åtkomst till virtuella datorer (VM) för distribution för malldistribution för att aktivera funktioner och åtkomst till Azure Disk Encryption för volymkryptering. 
  
9.  När du har konfigurerat inställningarna väljer **OK**, och välj sedan **skapa**. Detta startar distributionen nyckelvalvet. 

## <a name="manage-keys-and-secrets"></a>Hantera nycklar och hemligheter

När du har skapat ett valv, kan du använda följande steg för att skapa och hantera nycklar och hemligheter i valvet.

### <a name="create-a-key"></a>Skapa en nyckel

1. Logga in på den [användarportalen](https://portal.local.azurestack.external).  

2. Från instrumentpanelen, Välj **alla resurser**, Välj nyckelvalv som du skapade tidigare och väljer sedan den **nycklar** panelen.  

3. I den **nycklar** väljer **Lägg till**. 

4. I den **skapar du en nyckel** rutan från listan över **alternativ**, Välj den metod som du vill använda för att skapa en nyckel. Du kan **generera** en ny nyckel **överför** en befintlig nyckel eller använda **återställa säkerhetskopiering** att välja en säkerhetskopia av en nyckel.  

5. Ange en **namn** för din nyckel. Namnet får innehålla endast alfanumeriska tecken och specialtecken bindestreck (-).  

6. Alternativt kan du konfigurera den **ange aktiveringsdatumet** och **ange förfallodatum** värden för din nyckel.  

7. Välj **skapa** att starta distributionen.  

När nyckeln har skapats kan du välja den under **nycklar** och visa eller ändra dess egenskaper. Egenskapsavsnittet innehåller den **nyckelidentifierare**, vilket är en identifierare URI (Uniform Resource) som du kan komma åt den här nyckeln externa program. För att begränsa åtgärder på den här nyckeln, konfigurerar du inställningarna under **tillåtna åtgärder**.

![URI-nyckel](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>Skapa en hemlighet 

1. Logga in på den [användarportalen](https://portal.local.azurestack.external).  
2. Från instrumentpanelen, Välj **alla resurser**, Välj nyckelvalv som du skapade tidigare och väljer sedan den **hemligheter** panelen.  

3. Under **hemligheter**väljer **Lägg till**.  

4. Under **skapa en hemlighet**, från listan över **överför alternativ**, Välj ett alternativ som du vill skapa en hemlighet. Du kan skapa en hemlighet **manuellt** om du anger värdet för hemligt eller överför en **certifikat** från din lokala dator.  

5. Ange en **namn** för hemligheten. Det hemliga namnet kan innehålla endast alfanumeriska tecken och bindestreck (-) specialtecken.  

6. Du kan också ange den **innehållstyp**, och konfigurera värden för **ange aktiveringsdatumet** och **ange förfallodatum** för hemligheten.  

7. Välj **skapa** att starta distributionen.  

När hemligheten som har skapats kan du välja den under **hemligheter** och visa eller ändra dess egenskaper. Egenskapsavsnittet innehåller en **hemlighet identifierare**, vilket är en URI som du kan komma åt den här hemligheten externa program. 

![URI-hemlighet](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Nästa steg
* [Distribuera en virtuell dator genom att hämta lösenord som lagras i Key Vault](azure-stack-kv-deploy-vm-with-secret.md) 
* [Distribuera en virtuell dator med certifikatet i Nyckelvalvet](azure-stack-kv-push-secret-into-vm.md)     


