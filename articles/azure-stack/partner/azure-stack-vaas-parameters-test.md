---
title: Testa parametrar för verifiering som en tjänst Azure Stack | Microsoft Docs
description: Referensavsnitt om konfigurationsfilen och test skicka parametrar för verifiering som en tjänst Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235096"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Parametrar för verifiering som en tjänst Azure Stack

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Innan du kör alla Test Suite från verifieringar som en tjänst (VaaS), Välj en lösning och skapa en testet.

- Logga in med ditt registrerade VaaS autentiseringsuppgifter för klienten.
- Skapa en ny lösning (genom att välja **lägga till lösning**) eller välja befintliga.
- Välj den **starta** knappen från den **tester** arbetsflöde panel.

> [!Note]  
> Skapa en ny lösning post för varje unikt datorns set/maskinvarukonfiguration som du verifierar och ett nytt test skickar för varje build-distribution på den dator uppsättningen.

Du måste ange de parametrar som krävs för att köra testerna i den **skicka testinformation** sidan. Ange dessa parametrar när du startar en ny testet eller kör verifieringen. De flesta av parametrarna har samma värden som du angav när du har distribuerat Azure Stack.

Du kan ange de värden som anges manuellt i den [parametrar datatypestable](#test-parameters), eller överför konfigurationsfil distribution som innehåller den fullständiga informationen för Azure Stack-stämpel. När du har överfört laddas portalen värdena från filen.

> [!Note]  
> Du kan söka efter och skriva parametervärden test genom att söka efter och läsa in konfigurationsfilen i portalen.

## <a name="export-the-test-parameters-using-powershell"></a>Exportera testparametrar med hjälp av PowerShell

1. Logga in på DVM dator eller en dator som har åtkomst till Azure Stack-miljön.
2. Öppna en upphöjd PowerShell-kommandotolk och kör:

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. Ladda upp **stampinfoproperties.json** VaaS-portalen.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>Leta upp test-parametrarna i konfigurationsfilen

Du kan också hitta parametervärden för testet genom att öppna FN **konfigurationsfilen**. Du hittar filen på följande sökväg på DVM-datorn:  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>Parametrarna för webbtestet 

| Parameter    | Beskrivning |
|-------------|-----------------|
| Azure Stack-version                      | Azure Stack bygger eller version tal som har distribuerats till exempel 1.0.170330.0 | 
| Klient-ID:t                              | Azure Active Directory-klient anges under distributionen av Azure Stack. Sök efter **AADTenant** i konfigurationsfilen och välj den **GUID** värde i den `Id` tagg. | 
| Region                                 | Azure Stack-distributionsregionen. Sök efter **Region** i konfigurationsfilen. | 
| Resurshanteraren för klient                | Klient-Azure Resource Manager-slutpunkten, till exempel `https://management.<ExternalFqdn>` | 
| Admin-resurshanteraren                 | Administratören Azure Resource Manager-slutpunkten. Exempel: `https://adminmanagement.<ExternalFqdn>` | 
| Externa FQDN                          | Externa FQDN för miljön. Sök efter **ExternalFqdn** i konfigurationsfilen. | 
| Klientorganisationsanvändaren                            | Azure Active Directory-Klientadministratör som antingen har redan etablerats eller affärsbehov som ska etableras som tjänstadministratör i Azure AD-katalog. Mer information om etablering klientkonto finns [lägga till ett nytt konto i Azure Stack-klient i Azure Active Directory](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad). Det här värdet används av testet för klient på åtgärder som till exempel distribuera mallar för att etablera resurser (Virtuella datorer, storage-konton osv) och köra arbetsbelastningar. | 
| Service-administratör             | Azure Active Directory-administratör för klienten för Azure AD-katalog som anges under distributionen av Azure Stack. Sök efter **AADTenant** i konfigurationen och väljer värdet i den `UniqueName` tagg i konfigurationsfilen. | 

## <a name="checks-before-starting-the-tests"></a>Kontrollerna innan du påbörjar testerna

Testerna utföra fjärråtgärder. Den dator som kör testerna måste ha åtkomst till Azure Stack-slutpunkter. Annars fungerar inte testet. Om du använder VaaS på lokal agent kan du använda dator där agenten körs. Du kan kontrollera att datorn har åtkomst till Azure Stack-punkter genom att köra följande kontroller.

1. Kontrollera att bas-URI: N kan nås. Öppna en kommandotolk eller bash-gränssnittet och kör följande kommando:

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Öppna en webbläsare och gå till `https://adminportal.<EXTERNALFQDN>` för att kontrollera att MAS-portalen kan nås.

3. Logga in med Azure AD-tjänsten administratör namn och lösenord värden som anges när du skapar i testet.

## <a name="next-steps"></a>Nästa steg

- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
