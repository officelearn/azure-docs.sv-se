---
title: Etablera enheter för flerhämtning i Azure IoT Hub Device Provisioning Service
description: Så här etablerar du enheter för multitenancy med DPS-instansen (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e0dec0a67ed33186797ccec8066aaad89ceb8dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434739"
---
# <a name="how-to-provision-for-multitenancy"></a>Hur man avsättning för multitenancy 

De allokeringsprinciper som definieras av etableringstjänsten stöder en mängd olika allokeringsscenarier. Två vanliga scenarier är:

* **Geolatency:** När en enhet flyttas mellan platser förbättras nätverksfördröjningen genom att enheten etableras till IoT-hubben närmast varje plats. I det här fallet väljs en grupp IoT-hubbar, som sträcker sig över regioner, för registreringar. **Allokeringsprincipen lägsta svarstid** har valts för dessa registreringar. Den här principen gör att enhetsetableringstjänsten utvärderar enhetsfördröjning och bestämmer garderobs-IoT-hubben från gruppen av IoT-hubbar. 

* **Flerargsinnehavare**: Enheter som används i en IoT-lösning kan behöva tilldelas en specifik IoT-hubb eller grupp av IoT-hubbar. Lösningen kan kräva att alla enheter för en viss klient kommunicerar med en viss grupp IoT-hubbar. I vissa fall kan en klient äga IoT-hubbar och kräva att enheter tilldelas till sina IoT-hubbar.

Det är vanligt att kombinera dessa två scenarier. En IoT-lösning med flera innehavare tilldelas till exempel ofta klientenheter med hjälp av en grupp IoT-hubbar som är utspridda över regioner. Dessa klientenheter kan tilldelas IoT-hubben i den gruppen, som har den lägsta svarstiden baserat på geografisk plats.

Den här artikeln använder ett simulerat enhetsexempel från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) för att visa hur du etablerar enheter i ett scenario med flera lutningar i olika regioner. Du kommer att utföra följande steg i den här artikeln:

* Använd Azure CLI för att skapa två regionala IoT-hubbar **(västra USA** och **östra USA)**
* Skapa en registrering med flera onsdagar
* Använd Azure CLI för att skapa två regionala virtuella Linux-datorer för att fungera som enheter i samma regioner **(västra USA** och **östra USA)**
* Konfigurera utvecklingsmiljön för Azure IoT C SDK på båda virtuella Linux-datorer
* Simulera enheterna för att se att de är etablerade för samma klient i närmaste region.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Krav

* Slutförande av tjänsten [Konfigurera IoT Hub Device Provisioning med snabbstarten för Azure-portalen.](./quick-setup-auto-provision.md)


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Skapa två regionala IoT-hubbar

I det här avsnittet använder du Azure Cloud Shell för att skapa två nya regionala IoT-hubbar i regionerna **västra USA** och **östra USA** för en klient.


