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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: 17fb8a82709e7c0c7353b70f7731895889167a79
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873962"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Använd API-versionsprofiler med Python i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Python och API-versionsprofiler

Python SDK har stöd för API-versionsprofiler för att rikta olika plattformar, till exempel Azure Stack och globala Azure. Du kan använda API-profiler i att skapa lösningar för ett hybridmoln. Python SDK har stöd för följande API-profiler:

1. **senaste**  
    Profilen som riktar sig mot den aktuella API-versionen för alla leverantörer i Azure-plattformen.
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    Profilen som riktar sig mot de API-versionerna som stöds av Azure Stack resource provider.

    Mer information om API-profiler och Azure Stack finns i [hantera API-versionsprofiler i Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Installera Azure Python SDK

1.  Installera Git från [webbplatsen officiella](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Anvisningar för att installera Python SDK finns i [Azure för Python-utvecklare](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Om det inte finns skapar du en prenumeration och spara prenumerations-ID som ska användas senare. Anvisningar för att skapa en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Skapa ett huvudnamn för tjänsten och spara dess ID och hemlighet. Anvisningar för att skapa ett tjänstobjekt för Azure Stack finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md). 
5.  Kontrollera att din tjänstens huvudnamn har rollen deltagare/ägare av prenumerationen. Anvisningar om hur du tilldelar roll till tjänstens huvudnamn finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda Azure SDK för Python med Azure Stack, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för operativsystemet på ställa in miljövariabler. 

| Värde | Miljövariabler | Beskrivning |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Klient-ID:t | AZURE_TENANT_ID | Värdet för Azure Stack [klient-ID](../azure-stack-identity-overview.md). |
| Klientorganisations-ID | AZURE_CLIENT_ID | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn har skapats i föregående avsnitt i det här dokumentet. |
| Prenumerations-ID:t | AZURE_SUBSCRIPTION_ID | Den [prenumerations-ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) är hur du kommer åt erbjudanden i Azure Stack. |
| Klienthemlighet | AZURE_CLIENT_SECRET | Huvudnamn tjänstprogrammet hemlighet sparas när tjänstens huvudnamn har skapats. |
| Resource Manager-slutpunkten | ARM_ENDPOINT | Se [Azure Stack resource manager-slutpunkten](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Python-exempel för Azure Stack 

Du kan använda följande kodexempel för att utföra vanliga administrationsuppgifter för virtuella datorer i Azure Stack.

Kodexempel visar att:

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

Om du vill granska koden för att utföra dessa åtgärder ta en titt på **run_example()** funktionen i Python-skriptet **Hybrid/unmanaged-disks/example.py** i GitHub-lagringsplatsen [ virtuella datorer – python-hantera](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Varje åtgärd är tydligt märkt med en kommentar och en utskriftsfunktionen.
Exemplen är inte nödvändigtvis i den ordning som visas i listan ovan.


## <a name="run-the-python-sample"></a>Köra Python-exemplet

1.  Om du inte redan har det, [installera Python](https://www.python.org/downloads/).

    Det här exemplet (och SDK: N) är kompatibel med Python 2.7, 3.4, 3.5 och 3.6.

2.  Allmän rekommendation för Python-utveckling är att använda en virtuell miljö. 
    Mer information finns i https://docs.python.org/3/tutorial/venv.html
    
    Installera och initiera den virtuella miljön med modulen ”venv” på Python 3 (du måste installera [virtuell miljö](https://pypi.python.org/pypi/virtualenv) för Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Klona databasen.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Installera beroenden med hjälp av pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Skapa en [tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) att arbeta med Azure Stack. Kontrollera att tjänstens huvudnamn har [deltagare/ägarrollen](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) på din prenumeration.

6.  Ange följande variabler och exportera miljövariablerna i ditt nuvarande gränssnitt. 

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  För att kunna köra det här exemplet, måste Ubuntu 16.04-LTS och Windows Server 2012 R2 Datacenter-avbildningar finnas i Azure Stack marketplace. Det kan vara antingen [ned från Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) eller [lagts till i plattformens Avbildningslagringsplats](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Kör exemplet.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Anteckningar

Du kanske tro att försöka hämta en virtuell dators OS-disk med hjälp av `virtual_machine.storage_profile.os_disk`.
I vissa fall kan detta göra vad du vill ha, men tänk på att den ger dig en `OSDisk` objekt.
Om du vill uppdatera OS-diskens storlek, som `example.py` , du behöver inte en `OSDisk` objekt men en `Disk` objekt.
`example.py` hämtar den `Disk` objekt med följande:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Nästa steg

- [Azure Python-Utvecklingscenter](https://azure.microsoft.com/develop/python/)
- [Dokumentation om Azure virtuella datorer](https://azure.microsoft.com/services/virtual-machines/)
- [Utbildningsväg för virtuella datorer](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Om du inte har en Microsoft Azure-prenumeration kan du få ett kostnadsfritt utvärderingskonto [här](https://go.microsoft.com/fwlink/?LinkId=330212).
