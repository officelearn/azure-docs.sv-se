---
title: Med hjälp av API-versionsprofiler med Python i Azure Stack | Microsoft Docs
description: Lär dig mer om hur du använder API-versionsprofiler med Python i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
<!-- dev: viananth -->
ms.openlocfilehash: b3bfc3072f819a92bdceb1721bb7737a3dc04cf8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078864"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Använd API-versionsprofiler med Python i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Python och API-versionsprofiler

Python SDK har stöd för API-versionsprofiler för att rikta olika plattformar, till exempel Azure Stack och globala Azure. Du kan använda API-profiler i att skapa lösningar för ett hybridmoln. Python SDK har stöd för följande API-profiler:

- **latest**  
    Den här profilen riktar sig mot den aktuella API-versionen för alla leverantörer i Azure-plattformen.
- **2018-03-01-hybrid**     
    Den här profilen riktar sig mot de senaste API-versionerna för alla providrar i Azure Stack-plattformen.
- **2017-03-09-profile**  
    Den här profilen riktar sig mot de mest kompatibla API-versionerna som stöds av Azure Stack resource provider.

   Mer information om API-profiler och Azure Stack finns i [hantera API-versionsprofiler i Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Installera Azure Python SDK

1. Installera Git från [webbplatsen officiella](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. För instruktioner om hur du installerar Python SDK, se [Azure för Python-utvecklare](/python/azure/python-sdk-azure-install?view=azure-python).
3. Om det inte finns skapar du en prenumeration och spara prenumerations-ID för senare användning. Anvisningar om hur du skapar en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).
4. Skapa ett huvudnamn för tjänsten och spara dess ID och hemlighet. Anvisningar om hur du skapar ett huvudnamn för tjänsten för Azure Stack finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md).
5. Kontrollera att tjänstens huvudnamn har rollen deltagare/ägare av prenumerationen. Anvisningar om hur du tilldelar roll till tjänstens huvudnamn finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda Azure-SDK för Python med Azure Stack, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för ditt operativsystem om hur du anger miljövariabler.

| Värde | Miljövariabler | Beskrivning |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Klient-ID:t | AZURE_TENANT_ID | Värdet för Azure Stack [klient-ID](../azure-stack-identity-overview.md). |
| Klientorganisations-ID | AZURE_CLIENT_ID | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn skapades i föregående avsnitt i den här artikeln. |
| Prenumerations-ID:t | AZURE_SUBSCRIPTION_ID | Den [prenumerations-ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) är hur du kommer åt erbjudanden i Azure Stack. |
| Klienthemlighet | AZURE_CLIENT_SECRET | Tjänstens huvudnamn programhemlighet sparas när tjänstens huvudnamn har skapats. |
| Resource Manager-slutpunkten | ARM_ENDPOINT | Se den [Azure Stack Resource Manager-slutpunkten](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Resursplats | AZURE_RESOURCE_LOCATION | Resursplatsen för din Azure Stack-miljön.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure Stack Certifikatutfärdarens rotcertifikat

Om du använder ASDK behöver ska lita på Certifikatutfärdarens rotcertifikat på din fjärrdatorn. Du behöver inte göra detta med integrerade system.

#### <a name="windows"></a>Windows

1. Hitta python plats för certifikatarkiv på din dator. Platsen kan variera beroende på var du har installerat Python. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Anteckna certifikaten du plats. Till exempel *~/lib/python3.5/site-packages/certifi/cacert.pem*. Viss sökvägen beror på ditt operativsystem och vilken version av Python som du har installerat.

2. Lita på Azure Stack Certifikatutfärdarens rotcertifikat genom att den läggs till det befintliga certifikatet för Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"

    ```

> [!NOTE]  
> Om du använder virtuell miljö för utveckling med SDK för Python enligt beskrivningen nedan, behöver du lägga till ovanstående cert samt certifikatarkiv för den virtuella miljön. Sökvägen kan se ut ”:... \mytestenv\Lib\site-packages\certifi\cacert.PEM ”



## <a name="python-samples-for-azure-stack"></a>Python-exempel för Azure Stack

Några av kodexempel för Azure Stack med hjälp av Python SDK är:

- [Hantera resurser och resursgrupper](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/).
- [Hantera Storage-konto](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/).
- [Hantera virtuella datorer](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/).

## <a name="python-manage-virtual-machine-sample"></a>Python hantera VM-exempel

Du kan använda följande kodexempel för att utföra vanliga administrationsuppgifter för virtuella datorer i Azure Stack. Kodexemplet visar att:

- Skapa virtuella datorer:
  - Skapa en virtuell Linux-dator
  - Skapa en virtuell Windows-dator
- Uppdatera en virtuell dator:
  - Expandera en enhet
  - Tagga en virtuell dator
  - Koppla datadiskar
  - Koppla från datadiskar
- Använda en virtuell dator:
  - Starta en virtuell dator
  - Stoppa en virtuell dator
  - Starta om en virtuell dator
- Lista över virtuella datorer
- Ta bort en virtuell dator

Den kod som utför de här åtgärderna finns i den **run_example()** funktionen i Python-skriptet **example.py** i GitHub-lagringsplatsen [Hybrid-beräkning-Python-hantera-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Varje åtgärd är tydligt märkt med en kommentar och en utskriftsfunktionen. Exemplen är inte nödvändigtvis i den ordning som visas i den här listan.

## <a name="run-the-python-sample"></a>Köra Python-exemplet

1. Om du inte redan har [installera Python](https://www.python.org/downloads/). Det här exemplet (och SDK: N) är kompatibel med Python 2.7, 3.4, 3.5 och 3.6.

2. Allmän rekommendation för Python-utveckling är att använda en virtuell miljö. Mer information finns i den [dokumentace Pro Python](https://docs.python.org/3/tutorial/venv.html).

3. Installera och initiera den virtuella miljön med modulen ”venv” på Python 3 (du måste installera [virtuell miljö](https://pypi.python.org/pypi/virtualenv) för Python 2.7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Klona databasen:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Installera beroenden med pip:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Skapa en [tjänstens huvudnamn](../azure-stack-create-service-principals.md) att arbeta med Azure Stack. Kontrollera att tjänstens huvudnamn har [deltagare/ägarrollen](../azure-stack-create-service-principals.md#assign-a-role) på din prenumeration.

7. Ange följande variabler och exportera miljövariablerna i ditt nuvarande gränssnitt:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. För att kunna köra det här exemplet, måste Ubuntu 16.04-LTS och Windows Server 2012 R2 DataCenter-avbildningar finnas i Azure Stack marketplace. Det kan vara antingen [ned från Azure](../azure-stack-download-azure-marketplace-item.md), eller lagts till i den [plattformens Avbildningslagringsplats](../azure-stack-add-vm-image.md).

9. Kör exemplet:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>Nästa steg

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Dokumentation om Azure virtuella datorer](https://azure.microsoft.com/services/virtual-machines/)
- [Utbildningsväg för virtuella datorer](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Om du inte har en Microsoft Azure-prenumeration kan du få ett kostnadsfritt utvärderingskonto [här](https://go.microsoft.com/fwlink/?LinkId=330212).
