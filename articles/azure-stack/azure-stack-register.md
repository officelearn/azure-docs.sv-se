---
title: Registrera Azure Stack | Microsoft Docs
description: Registrera Azure stacken med din Azure-prenumeration.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registrera Azure stacken med din Azure-prenumeration

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan registrera [Azure Stack](azure-stack-poc.md) med Azure att hämta marketplace-objekt från Azure och Ställ in commerce rapporterar tillbaka till Microsoft. 

> [!NOTE]
>Registrering rekommenderas eftersom du kan testa viktiga Azure Stack-funktioner, t.ex marketplace syndikering och användningsrapportering. När du har registrerat Azure Stack rapporterat användning till Azure handel. Du kan se den under den prenumeration som du använde för registrering. Azure-stacken Development Kit användare debiteras inte för användning rapporterar.
>


## <a name="get-azure-subscription"></a>Skaffa Azure-prenumeration

Innan du registrerar Azure stacken med Azure måste du ha:

- Prenumerations-ID för en Azure-prenumeration. Om du vill hämta ID, logga in på Azure, klickar du på **fler tjänster** > **prenumerationer**, klickar du på den prenumeration som du vill använda, och under **Essentials** hittar du i **Prenumerations-ID**. Kina, Tyskland och US government molnprenumerationer stöds inte för närvarande.
- Användarnamn och lösenord för ett konto som är en ägare till prenumerationen (MSA/2FA konton stöds).
- Azure Active Directory för Azure-prenumerationen. Du hittar den här katalogen i Azure genom att hovra över din avatar i det övre högra hörnet i Azure-portalen. 
- [Registrerad resursprovider Azure Stack](#register-azure-stack-resource-provider-in-azure).

Om du inte har en Azure-prenumeration som uppfyller dessa krav, kan du [skapa ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/en-us/free/?b=17.06). Registrera Azure Stack ådrar sig utan kostnad på din Azure-prenumeration.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registerresursleverantören för Azure-stacken i Azure
> [!NOTE] 
> Det här steget behöver bara utföras en gång i en Azure-Stack-miljö.
>

1. Starta Powershell ISE som administratör.
2. Logga in på Azure-konto som äger den Azure-prenumerationen med EnvironmentName - parameter har angetts till ”AzureCloud”.
3. Registrera Azure-resursprovider ”Microsoft.AzureStack”.

Exempel: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure

> [!NOTE]
>Dessa steg måste utföras på värddatorn.
>

1. [Installera PowerShell för Azure-stacken](azure-stack-powershell-install.md). 
2. Kopiera den [RegisterWithAzure.psm1 skriptet](https://go.microsoft.com/fwlink/?linkid=842959) till en mapp (exempelvis C:\Temp).
3. Starta PowerShell ISE som administratör och importera modulen RegisterWithAzure.    
4. Kör modulen Lägg till AzsRegistration från RegisterWithAzure.psm1-skriptet. Ersätt följande platshållare: 
    - *YourCloudAdminCredential* är ett PowerShell-objekt som innehåller autentiseringsuppgifter för domänen för domain\cloudadmin (för SDK-paket, det är azurestack\cloudadmin).
    - *YourAzureSubscriptionID* är ID för Azure-prenumeration som du vill använda för att registrera Azure stacken.
    - *YourAzureDirectoryTenantName* är namnet på katalogen Azure-klient som är associerade med din Azure-prenumeration. Registrera resursen kommer att skapas i directory-klient. 
    - *YourPrivilegedEndpoint* är namnet på den [Privilegierade slutpunkt](azure-stack-privileged-endpoint.md).

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. Ange dina autentiseringsuppgifter för Azure-prenumeration i fönstret popup-inloggning.

## <a name="verify-the-registration"></a>Verifiera registrering

1. Logga in på administratörsportalen (https://adminportal.local.azurestack.external).
2. Klicka på **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**.
3. Om du ser en lista med objekt som är tillgängliga från Azure (till exempel WordPress) lyckades aktiveringen.

> [!NOTE]
> När registreringen är klar, visas inte längre den aktiva varningen för att registrera inte.

## <a name="next-steps"></a>Nästa steg

[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)

