---
title: "Hur du använder service management API (Python) - Funktionsguide"
description: "Lär dig hur du programmässigt utföra vanliga hanteringsuppgifter för tjänsten från Python."
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 13249ba9a4b317a3154776b411ce0bb1f316b3bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-management-from-python"></a>Hur du använder Service Management från Python
Den här guiden visar hur du programmässigt utföra vanliga hanteringsuppgifter för tjänsten från Python. Den **ServiceManagementService** klassen i den [Azure SDK för Python](https://github.com/Azure/azure-sdk-for-python) stöder Programmeringsåtkomst till stor del av service management-relaterade funktioner som är tillgängliga i den [Azure klassiska portalen] [ management-portal] (exempelvis **skapa, uppdatera och ta bort molntjänster, distributioner, tjänster för data och virtuella datorer**). Den här funktionen kan vara användbar vid utveckling av program som behöver programmatisk åtkomst till service management.

## <a name="WhatIs"></a>Vad är Service Management
Service Management API som ger programmatisk åtkomst till stor del av hanteringsfunktioner för tjänsten via den [klassiska Azure-portalen][management-portal]. Azure SDK för Python kan du hantera dina molntjänster och storage-konton.

Service Management API du vill använda, [skapa ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Begrepp
Azure SDK för Python radbryts i [Azure Service Management API][svc-mgmt-rest-api], vilket är en REST-API. Alla API-operationer utförs över SSL och autentiserar varandra med X.509 v3-certifikat. Hanteringstjänsten kan nås inifrån en tjänst som körs i Azure eller direkt över Internet från alla program som kan skicka en HTTPS-begäran och ta emot HTTPS-svar.

## <a name="Installation"></a>Installation
De funktioner som beskrivs i den här artikeln är tillgängliga i den `azure-servicemanagement-legacy` paket som du kan installera med hjälp av pip. Mer information om installation (till exempel om du har använt Python) finns i den här artikeln: [installerar Python och Azure SDK](../python-how-to-install.md)

## <a name="Connect"></a>Så här: ansluta till service-hantering
Om du vill ansluta till Service Management-slutpunkten måste Azure prenumerations-ID och ett giltigt certifikat. Du kan hämta ditt prenumerations-ID via den [klassiska Azure-portalen][management-portal].

> [!NOTE]
> Nu är det möjligt att använda certifikat som skapas med OpenSSL när de körs på Windows.  Det krävs Python 2.7.4 eller senare. Vi rekommenderar att användarna kan använda OpenSSL i stället för PFX, eftersom stöd för PFX-certifikat kommer troligen att tas bort i framtiden.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Management-certifikat på Windows-/ Mac/Linux (OpenSSL)
Du kan använda [OpenSSL](http://www.openssl.org/) att skapa management-certifikat.  Faktiskt måste du skapa två certifikat, en för servern (en `.cer` filen) och en för klienten (en `.pem` fil). Att skapa den `.pem` fil, kör:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Att skapa den `.cer` certifikat, kör:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Läs mer om Azure certifikat [certifikat översikt för Azure Cloud Services](cloud-services-certs-create.md). En fullständig beskrivning av OpenSSL parametrar finns i dokumentationen på [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

När du har skapat de här filerna måste du ladda upp den `.cer` filen till Azure via ”överför”-åtgärd på fliken ”Inställningar” i den [klassiska Azure-portalen][management-portal], och du behöver anteckna var du Spara den `.pem` filen.

När du har fått ditt prenumerations-ID, skapas ett certifikat och överföra den `.cer` filen till Azure, som du kan ansluta till Azure management-slutpunkten genom att skicka prenumerations-id och sökvägen till den `.pem` filen till  **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

I föregående exempel `sms` är en **ServiceManagementService** objekt. Den **ServiceManagementService** klassen är den primära klassen som används för att hantera Azure-tjänster.

### <a name="management-certificates-on-windows-makecert"></a>Hanteringscertifikat i Windows (MakeCert)
Du kan skapa ett självsignerat certifikat på datorn med hjälp av `makecert.exe`.  Öppna en **Kommandotolken Visual Studio** som en **administratör** och använder du följande kommando ersätter *AzureCertificate* med certifikatets namn som du vill använda.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Kommandot skapar den `.cer` filen och installerar den i den **personliga** certifikatarkiv. Mer information finns i [certifikat översikt för Azure Cloud Services](cloud-services-certs-create.md).

När du har skapat certifikatet, måste du ladda upp den `.cer` filen till Azure via ”överför”-åtgärd på fliken ”Inställningar” i den [klassiska Azure-portalen][management-portal].

När du har fått ditt prenumerations-ID, skapas ett certifikat och överföra den `.cer` filen till Azure, som du kan ansluta till Azure management-slutpunkten genom att skicka prenumerations-id och platsen för certifikatet i din **personliga**  certifikatarkiv **ServiceManagementService** (igen och Ersätt *AzureCertificate* med namnet på ditt certifikat):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

I föregående exempel `sms` är en **ServiceManagementService** objekt. Den **ServiceManagementService** klassen är den primära klassen som används för att hantera Azure-tjänster.

## <a name="ListAvailableLocations"></a>Så här: visa en lista över tillgängliga platser
Om du vill visa de platser som är tillgängliga för värdtjänster använder den **lista\_platser** metoden:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

När du skapar en tjänst i molnet eller storage-tjänst som du måste ange en giltig plats. Den **lista\_platser** -metoden returnerar alltid en aktuell lista över tillgängliga platser. När detta skrivs är tillgängliga platser:

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

## <a name="CreateCloudService"></a>Så här: skapa en tjänst i molnet
När du skapar ett program och kör det i Azure, koden och konfigurationen tillsammans kallas för en Azure [Molntjänsten] [ cloud service] (kallas även en *värdtjänsten* i tidigare Azure versioner). Den **skapa\_finns\_service** metod kan du skapa en ny värdbaserad tjänst genom att tillhandahålla en värdtjänst namnet (måste vara unikt i Azure), en etikett (kodad automatiskt till base64), en beskrivning och en plats.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Du kan visa en lista med alla värdbaserade tjänster för din prenumeration med den **lista\_finns\_services** metoden:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Om du vill få information om en viss värdbaserad tjänst du göra detta genom att skicka värdbaserade tjänstens namn till den **hämta\_finns\_service\_egenskaper** metoden:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

När du har skapat en tjänst i molnet, kan du distribuera din kod till tjänsten med den **skapa\_distribution** metod.

## <a name="DeleteCloudService"></a>Så här: ta bort en tjänst i molnet
Du kan ta bort en tjänst i molnet genom att skicka tjänstnamnet som ska den **ta bort\_finns\_service** metoden:

    sms.delete_hosted_service('myhostedservice')

Innan du kan ta bort en tjänst måste du först bort alla distributioner för tjänsten. (Se [så här: ta bort en distribution](#DeleteDeployment) mer information.)

## <a name="DeleteDeployment"></a>Så här: ta bort en distribution
Ta bort en distribution genom att använda den **ta bort\_distribution** metod. I följande exempel visas hur du tar bort en distribution med namnet `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>Så här: skapa en storage-tjänst
En [lagringstjänsten](../storage/common/storage-create-storage-account.md) ger dig åtkomst till Azure [Blobbar](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabeller](../cosmos-db/table-storage-how-to-use-python.md), och [köer](../storage/queues/storage-python-how-to-use-queue-storage.md). Om du vill skapa en lagringstjänsten, behöver du ett namn för tjänsten (mellan 3 och 24 gemener och unikt i Azure), en beskrivning, en etikett (upp till 100 tecken, kodade automatiskt till base64) och en plats. I följande exempel visas hur du skapar en storage-tjänst genom att ange en plats.

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

Observera i föregående exempel som status för den **skapa\_lagring\_konto** åtgärden kan hämtas genom att skicka resultatet som returneras av **skapa\_lagring\_konto** till den **hämta\_åtgärden\_status** metod.  

Du kan visa dina lagringskonton och deras egenskaper med den **lista\_lagring\_konton** metoden:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>Så här: ta bort storage-tjänst
Du kan ta bort storage-tjänst genom att skicka namnet på lagring till det **ta bort\_lagring\_konto** metoden. Tar bort en lagringstjänsten alla data som lagras i tjänsten (blobbar, tabeller och köer).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>Så här: visa en lista över tillgängliga operativsystem
Om du vill visa en lista över de operativsystem som är tillgängliga för värdtjänster, Använd den **lista\_operativsystem\_system** metoden:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Du kan också använda den **lista\_operativsystem\_system\_familjer** metoden vilka grupper som operativsystem som familj:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>Så här: skapa en operativsystemavbildning
Lägg till en operativsystemavbildning avbildningslagringsplatsen genom att använda den **lägga till\_os\_bild** metoden:

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

Om du vill visa de operativsystemsavbildningar som är tillgängliga att använda den **lista\_os\_bilder** metod. Det innehåller alla plattform bilder och användaren bilder:

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

## <a name="DeleteVMImage"></a>Så här: ta bort en operativsystemavbildning
Ta bort en användaravbildning genom att använda den **ta bort\_os\_bild** metoden:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>Så här: skapa en virtuell dator
Om du vill skapa en virtuell dator, måste du först skapa en [Molntjänsten](#CreateCloudService).  Skapa sedan den virtuella datorn med den **skapa\_virtuella\_datorn\_distribution** metoden:

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

## <a name="DeleteVM"></a>Så här: ta bort en virtuell dator
Om du vill ta bort en virtuell dator måste du först ta bort en distribution med hjälp av den **ta bort\_distribution** metoden:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Molntjänsten kan sedan tas bort med den **ta bort\_finns\_service** metoden:

    sms.delete_hosted_service(service_name='myvm')

## <a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Så här: Skapa en virtuell dator från en avbildning av den fångade virtuell dator
Om du vill göra en VM-avbildning måste du först anropa den **avbilda\_vm\_bild** metoden:

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

Kontrollera att du har sparat avbildningen, Använd den **listan\_vm\_bilder** api, och kontrollera att bilden visas i resultaten:

    images = sms.list_vm_images()

Använd för att skapa den virtuella datorn med avbildningen slutligen den **skapa\_virtuella\_datorn\_distribution** metod som tidigare, men nu skicka in vm_image_name i stället

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

Mer information om hur du skapar en Linux-dator finns [så här skapar du en virtuell Linux-dator.](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

Mer information om hur du skapar en Windows-dator finns [så här skapar du en virtuell Windows-dator.](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="What's Next"> </a>Nästa steg
Nu när du har lärt dig grunderna om service-hantering, kan du komma åt den [fullständig API-referensdokumentationen för Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) och utföra komplicerade uppgifter enkelt om du vill hantera python-programmet.

Mer information finns i [Python Developer Center](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
