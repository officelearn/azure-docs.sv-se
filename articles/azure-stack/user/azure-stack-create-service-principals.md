---
title: Skapa ett huvudnamn för tjänsten för Azure-Stack | Microsoft Docs
description: Beskriver hur du skapar ett nytt huvudnamn för tjänsten som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 00bd606fc1b0d2c075789addd1b601becf7a011b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="provide-applications-access-to-azure-stack"></a>Ange program åtkomst till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

När ett program behöver tillgång till distribuera eller konfigurera resurser via Azure Resource Manager i Azure-stacken, kan du skapa en tjänstens huvudnamn, vilket är en autentiseringsuppgift för ditt program.  Du kan sedan delegera endast behörighet att som tjänstens huvudnamn.  

Exempelvis kanske en konfiguration hanteringsverktyg som använder Azure Resource Manager för att inventera Azure-resurser.  I detta scenario du skapar ett huvudnamn för tjänsten, bevilja läsarrollen som tjänstens huvudnamn och begränsa verktyget configuration management till skrivskyddad åtkomst. 

Tjänstens huvudnamn är bättre att köra appen enligt dina autentiseringsuppgifter eftersom:

* Du kan tilldela behörigheter till tjänsten huvudnamn som skiljer sig från din egen behörighet. Dessa behörigheter normalt begränsad till exakt vad appen behöver göra.
* Du behöver inte ändra appens autentiseringsuppgifterna ändrar dina ansvarsområden.
* Du kan använda ett certifikat för att automatisera autentisering när du kör ett oövervakat skript.  

## <a name="getting-started"></a>Komma igång

Beroende på hur du har distribuerat Azure-stacken, börja med att skapa en tjänst huvudnamn.  Det här dokumentet hjälper dig att skapa ett huvudnamn för tjänsten för både [Azure Active Directory (AD Azure)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) och [Active Directory Federation Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  När du har skapat tjänstens huvudnamn, en uppsättning steg som är gemensamma för både AD FS och Azure Active Directory används för att [delegera behörigheter](azure-stack-create-service-principals.md#assign-role-to-service-principal) till rollen.     

## <a name="create-service-principal-for-azure-ad"></a>Skapa tjänstens huvudnamn för Azure AD

Om du har distribuerat Azure stacken använder Azure AD som Identitetslagret kan skapa du tjänstens huvudnamn precis som för Azure.  Det här avsnittet visar hur du utför steg via portalen.  Kontrollera att du har den [nödvändiga behörigheter för Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) innan du börjar.

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn
I det här avsnittet skapar du ett program (tjänstens huvudnamn) i Azure AD som representerar ditt program.

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** > **App registreringar** > **Lägg till**   
3. Ange ett namn och en URL för programmet. Välj antingen **webbapp / API** eller **interna** för typ av program som du vill skapa. När du har angett värden, Välj **skapa**.

Du har skapat ett huvudnamn för tjänsten för ditt program.

### <a name="get-credentials"></a>Hämta autentiseringsuppgifter
Vid inloggning programmässigt, kan du använda ID: T för ditt program och en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Från **App registreringar** Markera programmet i Active Directory.

2. Kopiera **Program-ID:t** och lagra det i din programkod. Program i den [programexempel](#sample-applications) avsnittet refererar till det här värdet som klient-id.

     ![klient-ID](./media/azure-stack-create-service-principal/image12.png)
3. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

4. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet eftersom det inte går att hämta nyckeln senare. Du kan ange värdet för nyckeln med program-ID för att logga in som programmet. Lagra nyckelvärdet där programmet kan hämta det.

![sparad nyckel](./media/azure-stack-create-service-principal/image15.png)


När du är klar, Fortsätt till [tilldela en roll för ditt program](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Skapa tjänstens huvudnamn för AD FS
Om du har distribuerat Azure stacken med AD FS kan använda du PowerShell för att skapa ett huvudnamn för tjänsten, tilldela en roll för åtkomst och logga in från PowerShell den identiteten.

### <a name="before-you-begin"></a>Innan du börjar

[Hämta verktygen som krävs för att arbeta med Azure-stacken för den lokala datorn.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Importera identitet PowerShell-modulen
När du hämtar verktyg, navigera till mappen hämtade och importera identitet PowerShell-modulen med hjälp av följande kommando:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

När du importerar modulen kan du få ett felmeddelande som säger ”AzureStack.Connect.psm1 inte har signerats digitalt. Skriptet körs inte på datorn ”. Du kan ange körningsprincip för att köra skriptet med följande kommando i en upphöjd PowerShell-session för att lösa problemet:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Skapa huvudnamn för tjänsten
Du kan skapa ett huvudnamn för tjänsten genom att köra följande kommando, se till att uppdatera den *DisplayName* parameter:
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
```
### <a name="assign-a-role"></a>Tilldela en roll
När tjänstens huvudnamn har skapats måste du [tilldela den till en roll](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Logga in med hjälp av PowerShell
När du har tilldelat en roll, kan du logga in på Azure-stacken använder tjänstens huvudnamn med följande kommando:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Tilldela roll till tjänstens huvudnamn
Om du vill komma åt resurser i din prenumeration måste du tilldela program till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Mer information om tillgängliga roller, se [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

Du kan ange omfånget för prenumerationen, resursgruppen eller resursen. Behörigheter ärvs på lägre nivåer i omfånget. Till exempel innebär lägga till ett program rollen Läsare för en resursgrupp att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå för omfång som du vill tilldela program till i Azure Stack-portalen. Välj till exempel om du vill tilldela en roll på prenumerationsomfattningen **prenumerationer**. Du kan i stället välja en resursgrupp eller resurs.

2. Välj den vissa prenumerationen (resursgrupp eller resurs) om du vill tilldela programmet till.

     ![Välj prenumerationen för tilldelning](./media/azure-stack-create-service-principal/image16.png)

3. Välj **åtkomstkontroll (IAM)**.

     ![Välj åtkomst](./media/azure-stack-create-service-principal/image17.png)

4. Välj **Lägg till**.

5. Markera den roll som du vill tilldela till programmet.

6. Sök efter programmet och markera den.

7. Välj **OK** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

Nu när du har skapat ett huvudnamn för tjänsten och tilldelats en roll, kan du börja använda det i ditt program att komma åt resurser i Azure-stacken.  

## <a name="next-steps"></a>Nästa steg

[Hantera användarbehörigheter](azure-stack-manage-permissions.md)