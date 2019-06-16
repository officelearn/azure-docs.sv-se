---
title: Registrera för Azure NetApp Files| Microsoft Docs
description: Beskriver hur du registrera dig för att använda Azure NetApp-filer.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299215"
---
# <a name="register-for-azure-netapp-files"></a>Registrera för Azure NetApp Files

> [!IMPORTANT] 
> Innan du registrerar resursprovidern Azure NetApp filer, måste du har fått ett e-postmeddelande från Azure NetApp Files-teamet som bekräftar att du har fått åtkomst till tjänsten. 

I den här artikeln lär du dig hur du registrera dig för Azure NetApp filer så att du kan börja använda tjänsten.

## <a name="waitlist"></a>Skicka en väntelista begäran om åtkomst till tjänsten

1. Skicka en väntelista begäran om åtkomst till Azure NetApp Files-tjänsten via den [Azure NetApp Files Väntelista överföringssidan](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Väntelista registreringen garanterar inte omedelbar-åtkomst. 

2. Vänta tills en officiell bekräftelsemeddelandet från teamet för Azure NetApp filer innan du fortsätter med andra aktiviteter. 

## <a name="resource-provider"></a>Registrera Resursprovidern NetApp

För att kunna använda tjänsten behöver du registrera Azure-resursprovidern för Azure NetApp Files.

> [!NOTE] 
> Du kommer att kunna registrera har Resursprovidern NetApp även om du inte beviljas åtkomst för tjänsten. Men utan åtkomstauktorisering avvisas alla Azure-portalen eller API-begäran för att skapa ett NetApp-konto eller någon annan Azure NetApp Files-resurs med följande fel:  
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

    Om du inte ser funktionsnamnet `Microsoft.NetApp/ANFGA`, du har inte åtkomst till tjänsten. Stoppa i det här steget. Följ instruktionerna i [skicka en väntelista begäran om åtkomst till tjänsten](#waitlist) att begära service åtkomst innan du fortsätter. 

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