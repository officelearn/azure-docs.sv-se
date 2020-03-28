---
title: 'Självstudiekurs: Konfigurera IoT Edge-enhet – Machine Learning på Azure IoT Edge'
description: I den här självstudien konfigurerar du en Virtuell Azure-dator som kör Linux som en Azure IoT Edge-enhet som fungerar som en transparent gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d2bad581c925bb62cbe65a45000f6d3ae35db011
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372680"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Självstudiekurs: Konfigurera en IoT Edge-enhet

> [!NOTE]
> Den här artikeln är en del av en serie för en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt, uppmuntrar vi dig att börja med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln konfigurerar vi en virtuell Azure-dator som kör Linux till en IoT Edge-enhet som fungerar som en transparent gateway. En transparent gateway-konfiguration gör det möjligt för enheter att ansluta till Azure IoT Hub via gatewayen utan att veta att gatewayen finns. Samtidigt är en användare som interagerar med enheterna i Azure IoT Hub omedveten om den mellanliggande gateway-enheten. I slutändan kommer vi att lägga till edge analytics till vårt system genom att lägga till IoT Edge moduler till den transparenta gatewayen.

Stegen i den här artikeln utförs vanligtvis av en molnutvecklare.

## <a name="create-certificates"></a>Skapa certifikat

För att en enhet ska fungera som en gateway måste den kunna ansluta till nedströmsenheter på ett säkert sätt. Med Azure IoT Edge kan du använda en infrastruktur för offentliga nycklar (PKI) för att konfigurera säkra anslutningar mellan enheter. I det här fallet tillåter vi en nedströms IoT-enhet att ansluta till en IoT Edge-enhet som fungerar som en transparent gateway. För att upprätthålla rimlig säkerhet bör nedströmsenheten bekräfta IoT Edge-enhetens identitet. Mer information om hur IoT Edge-enheter använder certifikat finns i [Information om användningsinformation för Azure IoT Edge-certifikat](iot-edge-certs.md).

I det här avsnittet skapar vi de självsignerade certifikaten med hjälp av en Docker-avbildning som vi sedan bygger och kör. Vi valde att använda en Docker-avbildning för att slutföra det här steget eftersom det avsevärt minskar antalet steg som behövs för att skapa certifikaten på Windows-utvecklingsmaskinen. Se [Skapa democertifikat för att testa IoT Edge-enhetsfunktioner](how-to-create-test-certificates.md) för att förstå vad vi automatiserade med Docker-avbildningen.

1. Logga in på din utvecklings-VM.

2. Skapa en ny mapp med `c:\edgeCertificates`sökvägen och namnet .

3. Om du inte redan körs startar **du Docker för Windows** på Start-menyn i Windows.

4. Öppna Visual Studio Code.

5. Välj **Fil** > **öppen mapp...** och välj **C:\\\\källa IoTEdgeAndMlSample\\CreateCertificates**.

6. Högerklicka på **dockerfile** i utforskarfönstret och välj **Skapa avbildning**.

