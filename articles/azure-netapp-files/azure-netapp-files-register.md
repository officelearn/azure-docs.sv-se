---
title: Registrera för Azure NetApp Files| Microsoft Docs
description: Beskriver hur du registrerar dig för att använda Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79274065"
---
# <a name="register-for-azure-netapp-files"></a>Registrera för Azure NetApp Files

> [!IMPORTANT] 
> Innan du registrerar Azure NetApp Files Resource Provider måste du ha fått ett e-postmeddelande från Azure NetApp Filess teamet som bekräftar att du har beviljats åtkomst till tjänsten. 

I den här artikeln får du lära dig hur du registrerar dig för Azure NetApp Files så att du kan börja använda tjänsten.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Skicka en Waitlist-begäran om åtkomst till tjänsten

1. Skicka en Waitlist-begäran om att få åtkomst till Azure NetApp Files tjänsten via sidan för att [skicka Azure NetApp Files Waitlist](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Waitlist-registrering garanterar inte omedelbar åtkomst till tjänsten. 

2. Vänta en officiell bekräftelse via e-post från Azure NetApp Files-teamet innan du fortsätter med andra uppgifter. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Registrera resursprovidern för NetApp

För att kunna använda tjänsten behöver du registrera Azure-resursprovidern för Azure NetApp Files.

> [!NOTE] 
> Du kommer att kunna registrera NetApp-resurs leverantören trots att den inte har beviljats åtkomst till tjänsten. Men utan åtkomst behörighet avvisas eventuellt Azure Portal-eller API-begäran för att skapa ett NetApp-konto eller någon annan Azure NetApp Files resurs med följande fel:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. På Azure-portalen klickar du på Azure Cloud Shell-ikonen i det övre högra hörnet:

      ![Azure Cloud Shell-ikonen](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Om du har flera prenumerationer på ditt Azure-konto väljer du det som finns i listan över godkända för Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. I Azure Cloud Shell-konsolen anger du följande kommando för att kontrollera att prenumerationen är med i listan över tillåtna:
    
        az feature list | grep NetApp

   Utdata från kommandot visas på följande sätt:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` är ditt prenumerations-ID.

    Om du inte ser funktions namnet `Microsoft.NetApp/ANFGA`har du inte åtkomst till tjänsten. Stoppa i det här steget. Följ anvisningarna i [skicka en Waitlist-begäran om åtkomst till tjänsten](#waitlist) för att begära åtkomst till tjänsten innan du fortsätter. 

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