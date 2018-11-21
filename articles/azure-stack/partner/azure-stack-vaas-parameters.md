---
title: Arbetsflödets gemensamma parametrar i Azure Stack-validering som en tjänst | Microsoft Docs
description: Arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: ddc6942b56e3ad4d1f5b16c86dde87f408c1a2c1
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263017"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Parametrarna är värden som miljövariabler och användarens autentiseringsuppgifter som krävs av alla tester i verifiering som en tjänst (VaaS). Dessa värden definieras på arbetsflödesnivå när du skapar eller ändrar ett arbetsflöde. När du schemalägger testerna skickas dessa värden som parametrar för varje test under arbetsflödet.

> [!NOTE]
> Varje test definierar en egen uppsättning parametrar. Ett test på Schemalägg tid, kan kräva att du anger ett värde oberoende av de gemensamma parametrarna eller kan du åsidosätta vanliga parametervärdet.

## <a name="environment-parameters"></a>Miljö-parametrar

Miljö parametrar beskrivs Azure Stack-miljön under testet. Dessa värden måste anges genom att generera och överföra en fil Azure Stack stämpel information för den specifika instans som du vill testa.

> [!NOTE]
> Miljö-parametrar kan inte ändras när arbetsflödet har skapats i officiella verifiering arbetsflöden.

### <a name="generate-the-stamp-information-file"></a>Generera filen för stämpel-information

1. Logga in på DVM eller datorer som har åtkomst till Azure Stack-miljön.
2. Kör följande kommandon i en upphöjd PowerShell-kommandotolk:
    ```PowerShell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Leta upp värdena i konfigurationsfilen FN

Parametervärden för miljön kan också finnas manuellt i den **FN konfigurationsfilen** på `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` på DVM.

## <a name="test-parameters"></a>Parametrarna för webbtestet

Vanliga testparametrar innehåller känslig information som inte kan lagras i konfigurationsfiler. Dessa måste anges manuellt.

Parameter    | Beskrivning
-------------|-----------------
Klient-administratör                            | Azure Active Directory Innehavaradministratör som etablerades tjänstadministratören i AAD-katalogen. Den här användaren utför på klientnivå åtgärder som att distribuera mallar att ställa in resurser (virtuella datorer, lagringskonton osv) och köra arbetsbelastningar. Mer information om etablering klientkonto finns [lägga till en ny Azure Stack-klient](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad).
Service-administratör             | Azure Active Directory-administratören för AAD-Directory-klient som anges under distributionen av Azure Stack Sök efter `AADTenant` i FN konfigurationen och väljer värdet i den `UniqueName` element.
Molnadministratören användare               | Azure Stack-domänadministratörskonto (t.ex. `contoso\cloudadmin`). Sök efter `User Role="CloudAdmin"` i FN konfigurationen och väljer värdet i den `UserName` element.
Anslutningssträngen för diagnostik          | En SAS-URL till ett Azure Storage-konto på vilka felsökning loggfilerna kopieras under testkörning av. Anvisningar om att generera SAS-Webbadressen finns i [generera anslutningssträngen diagnostik](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> Den **diagnostik anslutningssträngen** måste vara giltig innan du fortsätter.

### <a name="generate-the-diagnostics-connection-string"></a>Generera anslutningssträngen diagnostik

Anslutningssträngen diagnostik krävs för att lagra diagnostikloggar under testkörning av. Använda Azure Storage-konto som skapades under installationen (finns i [ställa in din validering som en tjänstresurser](azure-stack-vaas-set-up-resources.md)) att skapa en signatur-URL för delad åtkomst för att ge VaaS åtkomst till att ladda upp loggar till ditt lagringskonto.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Välj **Blob** från **tillåtna tjänster alternativ**. Avmarkera eventuella återstående alternativen.

1. Välj **Service**, **behållare**, och **objekt** från **tillåtna resurstyper**.

1. Välj **Läs**, **skriva**, **lista**, **lägga till**, **skapa** från **tillåts behörigheter**. Avmarkera eventuella återstående alternativen.

1. Ange **starttid** till aktuell tid och **sluttid** till tre månader från den aktuella tiden.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> SAS-Webbadressen upphör att gälla vid sluttid anges när URL: en har genererats.  
När du schemalägger tester, kontrollera att URL: en är giltig i minst 30 dagar, plus tid som krävs för testkörning av (tre månader föreslås).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [verifiering som en tjänst viktiga begrepp](azure-stack-vaas-key-concepts.md)