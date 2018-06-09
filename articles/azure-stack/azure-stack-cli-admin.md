---
title: Aktivera Azure CLI för Azure-stacken användare | Microsoft Docs
description: Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att hantera och distribuera resurser i Azure-stacken
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
ms.date: 06/06/2018
ms.author: mabrigg
ms.openlocfilehash: d0103d211608514848da7d789d32d37d8385f33f
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247864"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Aktivera Azure CLI för Azure Stack-användare

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Det inte finns några Azure Stack operator-specifika aktiviteter som du kan utföra med hjälp av Azure CLI. Men innan användarna kan hantera resurser via CLI, Azure Stack operatorer måste ange dem med följande:

* **Azure-stacken Certifikatutfärdarens rotcertifikat** krävs om du använder CLI från en arbetsstation utanför Azure-stacken Development Kit.  

* **Virtuella datorn alias slutpunkten** ger ett alias som ”UbuntuLTS” eller ”Win2012Datacenter”, som refererar till en avbildningens utgivare, erbjudande, SKU och version som en parameter när du distribuerar virtuella datorer.  

I följande avsnitt beskrivs hur du hämtar dessa värden.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportera Azure Stack Certifikatutfärdarens rotcertifikat

Azure-stacken Certifikatutfärdarens rotcertifikat är tillgänglig på development kit och på en virtuell klientdator som körs i kit utvecklingsmiljön. Om du vill exportera rotcertifikatet för Azure-stacken i PEM-format, logga in på ditt development kit eller klient virtuella datorn och kör följande skript:

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

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Ställ in alias virtuell datorslutpunkt

Azure Stack-operatörer bör ställa in en offentligt tillgänglig slutpunkt som är värd för en virtuell dator alias-fil. Virtuella Aliasfilen är en JSON-fil som innehåller ett eget namn för en bild. Det här namnet anges senare när en virtuell dator distribueras som en parameter av Azure CLI.  

Innan du lägger till en post till en aliasfil, kontrollerar du att du [hämta bilder från Azure Marketplace](azure-stack-download-azure-marketplace-item.md), eller ha [publicerade en egen anpassad avbildning](azure-stack-add-vm-image.md). Om du publicerar en anpassad avbildning anteckna utgivare, erbjudande, SKU och version informationen som du angav vid publicering. Om det är en avbildning från marketplace, du kan visa informationen med hjälp av den ```Get-AzureVMImage``` cmdlet.  

En [alias exempelfilen](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) med många vanliga avbildningen alias är tillgänglig. Du kan använda som som en startpunkt. Värd för den här filen i ett utrymme där CLI-klienter kan nå. Ett sätt som är värd för filen i en blob storage-konto och dela URL: en med dina användare:

1. Hämta den [exempelfilen](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) från GitHub.
2. Skapa ett nytt lagringskonto i Azure-stacken. När det är klart kan du skapa en ny blobbbehållare. Ange åtkomstprincip för till ”offentliga”.  
3. Överför JSON-filen till den nya behållaren. När det är klart, kan du visa Webbadressen till blob genom att välja blobbnamnet och sedan välja URL: en från blob-egenskaper.

## <a name="next-steps"></a>Nästa steg

- [Distribuera mallar med Azure CLI](azure-stack-deploy-template-command-line.md)

- [Ansluta med PowerShell](azure-stack-connect-powershell.md)

- [Hantera användarbehörigheter](azure-stack-manage-permissions.md)