7. I dialogrutan godkänner du standardvärdet för bildnamnet och taggen: **createcertificates: senaste**.

    ![Skapa certifikat i Visual Studio-kod](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Vänta tills bygget är klart.

    > [!NOTE]
    > Du kan se en varning för om en saknad offentlig nyckel. Det är säkert att ignorera denna varning. På samma sätt visas en säkerhetsvarning som rekommenderar att du kontrollerar/återställer behörigheter för din bild, vilket är säkert att ignorera för den här bilden.

9. I terminalfönstret Visual Studio-kod kör du behållaren för createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker uppmanar till åtkomst till **\\ c:-enheten.** Välj **Dela den**.

11. Ange dina autentiseringsuppgifter när du uppmanas att göra det.

12. När behållaren har körts klart kontrollerar du om det finns följande filer i **c:\\edgeCertificates:**

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\\\edgeCertificates\\privata nya-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Ladda upp certifikat till Azure Key Vault

Om du vill lagra våra certifikat på ett säkert sätt och göra dem tillgängliga från flera enheter överför vi certifikaten till Azure Key Vault. Som ni kan se i listan ovan har vi två typer av certifikatfiler: PFX och PEM. Vi kommer att behandla PFX som Key Vault-certifikat som ska laddas upp till Key Vault. PEM-filerna är oformaterad text och vi kommer att behandla dem som Key Vault hemligheter. Vi använder nyckelvalvet som är associerat med arbetsytan Azure Machine Learning som vi skapade genom att köra [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Från [Azure-portalen](https://portal.azure.com)navigerar du till arbetsytan Azure Machine Learning.

2. På översiktssidan på arbetsytan Azure Machine Learning hittar du namnet på **Key Vault**.

    ![Kopiera namn på nyckelvalv](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Ladda upp certifikaten till Key Vault på utvecklingsdatorn. Ersätt ** \<subscriptionId\> ** och ** \<keyvaultname\> ** med din resursinformation.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Logga in på Azure om du uppmanas att göra det.

5. Skriptet körs i några minuter med utdata som listar de nya Key Vault-posterna.

    ![Skriva utdata för Key Vault-skript](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Skapa IoT Edge-enhet

Om du vill ansluta en Azure IoT Edge-enhet till en IoT-hubb skapar vi först en identitet för enheten i navet. Vi tar anslutningssträngen från enhetsidentiteten i molnet och använder den för att konfigurera körningen på vår IoT Edge-enhet. När en konfigurerad enhet ansluter till navet kan vi distribuera moduler och skicka meddelanden. Vi kan också ändra konfigurationen av den fysiska IoT Edge-enheten genom att ändra motsvarande enhetsidentitet i IoT-hubben.

För den här självstudien skapar vi den nya enhetsidentiteten med Visual Studio Code. Du kan också utföra dessa steg med [Azure-portalen](how-to-register-device.md#register-in-the-azure-portal)eller [Azure CLI](how-to-register-device.md#register-with-the-azure-cli).

1. Öppna Visual Studio Code på utvecklingsmaskinen.

2. Expandera **Azure IoT Hub-ramen** från utforskarvyn för Visual Studio-kod.

3. Klicka på ellipsen och välj **Skapa IoT Edge Device**.

4. Ge enheten ett namn. För enkelhetens skull använder vi namnet **aaTurbofanEdgeDevice** så det sorteras till toppen av listade enheter.

5. Den nya enheten visas i listan över enheter.

    ![Visa nya aaTurbofanEdgeDevice i VS Code explorer](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Distribuera virtuell Azure-dator

Vi använder [Azure IoT Edge på Ubuntu-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) från Azure Marketplace för att skapa vår IoT Edge-enhet för den här självstudien. Azure IoT Edge på Ubuntu-avbildningen installerar den senaste Azure IoT Edge-körningen och dess beroenden vid start. Vi distribuerar den virtuella datorn `Create-EdgeVM.ps1`med ett PowerShell-skript, ; en Resource Manager-mall. `IoTEdgeVMTemplate.json` och ett skalskript, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Aktivera programmatisk distribution

Om du vill använda avbildningen från Marketplace i en skriptdistribution måste vi aktivera programmatisk distribution för avbildningen.

1. Logga in på Azure Portal.

1. Välj **Alla tjänster**.

1. Ange och välj **Marketplace**i sökfältet .

1. I Sökfältet på Marketplace anger du och väljer **Azure IoT Edge på Ubuntu**.

1. Välj hyperlänken **Kom igång** för att distribuera programmässigt.

1. Välj knappen **Aktivera** och sedan **Spara**.

    ![Aktivera programmatisk distribution för virtuell dator](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Du kommer att se en framgång anmälan.

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

Kör sedan skriptet för att skapa den virtuella datorn för din IoT Edge-enhet.

1. Öppna ett PowerShell-fönster och navigera till **EdgeVM-katalogen.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Kör skriptet för att skapa den virtuella datorn.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Ange värden för varje parameter när du uppmanas att göra det. För prenumeration, resursgrupp och plats rekommenderar vi att du använder samma som du har för alla resurser i hela den här självstudien.

    * **Azure-prenumerations-ID:** finns i Azure-portalen
    * **Namn på resursgrupp:** minnesvärt namn för att gruppera resurserna för den här självstudien
    * **Plats**: Azure-plats där den virtuella datorn ska skapas. Till exempel westus2 eller northeurope. Mer information finns på alla [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: namnet på admin-kontot som du ska använda för att logga in på den virtuella datorn
    * **AdminPassword**: lösenordet för att ställa in för AdminUsername på den virtuella maskinen

4. För att skriptet ska kunna konfigurera den virtuella datorn måste du logga in på Azure med de autentiseringsuppgifter som är associerade med Den Azure-prenumeration som du använder.

5. Skriptet bekräftar informationen för att skapa den virtuella datorn. Välj **y** eller **Retur** om du vill fortsätta.

6. Skriptet körs i flera minuter när följande körs:

    * Skapa resursgruppen om den inte redan finns
    * Skapa den virtuella datorn
    * Lägg till NSG-undantag för den virtuella datorn för portarna 22 (SSH), 5671 (AMQP), 5672 (AMPQ) och 443 (TLS)
    * Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Skriptet matar ut SSH-anslutningssträngen för anslutning till den virtuella datorn. Kopiera anslutningssträngen för nästa steg.

    ![Kopiera SSH-anslutningssträng för virtuell dator](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ansluta till din IoT Edge-enhet

Nästa avsnitt konfigurerar den virtuella Azure-datorn som vi skapade. Det första steget är att ansluta till den virtuella datorn.

1. Öppna en kommandotolk och klistra in SSH-anslutningssträngen som du kopierade från skriptutdata. Ange din egen information för användarnamn, suffix och region enligt de värden du angav till PowerShell-skriptet i föregående avsnitt.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. När du uppmanas att verifiera värdens äkthet skriver du **ja** och väljer **Retur**.

3. Ange ditt lösenord när du uppmanas att göra det.

4. Ubuntu visar ett välkomstmeddelande och då `<username>@<machinename>:~$`bör du se en uppmaning som .

## <a name="download-key-vault-certificates"></a>Ladda ner Key Vault-certifikat

Tidigare i den här artikeln laddade vi upp certifikat till Key Vault för att göra dem tillgängliga för vår IoT Edge-enhet och vår bladenhet. Lövenheten är en nedströmsenhet som använder IoT Edge-enheten som en gateway för att kommunicera med IoT Hub.

Vi kommer att ta itu med bladenheten senare i handledningen. I det här avsnittet laddar du ned certifikaten till IoT Edge-enheten.

1. Logga in på Azure med Azure CLI från SSH-sessionen på den virtuella Linux-datorn.

    ```azurecli
    az login
    ```

1. Du uppmanas att öppna en <https://microsoft.com/devicelogin> webbläsare för och tillhandahålla en unik kod. Du kan utföra dessa steg på din lokala dator. Stäng webbläsarfönstret när du är klar med autentisera.

1. När du har autentiserat loggar Linux-virtuella datorn in och listar dina Azure-prenumerationer.

1. Ange den Azure-prenumeration som du vill använda för Azure CLI-kommandon.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Skapa en katalog på den virtuella datorn för certifikaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Hämta certifikaten som du har lagrat i nyckelvalvet: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem och azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Uppdatera konfigurationen av IoT Edge-enheten

IoT Edge-körningen använder `/etc/iotedge/config.yaml` filen för att bevara konfigurationen. Vi måste uppdatera tre bitar av information i den här filen:

* **Enhetsanslutningssträng:** anslutningssträngen från den här enhetens identitet i IoT Hub
* **Certifikat:** de certifikat som ska användas för anslutningar som görs med nedströmsenheter
* **Värdnamn:** det fullständigt kvalificerade domännamnet (FQDN) för VM IoT Edge-enheten.

*Azure IoT Edge på Ubuntu-avbildningen* som vi använde för att skapa IoT Edge-virtuella datorn levereras med ett skalskript som uppdaterar config.yaml med anslutningssträngen.

1. Högerklicka på IoT Edge-enheten i Visual Studio-kod och välj sedan **Kopiera enhetsanslutningssträng**.

    ![Kopiera anslutningssträng från Visual Studio-kod](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. I SSH-sessionen kör du kommandot för att uppdatera filen config.yaml med enhetens anslutningssträng.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Därefter kommer vi att uppdatera certifikat och värdnamn genom att direkt redigera config.yaml.

1. Öppna filen config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Uppdatera avsnittet certifikat i config.yaml genom `#` att ta bort den inledande och ange sökvägen så att filen ser ut som följande exempel:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Kontrollera att **certifikaten:** raden inte har något föregående blanktecken och att vart och ett av de kapslade certifikaten är indragna av två blanksteg.

    Om du högerklickar i nano klistras innehållet i Urklipp in i det aktuella markörens position. Om du vill ersätta strängen använder du tangentbordspilarna för att navigera till den sträng som du vill ersätta, tar bort strängen och högerklickar sedan för att klistra in från bufferten.

3. Navigera till din virtuella dator i Azure-portalen. Kopiera datorns DNS-namn (FQDN) från avsnittet **Översikt.**

4. Klistra in FQDN i värdnamnsavsnittet i config.yml. Kontrollera att namnet är gemen.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Spara och stäng`Ctrl + X`filen `Y` `Enter`( , , ).

6. Starta om iotedge demon.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Kontrollera status för IoT Edge Daemon (efter kommandot skriver du ":q" för att avsluta).

    ```bash
    systemctl status iotedge
    ```

8. Om du ser fel (färgad text\[\]som föregås av " FEL ") i statusen Granska daemonloggar för detaljerad felinformation.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Nästa steg

Vi har precis slutfört konfigurationen av en Azure VM som Azure IoT Edge Transparent Gateway. Vi började med att generera testcertifikat som vi har laddat upp till Azure Key Vault. Därefter använde vi en skript- och Resource Manager-mall för att distribuera den virtuella datorn med avbildningen "Ubuntu Server 16.04 LTS + Azure IoT Edge runtime" från Azure Marketplace. Med den virtuella datorn igång som vi anslutit via SSH loggade vi in på Azure och hämtade certifikat från Key Vault. Vi har gjort flera uppdateringar av konfigurationen av IoT Edge Runtime genom att uppdatera filen config.yaml.

Mer information finns i [Hur en IoT Edge-enhet kan användas som en gateway](iot-edge-as-gateway.md) och konfigurera en [IoT Edge-enhet för att fungera som en transparent gateway](how-to-create-transparent-gateway.md).

Fortsätt till nästa artikel för att skapa IoT Edge-moduler.

> [!div class="nextstepaction"]
> [Skapa och distribuera anpassade IoT Edge-moduler](tutorial-machine-learning-edge-06-custom-modules.md)
