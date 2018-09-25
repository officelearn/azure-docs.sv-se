---
title: Skapa och koppla tjänstslutpunktsprinciper - Azure-portalen | Microsoft Docs
description: I den här artikeln lär du dig hur du ställer in och associerade tjänstslutpunktsprinciper med Azure portal.
services: virtual-network
documentationcenter: virtual-network
author: anithaa
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: anithaa
ms.openlocfilehash: 8c750937cd45e104e754335f7da51d813b8dc7ba
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999655"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Skapa, ändra eller ta bort tjänstslutpunktsprincipen med Azure portal

Tjänstslutpunktsprinciper kan du filtrera trafik i virtuella nätverk till specifika Azure-resurser via tjänstslutpunkter. Om du inte är bekant med tjänstslutpunktsprinciper [tjänstöversikt endpoint principer](virtual-network-service-endpoint-policies-overview.md) vill veta mer.

 I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tjänstslutpunktsprincip
> * Skapa en tjänstslutpunktens principdefinition
> * Skapa ett virtuellt nätverk med ett undernät
> * Associera en tjänstslutpunktsprincip till ett undernät

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Skapa en tjänstslutpunktsprincip

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. I sökrutan skriver du ”tjänst endpoint principen” och välj **tjänsten endpoint princip (förhandsversion)** och välj sedan **skapa**.
3. Ange eller Välj följande information i **grunderna** 

   - Prenumeration: Välj din prenumeration för principen.    
   - Resursgrupp: Välj **Skapa nytt** och ange *myResourceGroup*.     
   - Namn: myEndpointPolicy
   - Plats: Västra centrala USA     
 
   ![Skapa princip grunderna för tjänstslutpunkt](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Ange eller Välj följande information i **Principdefinitioner**

   - Klicka på **+ Lägg till en resurs**anger, eller Välj följande information, acceptera standardinställningarna för återstående inställningar och klickar på **Lägg till**.  
   - Omfång: Välj **enda konto** eller **alla konton i prenumerationen** eller **alla konton i resursgruppen**.    
   - Prenumeration: Välj din prenumeration för storage-konto. Princip- och storage-konton kan finnas i olika prenumerationer.   
   - Resursgrupp: Välj en resursgrupp. Krävs om anges som ”alla konton i resursgruppen” eller ”enda konto”.  
   - Resurs: mystorageaccountportal    
   - Klicka på **+ Lägg till en resurs** att fortsätta att lägga till fler resurser.
   
   ![Skapa tjänstslutpunkt principdefinitioner](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Valfritt: Ange eller Välj följande information i **flikar**
   
   - Nyckel: Välj din nyckel för principen. Exempel: Avd     
   - Värde: Ange värdepar för nyckeln. Exempel: finans

6. Välj **granska + skapa**. Verifiera information och klicka på **skapa**. Om du vill göra ytterligare redigeringar, klickar du på **föregående**. 

   ![Skapa princip sista verifieringar för tjänstslutpunkt](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Visa endpoint principer 

1. I den *alla tjänster* rutan i portalen, börjar du skriva *tjänstslutpunktsprinciper*. Välj **tjänsten Endpoint Policies(Preview)**.
2. Under **prenumerationer**, väljer din prenumeration och resursgrupp, så som visas i följande bild

   ![Visa princip](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Välj principen och klicka på **Principdefinitioner** att visa eller lägga till flera principdefinitioner.

   ![Visa principdefinitioner](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Välj **associerade undernät** visa undernäten principen är associerad. Du associerar principen till ett undernät genom att klicka på ”Gå till virtuellt nätverk i samma region”.

   ![Visa associerade undernät](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Associera en princip till ett undernät

>[!WARNING] 
> Se till att alla resurser som nås från undernätet för den valda tjänsten har lagts till principen innan du kan koppla principen. När principen är associerad, åtkomst till resurserna som anges i principen ska tillåtas för slutpunkten regioner för tjänsten. 

Innan du kan associera en princip till ett undernät, måste du skapa ett virtuellt nätverk och undernät och sedan associera principen med undernätet:

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Under **Skapa virtuellt nätverk** anger du eller väljer följande information, accepterar standardinställningarna för återstående inställningar och välj sedan **Skapa**:
   - Namn: myVirtualNetwork      
   - Adressutrymme: 10.0.0.0/16      
   - Prenumeration: Välj din prenumeration. Det bör vara i samma prenumeration som det virtuella nätverket     
   - Resursgrupp: Välj **Använd befintlig** och välj sedan *myResourceGroup*     
   - Plats: Västra centrala USA     
   - Undernätsnamn: privata     
   - -Adressintervall: 10.0.0.0/24
     
4. I rutan **Sök efter resurser, tjänster och dokument** högst upp i portalen och börjar du skriva *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
5. Under **inställningar**väljer **undernät** och välj sedan **privata**.
6. I följande bild visas Välj **tjänstslutpunkter**väljer **Microsoft.Storage**väljer **Tjänstslutpunktsprinciper**väljer  **myEndpointPolicy**, och välj sedan **spara**:

   ![Associera princip](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>Åtkomst till tjänstresurser i andra regioner får från det här undernätet, baserat på Nätverkssäkerhetsgrupper (NSG). Om du vill begränsa åtkomsten till endast slutpunkten regioner, begränsar du NSG: er till endast tjänsttrafiken i slutpunkten regioner. Mer information om hur du skapar NSG: er med tjänsttaggar per region finns i [tjänsttaggar för NSG Azure.](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)

I exemplet nedan är NSG begränsad till endast Azure Storage-resurser i västra centrala USA och västra USA 2, med en regel för ”neka alla” som en regel med lägre prioritet.

![Neka alla NSG](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Nästa steg
I den här självstudien får du skapade en tjänstslutpunktsprincip och associerat den till ett undernät. Läs mer om tjänstslutpunktsprinciper i [tjänstöversikt endpoint principer.](virtual-network-service-endpoint-policies-overview.md)

