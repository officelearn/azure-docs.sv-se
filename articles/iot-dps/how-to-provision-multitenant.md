---
title: Hur du etablerar enheter för flera innehavare i Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Hur du etablerar enheter för flera innehavare med din device provisioning-tjänstinstans
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 43989ff9dac7cdad76117aaa6e3c862453b24813
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865118"
---
# <a name="how-to-provision-for-multitenancy"></a>Hur du etablerar för flera innehavare 

Resursallokeringsprinciper som definieras av etableringstjänsten stöder en mängd olika scenarier för allokering. Det finns två vanliga scenarier:

* **Geoplats / GeoLatency**: När en enhet flyttas mellan platser, bättre svarstid för nätverk genom att låta enheten etableras till den IoT-hubben som är närmast varje plats. En grupp med IoT-hubbar som sträcker sig över flera regioner, väljs i det här scenariot för registreringar. Den **kortast svarstid** allokeringsprincip har valts för dessa registreringar. Den här principen gör Device Provisioning-tjänsten att utvärdera enhetens svarstid och fastställa förstås IoT hub från gruppen med IoT-hubbar. 

* **Multitenans**: Enheter som används i en IoT-lösning kan behöva tilldelas till den specifika IoT-hubb eller en grupp av IoT-hubbar. Lösningen kan kräva att alla enheter för en viss klient kan kommunicera med en viss grupp av IoT-hubbar. I vissa fall kan en klient äger IoT-hubbar och Kräv att enheter som ska tilldelas till sina IoT-hubbar.

Det är vanligt att kombinera dessa två scenarier. Till exempel tilldelar en multitenant IoT-lösning ofta klient-enheter med hjälp av en grupp med IoT-hubbar som är spridda över regioner. Enheterna klient kan tilldelas till IoT hub i den grupp som har kortast svarstid baserat på geografisk plats.

Den här artikeln använder en simulerad enhet exempel från den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) att demonstrera hur du etablerar enheter i ett scenario med flera innehavare i regioner. Du utför följande steg i den här artikeln:

* Använda Azure CLI för att skapa två regionala IoT-hubbar (**västra USA** och **USA, östra**)
* Skapa en multitenant registrering
* Använda Azure CLI för att skapa två regionala virtuella Linux-datorer så att den fungerar som enheter i samma regioner (**västra USA** och **USA, östra**)
* Konfigurera utvecklingsmiljön för Azure IoT C SDK på både virtuella Linux-datorer
* Simulera enheter för att se att de har etablerats för samma klient i den närmaste regionen.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Förutsättningar

* Slutförandet av den [konfigurera IoT Hub Device Provisioning-tjänsten med Azure portal](./quick-setup-auto-provision.md) Snabbstart.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Skapa två regionala IoT-hubbar

I det här avsnittet ska du använda Azure Cloud Shell för att skapa två nya regionala IoT-hubbar i den **västra USA** och **USA, östra** regioner för en klient.


