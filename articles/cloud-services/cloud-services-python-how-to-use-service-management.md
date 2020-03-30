---
title: Använda Service Management API (Python) - funktionsguide
description: Lär dig hur du programmässigt utför vanliga servicehanteringsuppgifter från Python.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 50501413a63921a9a34be1c04ed259990922b686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70141473"
---
# <a name="use-service-management-from-python"></a>Använda tjänsthantering från Python
Den här guiden visar hur du programmässigt utför vanliga servicehanteringsuppgifter från Python. **Klassen ServiceManagementService** i [Azure SDK för Python](https://github.com/Azure/azure-sdk-for-python) stöder programmatisk åtkomst till en stor del av de tjänsthanteringsrelaterade funktioner som är tillgängliga i [Azure-portalen][management-portal]. Du kan använda den här funktionen för att skapa, uppdatera och ta bort molntjänster, distributioner, datahanteringstjänster och virtuella datorer. Den här funktionen kan vara användbar när du skapar program som behöver programmatisk åtkomst till tjänsthantering.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Vad är servicehantering?
Api:et för Azure Service Management ger programmatisk åtkomst till en stor del av de funktioner för tjänsthantering som är tillgängliga via [Azure-portalen][management-portal]. Du kan använda Azure SDK för Python för att hantera dina molntjänster och lagringskonton.

Om du vill använda API:et för tjänsthantering måste du [skapa ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Begrepp
Azure SDK för Python [radbryts API:et för tjänsthantering][svc-mgmt-rest-api], som är ett REST API. Alla API-åtgärder utförs via SSL och autentiseras ömsesidigt med hjälp av X.509 v3-certifikat. Hanteringstjänsten kan nås från en tjänst som körs i Azure. Den kan också nås direkt via Internet från alla program som kan skicka en HTTPS-begäran och få ett HTTPS-svar.

## <a name="installation"></a><a name="Installation"> </a>Installation
Alla funktioner som beskrivs i `azure-servicemanagement-legacy` den här artikeln finns i paketet, som du kan installera med hjälp av pip. Mer information om installation (till exempel om du inte har tidigare i Python) finns i [Installera Python och Azure SDK](/azure/python/python-sdk-azure-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Ansluta till tjänsthantering
För att ansluta till slutpunkten för tjänsthantering behöver du ditt Azure-prenumerations-ID och ett giltigt hanteringscertifikat. Du kan få ditt prenumerations-ID via [Azure-portalen][management-portal].

> [!NOTE]
> Nu kan du använda certifikat som skapats med OpenSSL när du kör på Windows. Python 2.7.4 eller senare krävs. Vi rekommenderar att du använder OpenSSL i stället för PFX, eftersom stöd för PFX-certifikat sannolikt kommer att tas bort i framtiden.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Hanteringscertifikat på Windows/Mac/Linux (OpenSSL)
Du kan använda [OpenSSL](https://www.openssl.org/) för att skapa ditt hanteringscertifikat. Du måste skapa två certifikat, ett för `.cer` servern (en fil) `.pem` och ett för klienten (en fil). Om du `.pem` vill skapa filen kör du:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Så här `.cer` skapar du certifikatet:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Mer information om Azure-certifikat finns i [översikt över certifikat för Azure Cloud Services](cloud-services-certs-create.md). En fullständig beskrivning av OpenSSL-parametrar [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html)finns i dokumentationen på .

När du har skapat `.cer` dessa filer överför du filen till Azure. Välj **Ladda upp**på fliken **Inställningar** i [Azure-portalen][management-portal]. Observera var du `.pem` sparade filen.

När du har skaffat ditt prenumerations-ID skapar du ett certifikat och överför `.cer` filen till Azure ansluter du till slutpunkten för Azure-hantering. Anslut genom att skicka prenumerations-ID:t och sökvägen till `.pem` filen till **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

I föregående exempel `sms` är ett **ServiceManagementService-objekt.** **Klassen ServiceManagementService** är den primära klassen som används för att hantera Azure-tjänster.

### <a name="management-certificates-on-windows-makecert"></a>Hanteringscertifikat i Windows (MakeCert)
Du kan skapa ett självsignerat hanteringscertifikat på datorn med hjälp `makecert.exe`av . Öppna en **Kommandotolk** i Visual Studio som **administratör** och använd följande kommando och ersätt *AzureCertificate* med det certifikatnamn du vill använda:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Kommandot skapar `.cer` filen och installerar den i det **personliga** certifikatarkivet. Mer information finns i [Översikt över certifikat för Azure Cloud Services](cloud-services-certs-create.md).

När du har skapat `.cer` certifikatet överför du filen till Azure. Välj **Ladda upp**på fliken **Inställningar** i [Azure-portalen][management-portal].

När du har skaffat ditt prenumerations-ID skapar du ett certifikat och överför `.cer` filen till Azure ansluter du till slutpunkten för Azure-hantering. Anslut genom att skicka prenumerations-ID:t och platsen för certifikatet i ditt **personliga** certifikatarkiv till **ServiceManagementService** (ersätt återigen *AzureCertificate* med namnet på ditt certifikat).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

I föregående exempel `sms` är ett **ServiceManagementService-objekt.** **Klassen ServiceManagementService** är den primära klassen som används för att hantera Azure-tjänster.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Lista tillgängliga platser
Om du vill visa de platser som är tillgängliga för värdtjänster använder du metoden **\_lista platser.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

När du skapar en molntjänst eller lagringstjänst måste du ange en giltig plats. Metoden **\_för listplatser** returnerar alltid en uppdaterad lista över de platser som för närvarande är tillgängliga. När detta skrivs är de tillgängliga platserna:

* Europa, västra
* Europa, norra
* Sydostasien
* Asien, östra
* USA, centrala
* USA, norra centrala
* USA, södra centrala
* USA, västra
* USA, östra
* Japan, östra
* Japan, västra
* Brasilien, södra
* Australien, östra
* Australien, sydöstra

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Skapa en molntjänst
När du skapar ett program och kör det i Azure kallas koden och konfigurationen tillsammans för en [Azure-molntjänst][cloud service]. (Det var känt som en *värdtjänst* i tidigare Azure-versioner.) Du kan använda metoden **skapa\_värdbaserade\_tjänster** för att skapa en ny värdtjänst. Skapa tjänsten genom att ange ett värddelat tjänstnamn (som måste vara unikt i Azure), en etikett (automatiskt kodad till base64), en beskrivning och en plats.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Du kan lista alla värdtjänster för din prenumeration med metoden **med värdbaserade\_\_tjänster** i listan.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Om du vill ha information om en viss värdtjänst skickar du tjänstnamnet som värd till metoden **hämta\_värdbaserade\_tjänstegenskaper.\_**

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

När du har skapat en molntjänst distribuerar du koden till tjänsten med **metoden\_skapa distribution.**

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Ta bort en molntjänst
Du kan ta bort en molntjänst genom att skicka tjänstnamnet till den **\_borttagningsvärda\_tjänstmetoden.**

    sms.delete_hosted_service('myhostedservice')

Innan du kan ta bort en tjänst måste alla distributioner för tjänsten först tas bort. Mer information finns i [Ta bort en distribution](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Ta bort en distribution
Om du vill ta bort en distribution använder du **borttagningsdistributionsmetoden.\_** I följande exempel visas hur `v1`du tar bort en distribution med namnet :

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Skapa en lagringstjänst
En [lagringstjänst](../storage/common/storage-create-storage-account.md) ger dig åtkomst till [Azure-blobbar,](../storage/blobs/storage-python-how-to-use-blob-storage.md) [tabeller](../cosmos-db/table-storage-how-to-use-python.md)och [köer](../storage/queues/storage-python-how-to-use-queue-storage.md). För att skapa en lagringstjänst behöver du ett namn för tjänsten (mellan 3 och 24 gemener och unika inom Azure). Du behöver också en beskrivning, en etikett (upp till 100 tecken, automatiskt kodad till base64) och en plats. I följande exempel visas hur du skapar en lagringstjänst genom att ange en plats:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

I föregående exempel kan statusen för åtgärden **skapa\_\_lagringskonto** hämtas genom att överföra resultatet som returneras genom att skapa **\_lagringskontot\_** till metoden hämta **\_åtgärdsstatus.\_** 

Du kan lista dina lagringskonton och deras egenskaper med **metoden listlagringskonton.\_\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Ta bort en lagringstjänst
Om du vill ta bort en lagringstjänst skickar du lagringstjänstnamnet till metoden **för borttagning av\_lagringskonto.\_** Om du tar bort en lagringstjänst tas alla data som lagras i tjänsten bort (blobbar, tabeller och köer).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Lista tillgängliga operativsystem
Om du vill visa de operativsystem som är tillgängliga för värdtjänster använder du metoden **med listoperativsystem.\_\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Du kan också använda metoden **listoperativfamiljer,\_\_\_** som grupperar operativsystemen efter familj.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Skapa en operativsystemavbildning
Om du vill lägga till en operativsystemavbildning i bilddatabasen använder du metoden **add\_os\_image.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Om du vill visa de operativsystemavbildningar som är tillgängliga använder du metoden **list\_os\_images.** Den innehåller alla plattformsbilder och användarbilder.

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Ta bort en operativsystemavbildning
Om du vill ta bort en användaravbildning använder du metoden **ta bort\_os-bild.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Skapa en virtuell dator
Om du vill skapa en virtuell dator måste du först skapa en [molntjänst](#CreateCloudService). Skapa sedan distributionen för den virtuella datorn med hjälp av **distributionsmetoden skapa\_virtuell dator.\_\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Ta bort en virtuell dator
Om du vill ta bort en virtuell dator tar du först bort distributionen med hjälp av **borttagningsdistributionsmetoden.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Molntjänsten kan sedan tas bort med hjälp av den **\_borttagningsvärda\_tjänstmetoden.**

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Skapa en virtuell dator från en tagen avbildning av virtuella datorer
Om du vill ta en vm-avbildning anropar du först **bildmetoden för att ta till en\_virtuell dator.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Om du vill vara säker på att du har tagit bilden använder du **API:et för vm-bilder\_\_** för lista. Kontrollera att bilden visas i resultatet.

    images = sms.list_vm_images()

Om du till vill skapa den virtuella datorn med den hämtade avbildningen använder du **distributionsmetoden skapa\_virtuell dator\_\_** som tidigare, men den här tiden går i vm_image_name i stället.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Mer information om hur du fångar en virtuell Linux-dator i den klassiska distributionsmodellen finns i [Fånga en virtuell Linux-dator](../virtual-machines/linux/classic/capture-image-classic.md).

Mer information om hur du fångar in en virtuell Windows-dator i den klassiska distributionsmodellen finns i [Fånga en virtuell Windows-dator](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="next-steps"></a><a name="What's Next"> </a>Nästa steg
Nu när du har lärt dig grunderna i tjänsthantering kan du komma åt [den fullständiga API-referensdokumentationen för Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) och utföra komplexa uppgifter enkelt för att hantera ditt Python-program.

Mer information finns i [Python Developer Center](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
