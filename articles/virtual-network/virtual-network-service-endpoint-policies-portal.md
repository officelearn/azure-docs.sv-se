---
title: Skapa och associera tjänstslutpunktsprinciper - Azure-portal
titlesuffix: Azure Virtual Network
description: I den här artikeln får du lära dig hur du konfigurerar och associerade tjänstslutpunktsprinciper med hjälp av Azure-portalen.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651272"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Skapa, ändra eller ta bort tjänstslutpunktsprincipen med hjälp av Azure-portalen

Med tjänstslutpunktsprinciper kan du filtrera virtuell nätverkstrafik till specifika Azure-resurser, över tjänstslutpunkter. Om du inte är bekant med tjänstslutpunktsprinciper läser du [översikt över tjänstslutpunktsprinciper](virtual-network-service-endpoint-policies-overview.md) om du vill veta mer.

 I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en princip för tjänstslutpunkt
> * Skapa principdefinition för tjänstslutpunkt
> * Skapa ett virtuellt nätverk med ett undernät
> * Associera en tjänstslutpunktsprincip till ett undernät

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Skapa en princip för tjänstslutpunkt

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. I sökfönstret skriver du "tjänstslutpunktsprincip" och väljer **Princip för Tjänstslutpunkt** och väljer sedan **Skapa**.

![Skapa princip för tjänstens slutpunkt](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Ange, eller välj, följande information i **Grunderna** 

   - Prenumeration : Välj din prenumeration för policy
   - Resursgrupp: Välj **Skapa nytt** och ange *myResourceGroup*
   - Namn : myEndpointPolicy
   - Plats : Centrala USA
 
   ![Skapa grundläggande princip för tjänstslutpunkt](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Välj **+ Lägg till** under **Resurser** och ange eller markera följande information i Lägg till **ett resursfönster**

   - Tjänst: Endast **Microsoft.Storage** är tillgängligt med tjänstslutpunktsprinciper
   - Omfattning: Välj ett av **ett enda konto,** **Alla konton i prenumeration** och Alla konton i **resursgruppen**
   - Prenumeration : Välj din prenumeration för lagringskonto. Princip- och lagringskonton kan finnas i olika prenumerationer.
   - Resursgrupp : Välj resursgrupp. Obligatoriskt, om scopet anges som "Alla konton i resursgruppen" eller "Ett enda konto".  
   - Resurs: Välj din Azure Storage-resurs under den valda prenumerations- eller resursgruppen
   - Klicka på **Lägg till-knappen** längst ned för att slutföra lägger till resursen

   ![Principdefinition för tjänstslutpunkt - resurs](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Lägg till fler resurser genom att upprepa ovanstående steg efter behov

5. Valfritt: Ange eller markera följande information i **Taggar:**
   
   - Nyckel : Välj din nyckel för principen. Ex: Avd     
   - Värde : Ange värdepar för nyckeln. Ex: Ekonomi

6. Välj **Granska + Skapa**. Validera informationen och klicka på **Skapa**. Om du vill göra ytterligare ändringar klickar du på **Föregående**. 

   ![Skapa slutliga valideringar av tjänstslutpunktsprincip](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Visa principer för slutpunkt 

1. Börja skriva *tjänstslutpunktsprinciper*i rutan *Alla tjänster* i portalen . Välj **tjänstslutpunktsprinciper**.
2. Under **Prenumerationer**väljer du din prenumeration och resursgrupp, som visas i följande bild

   ![Visa princip](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Välj principen och klicka på **Principdefinitioner** om du vill visa eller lägga till fler principdefinitioner.

   ![Visa principdefinitioner](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Välj **Associerade undernät** om du vill visa de undernät som principen är associerad med. Om inget undernät är associerat ännu följer du instruktionerna i nästa steg.

   ![Associerade undernät](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Associera en princip till ett undernät

>[!WARNING] 
> Kontrollera att alla resurser som används från undernätet läggs till i principdefinitionen innan principen associeras med det angivna undernätet. När principen är associerad tillåts endast åtkomst till de *tillåtna angivna* resurserna över tjänstslutpunkter. 
>
> Se också till att det inte finns några hanterade Azure-tjänster i undernätet som associeras till tjänstslutpunktsprincipen

- Innan du kan associera en princip till ett undernät måste du skapa ett virtuellt nätverk och ett undernät. Mer hjälp med detta finns i artikeln [Skapa ett virtuellt nätverk.](./quick-create-portal.md)

- När du har det virtuella nätverket och undernätet är konfigurerat slutpunkter för virtual network service för Azure Storage. På bladet Virtuellt nätverk väljer du **Tjänstslutpunkter**och väljer **microsoft.Storage** i nästa fönster och **väljer** du önskat virtuellt nätverk eller undernät

- Nu kan du antingen välja princip för tjänstslutpunkt från listrutan i fönstret ovan om du redan har skapat tjänstslutpunktsprinciper innan du konfigurerar tjänstslutpunkt för undernätet som visas nedan

    ![Associera undernät när du skapar tjänstslutpunkt](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- Eller om du associerar tjänstslutpunktsprinciper efter att tjänstslutpunkter redan har konfigurerats kan du välja att associera undernätet från bladet Tjänstslutpunktsprincip genom att navigera till fönstret **Associerade undernät** enligt nedan

    ![Associera undernät via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Åtkomst till Azure Storage-resurser i alla regioner begränsas enligt tjänstslutpunktsprincipen från det här undernätet.

## <a name="next-steps"></a>Nästa steg
I den här självstudien skapade du en princip för tjänstslutpunkt och associerade den till ett undernät. Mer information om tjänstslutpunktsprinciper finns i [översikt över tjänstslutpunktsprinciper.](virtual-network-service-endpoint-policies-overview.md)
