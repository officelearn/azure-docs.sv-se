---
title: "Hur du använder hanterade tjänstidentiteten en Azure VM för inloggning"
description: "Stegvisa anvisningar och exempel för med en Azure VM MSI tjänstens huvudnamn för skript-klient loggar in och resurs för åtkomst till."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Logga in med en virtuell dator Användartilldelad hanteras Service identitet (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]Den här artikeln innehåller exempel på skript CLI för att logga in med en Användartilldelad MSI tjänstens huvudnamn och vägledning om viktiga ämnen, till exempel felhantering.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

För att kunna använda Azure CLI-exemplen i den här artikeln, måste du installera den senaste versionen av [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Alla exempelskript i den här artikeln förutsätter kommandoradsverktyget klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” VM i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md), eller en variant artiklar (med PowerShell-portalen, en mall eller en Azure SDK). 
> - För att förhindra fel vid inloggning och resursen åtkomst MSI måste ges minst ”Reader” komma åt på en lämplig omfattning (den virtuella datorn eller högre) så att Azure Resource Manager-åtgärder på den virtuella datorn. Se [tilldelar en hanterad tjänst identitet (MSI) åtkomst till en resurs med hjälp av Azure CLI](msi-howto-assign-access-cli.md) mer information.

## <a name="overview"></a>Översikt

En MSI som innehåller en [tjänstens huvudnamn](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), vilket är [skapas vid aktivering av MSI](msi-overview.md#how-does-it-work) på den virtuella datorn. Tjänstens huvudnamn kan få åtkomst till Azure-resurser och användas som en identitet av skriptet/Kommandotolken-Command-Line klienter för inloggning och resurs. För att komma åt skyddade resurser under sin egen identitet traditionellt måste en skript-klient du:  

   - registreras och godkänt med Azure AD som ett konfidentiellt/klienten webbprogram
   - logga in under dess huvudnamn för tjänsten med hjälp av appens autentiseringsuppgifter (som är sannolikt inbäddad i skriptet)

Med MSI, skript-klienten inte längre behöver göra något, eftersom den kan logga in under MSI tjänstens huvudnamn. 

## <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

1. Logga in på Azure AD under Användartilldelad MSI-tjänstens huvudnamn.  
2. Anropa Azure Resource Manager och hämta platsen för en virtuell Azure-region. CLI hand tar om hanteringen token förvärv/Använd åt dig automatiskt. Se till att ersätta ditt VM-namn för `<VM NAME>`, och Användartilldelad MSI resurs-id för `<MSI ID>`. Resurs-id MSI returneras den `id` egenskapen under skapandet av en Användartilldelad MSI (finns [konfigurera en Användartilldelad hanteras Service identitet (MSI) för en virtuell dator med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md) exempel på den `az identity create` kommando ).

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    Exempel på svar:
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](msi-overview.md#azure-services-that-support-azure-ad-authentication) en lista över resurser som stöder Azure AD och har testats med MSI och deras respektive resurs-ID.

## <a name="error-handling-guidance"></a>Riktlinjer för hantering av fel 

Följande svar kan tyda på att MSI inte har konfigurerats korrekt:

- CLI: *MSI: Det gick inte att hämta en token från 'http://localhost:50342/oauth2/token' med ett fel av ' HTTPConnectionPool (värden = localhost, port = 50342)* 

Om något av dessa fel, gå tillbaka till den virtuella Azure-datorn i den [Azure-portalen](https://portal.azure.com) och:

- Gå till den **Configuration** sidan och se till att ”hanterade tjänstidentiteten” är inställt på ”Ja”.
- Gå till den **tillägg** sidan och kontrollera MSI-tillägget har distribuerats.

Om något är fel, kan du behöva omtilldela MSI-filerna till resursen igen eller felsöka distributionen misslyckades. Se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md) om du behöver hjälp med VM-konfiguration.

## <a name="next-steps"></a>Nästa steg

- För att aktivera MSI på en Azure VM, se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.








