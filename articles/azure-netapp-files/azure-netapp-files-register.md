---
title: Registrera för Azure NetApp Files| Microsoft Docs
description: Beskriver hur du skickar en begäran om att registrera i Azure NetApp Files-tjänsten.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 0a5483cd5a6316cb5d6cba3a110ca757eb2171c8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104003"
---
# <a name="register-for-azure-netapp-files"></a>Registrera för Azure NetApp Files
Innan du använder Azure NetApp filer behöver du skicka en begäran om att registrera i Azure NetApp Files-tjänsten.  Efter registreringen registrerar du dig för att använda tjänsten.

## <a name="request-to-enroll-in-the-service"></a>Begäran om att registrera i tjänsten
Du behöver delta i programmet för allmänt tillgängliga förhandsversioner och vara med i listan över tillåtna för att få åtkomst till Azure-resursprovidern för Microsoft.NetApp. Mer information om att gå med i programmet för allmänt tillgängliga förhandsversioner finns på [registreringssidan för allmänt tillgängliga förhandsversioner för Azure NetApp Files](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Registrera resursprovidern för NetApp

För att kunna använda tjänsten behöver du registrera Azure-resursprovidern för Azure NetApp Files. 

1. På Azure-portalen klickar du på Azure Cloud Shell-ikonen i det övre högra hörnet:

      ![Azure Cloud Shell-ikonen](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Om du har flera prenumerationer på ditt Azure-konto väljer du det som finns i listan över godkända för Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. I Azure Cloud Shell-konsolen anger du följande kommando för att kontrollera att prenumerationen är med i listan över tillåtna:
    
        az feature list | grep NetApp

   Utdata från kommandot visas på följande sätt:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` är ditt prenumerations-ID.

4. I Azure Cloud Shell-konsolen anger du följande kommando för att registrera Azure-resursprovidern: 
    
        az provider register --namespace Microsoft.NetApp --wait

   Parametern `--wait` instruerar konsolen att vänta tills registreringen är klar. Registreringsprocessen kan ta lite tid att slutföra.

5. I Azure Cloud Shell-konsolen anger du följande kommando för att kontrollera att Azure-resursprovidern har registrerats: 
    
        az provider show --namespace Microsoft.NetApp

  Utdata från kommandot visas på följande sätt:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` är ditt prenumerations-ID.  `state`-parametervärdet anger `Registered`.

6. På Azure-portalen klickar du på bladet **Prenumerationer**.
7. På bladet Prenumerationer klickar du på ditt prenumerations-ID. 
8. I inställningarna för prenumerationen klickar du på **Resursprovidrar** för att kontrollera att Microsoft.NetApp-providern anger statusen Registrerad: 

      ![Registrerad Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Nästa steg  

[Skapa ett NetApp-konto](azure-netapp-files-create-netapp-account.md)