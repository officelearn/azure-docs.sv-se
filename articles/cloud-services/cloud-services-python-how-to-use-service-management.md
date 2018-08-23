---
title: Använda Service Management API (Python) - Funktionsguide
description: Lär dig hur du programmässigt kan utföra vanliga hanteringsuppgifter för tjänsten från Python.
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
ms.openlocfilehash: ab2a05c55ee248d8a74f70d36b06277db3a0f38a
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42059569"
---
# <a name="use-service-management-from-python"></a>Använda service management från Python
Den här guiden visar hur du programmässigt kan utföra vanliga hanteringsuppgifter för tjänsten från Python. Den **ServiceManagementService** klassen i den [Azure SDK för Python](https://github.com/Azure/azure-sdk-for-python) stöder Programmeringsåtkomst till stor del av service management-relaterade funktioner, som är tillgängliga i den [Azure portalen][management-portal]. Du kan använda den här funktionen för att skapa, uppdatera och ta bort cloud services, distributioner, datahanteringstjänster och virtuella datorer. Den här funktionen kan användas för att skapa program som behöver Programmeringsåtkomst till service management.

## <a name="WhatIs"> </a>Vad är service management?
Azure Service Management-API ger Programmeringsåtkomst till programmering av mycket av den tjänsten tillgänglig via den [Azure-portalen][management-portal]. Du kan använda Azure SDK för Python för att hantera dina molntjänster och storage-konton.

Service Management-API du vill använda, [skapa ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Begrepp
Azure SDK för Python omsluter den [Service Management-API][svc-mgmt-rest-api], vilket är ett REST-API. Alla API-åtgärder är fördelade över SSL och autentiserar varandra med X.509 v3-certifikat. Management-tjänsten kan nås inifrån en tjänst som körs i Azure. Det kan också nå direkt via Internet från alla program som kan skicka en HTTPS-begäran och ta emot HTTPS-svar.

## <a name="Installation"> </a>installationen
Alla de funktioner som beskrivs i den här artikeln är tillgängliga i den `azure-servicemanagement-legacy` paket som du kan installera med hjälp av pip. Mer information om installation (till exempel, om du inte har använt Python) finns i [installera Python och Azure SDK](../python-how-to-install.md).

## <a name="Connect"> </a>Ansluta till service management
Om du vill ansluta till hanteringsslutpunkten service behöver du ditt Azure-prenumerations-ID och ett giltigt hanteringscertifikat. Du kan hämta ditt prenumerations-ID via den [Azure-portalen][management-portal].

> [!NOTE]
> Du kan nu använda certifikaten som skapas med OpenSSL vid körning på Windows. Python 2.7.4 eller senare krävs. Vi rekommenderar att du använder OpenSSL i stället för .pfx, eftersom stödet för .pfx-certifikat troligen kommer att tas bort i framtiden.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Hanteringscertifikat i Windows/Mac/Linux (OpenSSL)
Du kan använda [OpenSSL](http://www.openssl.org/) att skapa ditt hanteringscertifikat. Du måste skapa två certifikat, en för servern (en `.cer` fil) och en för klienten (en `.pem` fil). Att skapa den `.pem` filen, kör:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Att skapa den `.cer` certifikat, kör:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Läs mer om Azure-certifikat, [Certifikatöversikt för Azure Cloud Services](cloud-services-certs-create.md). En fullständig beskrivning av OpenSSL parametrar finns i dokumentationen på [ http://www.openssl.org/docs/apps/openssl.html ](http://www.openssl.org/docs/apps/openssl.html).

När du har skapat dessa filer, ladda upp den `.cer` filen till Azure. I den [Azure-portalen][management-portal]på den **inställningar** fliken **överför**. Obs där du sparade den `.pem` filen.

När du har fått ditt prenumerations-ID, skapar du ett certifikat och ladda upp den `.cer` filen till Azure, ansluta till Azure management-slutpunkten. Ansluta genom att skicka prenumerations-ID och sökvägen till den `.pem` filen till **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

I föregående exempel `sms` är en **ServiceManagementService** objekt. Den **ServiceManagementService** klassen är den primära klassen används för att hantera Azure-tjänster.

### <a name="management-certificates-on-windows-makecert"></a>Hanteringscertifikat på Windows (MakeCert)
Du kan skapa ett självsignerat certifikat på datorn med hjälp av `makecert.exe`. Öppna en **Visual Studio-kommandotolk** som en **administratör** och använder du följande kommando, ersätta *AzureCertificate* med certifikatets namn som du vill använda:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Kommandot skapar den `.cer` filen och installerar den i den **personliga** certifikatarkiv. Mer information finns i [Certifikatöversikt för Azure Cloud Services](cloud-services-certs-create.md).

När du har skapat certifikatet laddar du upp den `.cer` filen till Azure. I den [Azure-portalen][management-portal]på den **inställningar** fliken **överför**.

När du har fått ditt prenumerations-ID, skapar du ett certifikat och ladda upp den `.cer` filen till Azure, ansluta till Azure management-slutpunkten. Ansluta genom att skicka prenumerations-ID och platsen för certifikatet i din **personliga** certifikatarkiv **ServiceManagementService** (ersätter *AzureCertificate* med namnet på certifikatet).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

I föregående exempel `sms` är en **ServiceManagementService** objekt. Den **ServiceManagementService** klassen är den primära klassen används för att hantera Azure-tjänster.

## <a name="ListAvailableLocations"> </a>Lista över tillgängliga platser
Om du vill visa de platser som är tillgängliga för värdtjänster, använda den **lista\_platser** metod.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

När du skapar en tjänst i molnet eller storage-tjänsten, måste du ange en giltig plats. Den **lista\_platser** metoden returnerar alltid en uppdaterad förteckning över för närvarande tillgängliga platser. När detta skrivs är tillgängliga platser:

* Västra Europa
* Norra Europa
* Sydostasien
* Östasien
* Centrala USA
* Norra centrala USA
* Södra centrala USA
* Västra USA
* Östra USA
* Östra Japan
* Västra Japan
* Södra Brasilien
* Östra Australien
* Sydöstra Australien

## <a name="CreateCloudService"> </a>Skapa en molntjänst
När du skapar ett program och kör det i Azure, kod och konfiguration som tillsammans kallas för en Azure [molntjänst][cloud service]. (Den kallades förut en *värdtjänsten* i tidigare versioner av Azure.) Du kan använda den **skapa\_finns\_service** metod för att skapa en ny värdbaserade tjänsten. Skapa tjänsten genom att ange ett namn för värdtjänsten (som måste vara unikt i Azure), en etikett (kodad automatiskt till base64), en beskrivning och en plats.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Du kan lista alla värdbaserade tjänster för din prenumeration med den **lista\_finns\_services** metod.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Om du vill ha information om en viss värdbaserade tjänst kan skicka värdbaserade tjänstens namn till den **hämta\_finns\_service\_egenskaper** metod.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

När du skapar en molnbaserad tjänst kan du distribuera din kod till tjänsten med den **skapa\_distribution** metod.

## <a name="DeleteCloudService"> </a>Ta bort en tjänst i molnet
Du kan ta bort en tjänst i molnet genom att skicka tjänstnamnet som ska den **ta bort\_finns\_service** metod.

    sms.delete_hosted_service('myhostedservice')

Innan du kan ta bort en tjänst, måste du först bort alla distributioner för tjänsten. Mer information finns i [ta bort en distribution](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Ta bort en distribution
Ta bort en distribution genom att använda den **ta bort\_distribution** metod. I följande exempel visas hur du tar bort en distribution med namnet `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Skapa en lagringstjänst
En [lagringstjänst](../storage/common/storage-create-storage-account.md) ger dig tillgång till Azure [blobar](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabeller](../cosmos-db/table-storage-how-to-use-python.md), och [köer](../storage/queues/storage-python-how-to-use-queue-storage.md). För att skapa en lagringstjänst, behöver du ett namn för tjänsten (mellan 3 och 24 gemena bokstäver och unikt i Azure). Du måste också en beskrivning, en etikett (upp till 100 tecken, kodade automatiskt till base64) och en plats. I följande exempel visas hur du skapar en tjänst genom att ange en plats:

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

I föregående exempel, status för den **skapa\_storage\_konto** åtgärden kan hämtas genom att skicka resultatet som returneras av **skapa\_storage\_ kontot** till den **hämta\_åtgärden\_status** metod. 

Du kan ange dina lagringskonton och deras egenskaper med den **lista\_storage\_konton** metod.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Ta bort en lagringstjänst
Om du vill ta bort en lagringstjänst, skicka namnet på storage-tjänsten till den **ta bort\_storage\_konto** metod. Tar bort en lagringstjänst tar bort alla data som lagras i tjänsten (blobs, tabeller och köer).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Lista tillgängliga operativsystem
Om du vill visa de operativsystem som är tillgängliga för värdtjänster, använda den **lista\_fungerar\_system** metod.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Du kan också använda den **lista\_fungerar\_system\_familjer** metod, vilka grupper som operativsystem som familj.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Skapa en operativsystemavbildning
Lägg till en operativsystemavbildning till lagringsplatsen för avbildningen genom att använda den **lägga till\_os\_bild** metod.

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

Visa de operativsystemsavbildningar som är tillgängliga genom att använda den **lista\_os\_avbildningar** metod. Det omfattar alla avbildningar och avbildningar för användaren.

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

## <a name="DeleteVMImage"> </a>Ta bort en operativsystemavbildning
Ta bort en användaravbildning genom att använda den **ta bort\_os\_bild** metod.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Skapa en virtuell dator
Om du vill skapa en virtuell dator, måste du först skapa en [molntjänst](#CreateCloudService). Skapa sedan distributionen av virtuella datorer med hjälp av den **skapa\_virtuella\_datorn\_distribution** metod.

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

## <a name="DeleteVM"> </a>Ta bort en virtuell dator
Om du vill ta bort en virtuell dator måste du först ta bort distributionen med hjälp av den **ta bort\_distribution** metod.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Molntjänsten kan sedan tas bort med hjälp av den **ta bort\_finns\_service** metod.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Skapa en virtuell dator från en avbildade VM-avbildning
Om du vill hämta en datoravbildning av virtuell du först anropa den **avbilda\_vm\_bild** metod.

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

Kontrollera att du har sparat avbildningen genom att använda den **lista\_vm\_avbildningar** API. Kontrollera att avbildningen visas i resultaten.

    images = sms.list_vm_images()

Slutligen skapar den virtuella datorn med hjälp av den fångade avbildningen använder den **skapa\_virtuella\_datorn\_distribution** metod som tidigare, men nu skicka i vm_image_name i stället.

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

Mer information om hur du sammanställer en Linux-dator i den klassiska distributionsmodellen finns [avbilda en virtuell Linux-dator](../virtual-machines/linux/classic/capture-image-classic.md).

Läs mer om att avbilda en Windows-dator i den klassiska distributionsmodellen i [avbilda en Windows-dator](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Nästa steg
Nu när du har lärt dig grunderna i service management, kan du komma åt den [fullständig API-referensdokumentation för Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) och utföra komplexa uppgifter enkelt för att hantera ditt Python-program.

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
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
