---
title: Aktivera Azure CLI för Azure Stack-användare | Microsoft Docs
description: Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att hantera och distribuera resurser i Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: mabrigg
ms.openlocfilehash: 09c551ea7196ae20a60a5dd34c1cda889ff5df46
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648981"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Aktivera Azure CLI för Azure Stack-användare

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan ange Certifikatutfärdarens rotcertifikat för användare av Azure Stack så att de kan använda Azure CLI på sina datorer för utveckling. Användarna behöver certifikatet som ska hantera resurser via CLI.

* **Azure Stack Certifikatutfärdarens rotcertifikat** krävs om du använder CLI från en dator utanför Azure Stack Development Kit.  

* **Virtuella datorns alias slutpunkt** innehåller ett alias, t.ex. ”UbuntuLTS” eller ”Win2012Datacenter” som refererar till en avbildningens utgivare, erbjudande, SKU och version som en parameter när du distribuerar virtuella datorer.  

I följande avsnitt beskrivs hur du hämtar dessa värden.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportera rotcertifikatet för Azure Stack

Du hittar Azure Stack Certifikatutfärdarens rotcertifikat på i development kit och på en virtuell klientdator som kör i kit utvecklingsmiljö. Om du vill exportera rotcertifikatet för Azure Stack i PEM-format, logga in på din development kit eller virtuell klientdator och kör följande skript:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Konfigurera virtuella datorns alias-slutpunkt

Azure Stack-operatörer bör ställa in en offentligt tillgänglig slutpunkt som är värd för en virtuell dator alias-fil. Alias VHD-filen är en JSON-fil som innehåller ett eget namn för en bild. Det namnet anges därefter när en virtuell dator distribueras som en parameter för Azure CLI.  

Innan du lägger till en post till en aliasfil, kontrollerar du att du [hämta avbildningar från Azure Marketplace](azure-stack-download-azure-marketplace-item.md), eller så har [publicerat en egen anpassad bild](azure-stack-add-vm-image.md). Om du publicerar en anpassad avbildning anteckna utgivare, erbjudande, SKU och version informationen som du angav under publiceringen. Om det är en avbildning från marketplace kan du visa informationen med hjälp av den ```Get-AzureVMImage``` cmdlet.  

En [alias-exempelfilen](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) med många vanliga avbildning alias är tillgänglig. Du kan använda som som en startpunkt. Vara värd för den här filen i ett utrymme där din CLI-klienter kan nå den. Ett sätt är att vara värd för filen i ett blob storage-konto och dela URL: en med dina användare:

1. Ladda ned den [exempelfilen](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) från GitHub.
2. Skapa ett nytt lagringskonto i Azure Stack. När det är klart kan du skapa en ny blobbehållare. Skapa princip för åtkomst till ”offentliga”.  
3. Ladda upp JSON-filen till den nya behållaren. När det är klart kan du visa blobens URL genom att välja blobnamnet och sedan välja URL: en från blobegenskaper.

## <a name="next-steps"></a>Nästa steg

- [Distribuera mallar med Azure CLI](azure-stack-deploy-template-command-line.md)

- [Ansluta med PowerShell](azure-stack-connect-powershell.md)

- [Hantera användarbehörigheter](azure-stack-manage-permissions.md)