1. Använda Azure Cloud Shell för att skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az-group-create) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

    I följande exempel skapas en resursgrupp med namnet *contoso-oss-resource-group* i den *eastus* region. Du rekommenderas att du använder den här gruppen för alla resurser som skapats i den här artikeln. Detta gör Rensa enklare när du är klar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Använda Azure Cloud Shell för att skapa en IoT hub i den **eastus** region med de [az iot hub skapa](/cli/azure/iot/hub#az-iot-hub-create) kommando. IoT-hubben ska läggas till i *contoso-oss-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-Öst-hub* i den *eastus* plats. Du måste använda en egen unik hubbnamn i stället för **contoso-Öst-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Det här kommandot kan ta några minuter att slutföra.

3. Använda Azure Cloud Shell för att skapa en IoT hub i den **westus** region med de [az iot hub skapa](/cli/azure/iot/hub#az-iot-hub-create) kommando. Den här IoT-hubben läggs också till den *contoso-oss-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-Väst-hub* i den *westus* plats. Du måste använda en egen unik hubbnamn i stället för **contoso-Väst-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det här kommandot kan ta några minuter att slutföra.



## <a name="create-the-multitenant-enrollment"></a>Skapa multitenant registrering

I det här avsnittet skapar du en ny grupp för registrering för klient-enheter.  

För enkelhetens skull använder den här artikeln [symmetriska nyckelattestering](concepts-symmetric-key-attestation.md) med registreringen. Överväg att använda för en säkrare lösningen [X.509-certifikat attestering](concepts-security.md#x509-certificates) med en certifikatkedja.

1. Logga in på den [Azure-portalen](http://portal.azure.com), och öppna Device Provisioning Service-instans.

2. Välj den **hantera registreringar** fliken och klicka sedan på den **Lägg till grupp för registrering** längst upp på sidan. 

3. På **lägga till Registreringsgruppen**, anger du följande information och klicka på den **spara** knappen.

    **Gruppnamn**: Ange **contoso-USA-enheter**.

    **Typ av attestering**: Välj **symmetrisk nyckel**.

    **Generera nycklar automatiskt**: Den här kryssrutan bör redan vara markerad.

    **Välj hur du vill tilldela enheter till hubs**: Välj **kortast svarstid**.

    ![Lägg till flera innehavare registreringsgrupp för symmetrisk nyckelattestering](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. På **lägga till Registreringsgruppen**, klickar du på **länka en ny IoT hub** länka båda dina regionala hubbar.

    **Prenumeration**: Om du har flera prenumerationer väljer du den prenumeration där du skapade de regionala IoT-hubbarna.

    **IoT hub**: Välj en av de regionala hubbar som du skapade.

    **Princip för**: Välj **iothubowner**.

    ![Länka regionala IoT-hubbar med etableringstjänsten](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. När båda regionala IoT-hubbar har länkats, måste du välja dem för registreringsgruppen och klicka på **spara** skapa regionala IoT hub-gruppen för registrering.

    ![Skapa regionala hub-grupp för registrering](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. När du har sparat registreringen, öppna den igen och anteckna den **primärnyckel**. Du måste spara registreringen först om du vill att nyckeln som skapas. Den här nyckeln används för att generera unika enhetsnycklar för både simulerade enheter senare.


## <a name="create-regional-linux-vms"></a>Skapa regionala virtuella Linux-datorer

I det här avsnittet skapar du två regionala virtuella Linux-datorer (VM). Dessa virtuella datorer körs ett enheten simulering exempel från varje region för att demonstrera enhetsetablering för klient-enheter från båda regionerna.

Att rensa enklare, dessa virtuella datorer kommer att läggas till samma resursgrupp som innehåller IoT-hubbar som har skapats, *contoso-oss-resource-group*. Men de virtuella datorerna körs i olika områden (**västra USA** och **USA, östra**).

1. I Azure Cloud Shell, kör du följande kommando för att skapa en **USA, östra** region virtuell dator när du har gjort följande ändringar för parametern i kommandot:

    **--name**: Ange ett unikt namn för din **USA, östra** regionala enheten VM. 

    **--admin-username**: Använd ditt eget användarnamn för administratör.

    **--admin-password**: Använd din egen administratörslösenord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Det här kommandot tar några minuter att slutföra. När kommandot har slutförts, notera den **publicIpAddress** värde för din region för USA, östra VM.

1. I Azure Cloud Shell, kör du kommandot för att skapa en **västra USA** region virtuell dator när du har gjort följande ändringar för parametern i kommandot:

    **--name**: Ange ett unikt namn för din **västra USA** regionala enheten VM. 

    **--admin-username**: Använd ditt eget användarnamn för administratör.

    **--admin-password**: Använd din egen administratörslösenord.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Det här kommandot tar några minuter att slutföra. När kommandot har slutförts, notera den **publicIpAddress** värde för USA, västra region VM.

1. Öppna två kommandoradsverktyget gränssnitt. Ansluta till en av de regionala virtuella datorerna i varje gränssnitt med SSH. 

    Skicka ditt administratörsanvändarnamn och offentliga IP-adress du antecknade för den virtuella datorn som parametrar för SSH. Ange lösenord för serveradministratören när du uppmanas till detta.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Förbereda utvecklingsmiljön för Azure IoT C SDK

I det här avsnittet ska du klona Azure IoT C SDK på varje virtuell dator. SDK innehåller ett exempel som simulerar en klients enhetsetablering från varje region.


1. För varje virtuell dator, installera **Cmake**, **g ++**, **gcc**, och [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) med hjälp av följande kommandon:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. Klona den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) på bägge VM: ar.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Storleken på den här lagringsplatsen är för närvarande cirka 220 MB. Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. För båda virtuella datorerna, skapa en ny **cmake** mapp inuti lagringsplatsen och ändra till mappen.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. För båda virtuella datorerna, kör du följande kommando, vilket skapar en version av SDK som är specifika för din utvecklingsplattform för klienten. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Härled unika enhetsnycklar

När du använder symmetrisk nyckelattestering med gruppregistreringar kan använder du inte gruppnycklarna registrering direkt. I stället skapar du ett unikt härledd nyckel för varje enhet och nämns i [gruppregistreringar med symmetriska nycklar](concepts-symmetric-key-attestation.md#group-enrollments).

Generera enhetsnyckeln genom att använda huvudnyckeln grupp för att beräkna en [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) av unika registrerings-ID för enheten och konvertera resultatet i Base64-format.

Ta inte med din grupp huvudnyckel i din kod för enheten.

Använd Bash shell-exempel för att skapa en härledd enhetsnyckel för varje enhet med hjälp av **openssl**.

- Ersätt värdet för **nyckel** med den **primärnyckel** du antecknade tidigare för din registrering.

- Ersätt värdet för **REG_ID** med dina egna unika registrerings-ID för varje enhet. Använda alfanumeriska gemener och bindestreck (”-”) tecken för att definiera både ID: N.

Exempel enheten nyckelgenerering för *contoso simdevice östra*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Exempel enheten nyckelgenerering för *contoso simdevice västra*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Klient-enheter kommer varje använda sin härledda enhetsnyckel och unika registrerings-ID för symmetrisk nyckelattestering med registreringsgruppen under etableringen för klienten IoT hubs.




## <a name="simulate-the-devices-from-each-region"></a>Simulera enheterna från varje region


I det här avsnittet ska uppdaterar du ett etablering exempel i Azure IoT C SDK för båda de regionala virtuella datorerna. 

Exempelkoden simulerar en startsekvens för enheten som skickar en begäran om etablering till din instans av Device Provisioning-tjänsten. Startsekvens medför att enheten ska identifieras och tilldelad till IoT-hubben som är närmast baserat på svarstid.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Öppna **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_klienten\_exemplet/prov\_dev\_klienten\_sample.c**för redigering på bägge VM: ar.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att den `hsm_type` variabeln anges till `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` enligt nedan så att de matchar registreringsmetoden för attestering av gruppen. 

    Spara dina ändringar i filerna på bägge VM: ar.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Hitta anropet till på bägge VM: ar, `prov_dev_set_symmetric_key_info()` i **prov\_dev\_klienten\_sample.c** som har kommenterats bort.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ta bort kommentarerna funktionsanropen och Ersätt platshållarvärdena (inklusive hakparenteser) med unika registrerings-ID: N och härledda enhetsnycklar för varje enhet. Nycklar som visas nedan är till exempel endast. Använd nycklar som du skapade tidigare.

    Östra USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Västra USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Spara filerna.

1. Navigera till exempelmappen visas nedan på bägge VM: ar, och skapa exemplet.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. När den har byggts köra **prov\_dev\_klienten\_sample.exe** på bägge VM: ar för att simulera en klient-enhet från varje region. Observera att varje enhet tilldelas klienten IoT-hubb som är närmast den simulerade enheten regioner.

    Köra simuleringen:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exempel utdata från östra USA virtuell dator:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Exempel utdata från västra USA virtuell dator:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med resurser som skapats i den här artikeln kan du lämna dem. Om du inte tänker fortsätta att använda resursen kan du använda följande steg för att ta bort alla resurser som skapats av den här artikeln för att undvika onödiga avgifter.

De här stegen förutsätter att alla resurser som du skapade i den här artikeln som finns beskrivet i samma resursgrupp med namnet **contoso-oss-resource-group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Ta bort resursgruppen med namnet:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

2. I den **filtrera efter namn...**  textrutan skriver namnet på resursen gruppen som innehåller dina resurser, **contoso-oss-resource-group**. 

3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

- Läs mer Reprovisioning i [IoT Hub Device reprovisoning begrepp](concepts-device-reprovision.md) 
- Läs mer avetablering i [så ta bort etableringen av enheter som har tidigare Automatisk etablering](how-to-unprovision-devices.md) 