1. Använd Azure Cloud Shell för att skapa en resursgrupp med kommandot [az group create.](/cli/azure/group#az-group-create) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

    I följande exempel skapas en resursgrupp med namnet *contoso-us-resource-group* i *regionen eastus.* Vi rekommenderar att du använder den här gruppen för alla resurser som skapas i den här artikeln. Detta kommer att göra städa upp lättare när du är klar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Använd Azure Cloud Shell för att skapa en IoT-hubb i **regionen Eastus** med kommandot [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) IoT-hubben läggs till i *contoso-us-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-east-hub* på *eastus-platsen.* Du måste använda ditt eget unika navnamn i stället för **contoso-east-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Det kan ta några minuter att slutföra det här kommandot.

3. Använd Azure Cloud Shell för att skapa en IoT-hubb i **westus-regionen** med kommandot [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) Det här IoT-navet läggs också till i *contoso-us-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-west-hub* på *westus-platsen.* Du måste använda ditt eget unika navnamn i stället för **contoso-west-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det kan ta några minuter att slutföra det här kommandot.



## <a name="create-the-multitenant-enrollment"></a>Skapa registrering med flera onsdagar

I det här avsnittet skapar du en ny registreringsgrupp för klientenheterna.  

För enkelhetens skull använder den här artikeln [symmetrisk nyckeltysstik](concepts-symmetric-key-attestation.md) med registreringen. För en säkrare lösning, överväg att använda [X.509 certifikat attestation](concepts-security.md#x509-certificates) med en kedja av förtroende.

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna instansen för enhetsetableringstjänst.

2. Välj fliken **Hantera registreringar** och klicka sedan på knappen **Lägg till registreringsgrupp** högst upp på sidan. 

3. I **Gruppen Lägg till registrering**anger du följande information och klickar på knappen **Spara.**

    **Gruppnamn**: Ange **contoso-us-devices**.

    **Attestation typ:** Välj **symmetrisk nyckel**.

    **Generera nycklar automatiskt**: Den här kryssrutan bör redan vara markerad.

    **Välj hur du vill tilldela enheter till hubbar:** Välj **Lägsta svarstid**.

    ![Lägg till registreringsgrupp med flera metriska värden för symmetrisk nyckelattestation](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. I **Lägg till registreringsgrupp**klickar du på **Länka en ny IoT-hubb** för att länka båda dina regionala hubbar.

    **Prenumeration:** Om du har flera prenumerationer väljer du den prenumeration där du skapade de regionala IoT-hubbar.

    **IoT-hubb**: Välj ett av de regionala nav som du har skapat.

    **Åtkomstprincip:** Välj **iothubowner**.

    ![Länka de regionala IoT-hubbar med etableringstjänsten](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. När båda regionala IoT-hubbar har länkats måste du välja dem för registreringsgruppen och klicka på **Spara** för att skapa den regionala IoT-hubben för registreringen.

    ![Skapa den regionala hubbgruppen för registreringen](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. När du har sparat registreringen öppnar du den igen och noterar **primärnyckeln**. Du måste spara registreringen först för att nycklarna ska kunna genereras. Den här nyckeln används för att generera unika enhetsnycklar för båda simulerade enheter senare.


## <a name="create-regional-linux-vms"></a>Skapa regionala virtuella Linux-datorer

I det här avsnittet skapar du två regionala virtuella Linux-datorer (VIRTUELLA datorer). Dessa virtuella datorer kör ett enhetssimuleringsexempel från varje region för att demonstrera enhetsetablering för klientenheter från båda regionerna.

För att underlätta rensningen läggs dessa virtuella datorer till i samma resursgrupp som innehåller de IoT-hubbar som har skapats, *contoso-us-resource-group*. De virtuella datorerna kommer dock att köras i separata regioner (**västra USA** och **östra USA).**

1. I Azure Cloud Shell kör du följande kommando för att skapa en virtuell dator med **östra USA-region** efter att ha gjort följande parameterändringar i kommandot:

    **--name**: Ange ett unikt namn för den virtuella datorn för den regionala enheten **i östra USA.** 

    **--admin-användarnamn**: Använd ditt eget administratörsanvändarnamn.

    **--admin-password**: Använd ditt eget administratörslösenord.

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

    Kommandot tar fem minuter att slutföra. När kommandot har slutförts, anteckna **värdet för publicIpAddress** för din virtuella dator med östra USA-region.

1. I Azure Cloud Shell kör du kommandot för att skapa en virtuell dator med flera regioner i **västra USA** efter att ha gjort följande parameterändringar i kommandot:

    **--name**: Ange ett unikt namn för den virtuella datorn för den virtuella datorn för den virtuella datorn **i västra USA.** 

    **--admin-användarnamn**: Använd ditt eget administratörsanvändarnamn.

    **--admin-password**: Använd ditt eget administratörslösenord.

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

    Kommandot tar fem minuter att slutföra. När kommandot har slutförts, anteckna **värdet för publicIpAddress** för din virtuella virtuella virtuella väst-USA-region.

1. Öppna två kommandoradsskal. Anslut till en av de regionala virtuella datorerna i varje skal med SSH. 

    Skicka ditt administratörsanvändarnamn och den offentliga IP-adressen som du noterade för den virtuella datorn som parametrar till SSH. Ange administratörslösenordet när du uppmanas att göra det.

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

1. För varje virtuell dator installerar du **CMake**, **g++**, **gcc**och [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) med följande kommandon:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Leta reda på taggnamnet för den [senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

1. Klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) på båda virtuella datorerna.  Använd taggen som du hittade i föregående `-b` steg som värde för parametern:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. För båda virtuella datorerna skapar du en ny **cmake-mapp** i databasen och ändrar till den mappen.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. För båda virtuella datorerna kör du följande kommando, som bygger en version av SDK som är specifik för utvecklingsklientplattformen. 

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


## <a name="derive-unique-device-keys"></a>Härleda unika enhetsnycklar

När du använder symmetrisk nyckeltysstik med gruppregistreringar använder du inte registreringsgruppsnycklarna direkt. I stället skapar du en unik härledd nyckel för varje enhet och som nämns i [Gruppregistreringar med symmetriska nycklar](concepts-symmetric-key-attestation.md#group-enrollments).

Om du vill generera enhetsnyckeln använder du grupphuvudnyckeln för att beräkna ett [HMAC-SHA256-format](https://wikipedia.org/wiki/HMAC) för det unika registrerings-ID:t för enheten och konverterar resultatet till Base64-format.

Ta inte med grupphuvudnyckeln i enhetskoden.

Använd exemplet Bash-skal för att skapa en härledd enhetsnyckel för varje enhet med **openssl**.

- Ersätt värdet för **KEY** med den **primärnyckel som** du noterade tidigare för registreringen.

- Ersätt värdet för **REG_ID** med ditt eget unika registrerings-ID för varje enhet. Använd gemener alfanumeriska och streck ('-') tecken för att definiera båda ID: er.

Exempel på generering av *enhetsnyckel för contoso-simdevice-east:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Exempel på generering av *enhetsnyckel för contoso-simdevice-west:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Klientenheterna använder var sin härledda enhetsnyckel och unika registrerings-ID för att utföra symmetrisk nyckeltysstation med registreringsgruppen under etablering till klient-IoT-hubbar.




## <a name="simulate-the-devices-from-each-region"></a>Simulera enheterna från varje region


I det här avsnittet uppdaterar du ett etableringsexempel i Azure IoT C SDK för båda de regionala virtuella datorerna. 

Exempelkoden simulerar en enhetsstartsekvens som skickar etableringsbegäran till instansen av enhetsetableringstjänsten. Startsekvensen gör att enheten identifieras och tilldelas IoT-hubben som är närmast baserat på svarstid.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Öppna **\_~/azure-iot-sdk-c/tablering klient/sampling/prov\_\_\_\_\_dev-klientexempel/prov\_dev-klient sample.c** för redigering på båda virtuella datorerna.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att `hsm_type` variabeln `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` är inställd på som visas nedan för att matcha registreringsgruppsintygsmetoden. 

    Spara ändringarna i filerna på båda virtuella datorerna.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. På båda virtuella datorer, `prov_dev_set_symmetric_key_info()` hitta samtalet till i **prov\_dev\_klient\_sample.c** som kommenteras ut.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Avkommentera funktionsanropen och ersätt platshållarvärdena (inklusive vinkelparenteserna) med de unika registrerings-ID:na och de härledda enhetsnycklarna för varje enhet. Nycklarna som visas nedan är endast i exempelvis syften. Använd nycklarna som du genererade tidigare.

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

1. På båda virtuella datorerna navigerar du till exempelmappen som visas nedan och skapar exemplet.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. När build lyckas kör du **prov\_dev-klientexit.exe\_\_** på båda virtuella datorerna för att simulera en klientenhet från varje region. Observera att varje enhet allokeras till klient-IoT-hubben närmast den simulerade enhetens regioner.

    Kör simuleringen:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exempel på utdata från den virtuella datorn för östra USA:

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

    Exempel på utdata från den virtuella datorn för västra USA:
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

Om du planerar att fortsätta arbeta med resurser som skapats i den här artikeln kan du lämna dem. Om du inte planerar att fortsätta använda resursen använder du följande steg för att ta bort alla resurser som skapas av den här artikeln för att undvika onödiga avgifter.

Stegen här förutsätter att du har skapat alla resurser i den här artikeln enligt anvisningarna i samma resursgrupp med namnet **contoso-us-resource-group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Så här tar du bort resursgruppen efter namn:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

2. I **textrutan Filter efter namn...** skriver du namnet på resursgruppen som innehåller dina resurser, **contoso-us-resource-group**. 

3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

- Mer ometablering finns i [IoT Hub Device reetableing concepts](concepts-device-reprovision.md) 
- Mer information om avetablering finns i [Så här avetablering av enheter som tidigare var automatiskt etablerade](how-to-unprovision-devices.md) 











