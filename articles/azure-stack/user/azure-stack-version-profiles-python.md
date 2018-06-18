---
title: Med hjälp av API-version profiler med Python i Azure-stacken | Microsoft Docs
description: Lär dig mer om hur du använder profiler för API-version med Python i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806844"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Använd profiler för API-version med Python i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="python-and-api-version-profiles"></a>Profiler för Python och API-version

Python SDK stöder profiler för API-version att målplattformar annat moln, till exempel Azure-stacken och global Azure. Du kan använda API-profiler för att skapa lösningar för ett hybridmoln. Python SDK stöder följande API-profiler:

1. **senaste**  
    Profilen som riktar sig till den senaste API-versionen för alla providers i Azure-plattformen.
2.  **2017-03-09-profil**  
    **2017-03-09-profil**  
    Profilen som riktar sig till API-versioner av resursproviders som stöds av Azure-stacken.

    Mer information om API-profiler och Azure-stacken finns [hantera API-version-profiler i Azure-stacken](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Installera Azure Python SDK

1.  Installera Git från [officiella platsen](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Anvisningar att installera Python SDK finns [Azure för Python-utvecklare](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Om det inte finns kan du skapa en prenumeration och spara prenumerations-ID som ska användas senare. Anvisningar att skapa en prenumeration finns [skapa prenumerationer erbjudanden i Azure-stacken](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Skapa ett huvudnamn för tjänsten och spara dess ID och hemlighet. Instruktioner för att skapa ett huvudnamn för tjänsten för Azure-stacken, se [ge program åtkomst till Azure-stacken](../azure-stack-create-service-principals.md). 
5.  Kontrollera att din tjänstens huvudnamn har deltagare/ägarrollen på din prenumeration. Instruktioner om hur du tilldelar rollen till tjänstens huvudnamn finns [ge program åtkomst till Azure-stacken](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda Azure SDK för Python med Azure-stacken, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för operativsystemet på inställningen i miljövariabler. 

| Värde | Miljövariabler | Beskrivning |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Klient-ID:t | AZURE_TENANT_ID | Värdet för Azure-stacken [klient-ID](../azure-stack-identity-overview.md). |
| Klient-ID | AZURE_CLIENT_ID | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn skapades i föregående avsnitt i det här dokumentet. |
| Prenumerations-ID:t | AZURE_SUBSCRIPTION_ID | Den [prenumerations-ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) är hur du kommer åt erbjudanden i Azure-stacken. |
| Klienthemlighet | AZURE_CLIENT_SECRET | Huvudnamn tjänstprogrammet hemlighet sparas när tjänstens huvudnamn skapades. |
| Resource Manager-slutpunkt | ARM_ENDPOINT | Se [Stack Azure resource manager-slutpunkt](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Python-exempel för Azure-Stack 

Du kan använda följande kodexempel för att utföra vanliga hanteringsuppgifter för virtuella datorer i Azure-stacken.

Kodexempel visar du:

- Skapa virtuella datorer:
    - Skapa en virtuell Linux-dator
    - Skapa en virtuell Windows-dator
- Uppdatera en virtuell dator:
    - Expandera en enhet
    - Tagga en virtuell dator
    - Bifoga datadiskar
    - Koppla från datadiskar
- Använda en virtuell dator:
    - Starta en virtuell dator
    - Stoppa en virtuell dator
    - Starta om en virtuell dator
- Lista över virtuella datorer
- Ta bort en virtuell dator

Om du vill granska koden för att utföra dessa åtgärder, ta en titt på **run_example()** funktion i Python-skriptet **Hybrid/unmanaged-disks/example.py** i GitHub-Repo- [ virtuella datorer – python-hantera](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Varje åtgärd är tydligt märkt med en kommentar och en funktionen print.
Exemplen är inte nödvändigtvis i den ordning som visas i listan ovan.


## <a name="run-the-python-sample"></a>Kör Python-exempel

1.  Om du inte redan har det, [installera Python](https://www.python.org/downloads/).

    Det här exemplet (och SDK) är kompatibel med Python 2.7, 3.4, 3.5 och 3,6.

2.  Allmänna rekommendationer för utveckling av Python är att använda en virtuell miljö. 
    Mer information finns i https://docs.python.org/3/tutorial/venv.html
    
    Installera och starta den virtuella miljön med modulen ”venv” på Python 3 (du måste installera [virtuell miljö](https://pypi.python.org/pypi/virtualenv) för Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Klona lagringsplatsen.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Installera beroenden med pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Skapa en [tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) att arbeta med Azure-stacken. Kontrollera att tjänstens huvudnamn har [deltagare/ägarrollen](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) på din prenumeration.

6.  Ange följande variabler och exportera dessa miljövariabler i din aktuella shell. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).