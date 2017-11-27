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
ms.date: 11/21/2017
ms.author: erikje
ms.openlocfilehash: 6ce8f86592ece59e338578be86c2cb673c35dbc1
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
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

Om du inte har en Azure-prenumeration som uppfyller dessa krav, kan du [skapa ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/en-us/free/?b=17.06). Registrera Azure Stack ådrar sig utan kostnad på din Azure-prenumeration.


## <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure

> [!NOTE]
> De här stegen måste köras från en dator som har åtkomst till Privilegierade slutpunkten. Det skulle vara värddatorn för Azure-stacken Development Kit. Kontakta Azure Stack-operatorn om du använder ett integrerat system.
>

1. Öppna ett PowerShell-konsolen som administratör och [installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).  

2. Lägg till Azure-konto som ska användas för att registrera Azure stacken. Det gör du genom att köra den `Add-AzureRmAccount` cmdlet med parametern EnvironmentName inställd på ”AzureCloud”. Du uppmanas att ange dina autentiseringsuppgifter för Azure-konto och du kan behöva använda 2-faktor-autentisering baserat på konfigurationen för ditt konto. 

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Om du har flera prenumerationer kör du följande kommando för att välja den du vill använda:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Registrera resursprovidern AzureStack i din Azure-prenumeration. Det gör du genom att köra följande kommando:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Ta bort alla befintliga versioner av Powershell-moduler som motsvarar registrering och [ladda ned den senaste versionen av den från GitHub](azure-stack-powershell-download.md).  

6. Navigera till mappen ”registrering” från katalogen ”AzureStack-verktyg-master” som har skapats i föregående steg, och importera modulen ”.\RegisterWithAzure.psm1”:  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

7. Kör följande skript i samma PowerShell-sessionen. När du tillfrågas om autentiseringsuppgifter, ange `azurestack\cloudadmin` som användarnamn och lösenord är detsamma som som användes för den lokala administratören under distributionen.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.SubscriptionId `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Parameter | Beskrivning |
   | -------- | ------------- |
   | CloudAdminCredential | Moln-domänautentiseringsuppgifter som används för att [komma åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Användarnamnet är i formatet ”\<Azure Stack domän\>\cloudadmin”. Development kit anges användarnamnet till ”azurestack\cloudadmin”. Om du använder ett integrerat system kontaktar du din Azure Stack-operatorn för att få det här värdet.|
   | AzureSubscriptionId | Azure-prenumerationen som du använder för att registrera Azure stacken.|
   | AzureDirectoryTenantName | Namnet på katalogen Azure-klient som är associerad med din Azure-prenumeration. Registrera resursen kommer att skapas i directory-klient. |
   | PrivilegedEndpoint | En förkonfigurerad PowerShell fjärrkonsolen ger tillgång till funktioner som Logginsamling och andra post distributionsåtgärder. För development kit finns Privilegierade slutpunkten på den virtuella datorn ”AzS ERCS01”. Om du använder ett integrerat system kontaktar du din Azure Stack-operatorn för att få det här värdet. Mer information finns i den [med Privilegierade slutpunkten](azure-stack-privileged-endpoint.md) avsnittet.|
   | BillingModel | Vilken faktureringsmodell som tillämpas med din prenumeration. Tillåtna värden för den här parametern är-”kapacitet”, ”PayAsYouUse” och ”utveckling”. Det här värdet anges till ”utveckla” för development kit. Om du använder ett integrerat system kontaktar du din Azure Stack-operatorn för att få det här värdet. |

8. När skriptet har slutförts visas ett meddelande om ”Aktivera Azure Stack (det här steget kan ta upp till 10 minuter att slutföra)”. 

## <a name="verify-the-registration"></a>Verifiera registrering

1. Logga in på administratörsportalen (https://adminportal.local.azurestack.external).
2. Klicka på **fler tjänster** > **Marketplace Management** > **Lägg till från Azure**.
3. Om du ser en lista med objekt som är tillgängliga från Azure (till exempel WordPress) lyckades aktiveringen.

> [!NOTE]
> När registreringen är klar, visas inte längre den aktiva varningen för att registrera inte.

## <a name="next-steps"></a>Nästa steg

[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)

