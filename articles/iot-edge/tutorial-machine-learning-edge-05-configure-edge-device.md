---
title: 'Självstudie: Konfigurera IoT Edge enhet – Machine Learning på Azure IoT Edge'
description: I den här självstudien kommer du att konfigurera en virtuell Azure-dator som kör Linux som en Azure IoT Edge enhet som fungerar som en transparent Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5be7b66a51113121ed755d8ad9cea3518577f2e7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706962"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Självstudie: Konfigurera en IoT Edge enhet

> [!NOTE]
> Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt rekommenderar vi att du börjar med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa möjliga resultat.

I den här artikeln konfigurerar vi en virtuell Azure-dator som kör Linux som en Azure IoT Edge-enhet som fungerar som en transparent Gateway. Med den transparenta Gateway-konfigurationen kan enheter ansluta till Azure IoT Hub via gatewayen utan att veta att gatewayen finns. På samma gång är en användare som interagerar med enheterna i IoT Hub inte medveten om den mellanliggande gateway-enheten. Slutligen använder vi den transparenta gatewayen för att lägga till Edge Analytics i vårt system genom att lägga till IoT Edge moduler till gatewayen.

Stegen i den här artikeln utförs vanligt vis av en molnbaserad utvecklare.

## <a name="generate-certificates"></a>Generera certifikat

För att en enhet ska fungera som en gateway måste den kunna ansluta till underordnade enheter på ett säkert sätt. Med Azure IoT Edge kan du använda en PKI (Public Key Infrastructure) för att konfigurera säkra anslutningar mellan enheter. I det här fallet låter vi en underordnad enhet ansluta till en IoT Edge-enhet som fungerar som en transparent Gateway. För att upprätthålla rimlig säkerhet bör den underordnade enheten bekräfta identiteten för den IoT Edge enheten. Mer information om hur IoT Edge enheter använder certifikat finns i avsnittet [information om Azure IoT Edge certifikat användning](iot-edge-certs.md).

I det här avsnittet skapar vi de självsignerade certifikaten med hjälp av en Docker-avbildning som vi sedan skapar och kör. Vi valde att använda en Docker-avbildning för att slutföra det här steget, eftersom det betydligt minskade antalet steg som krävs för att skapa certifikaten på Windows Development-datorn. Se [generera certifikat med Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) för information om hur du skapar certifikaten utan att använda en behållare. [Skapa certifikat med Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) har den uppsättning instruktioner som vi har automatiserat med Docker-avbildningen.

1. Logga in på din virtuella utvecklings dator.

2. Öppna en kommando tolk och kör följande kommando för att skapa en katalog på den virtuella datorn.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Starta **Docker för Windows** från Start-menyn i Windows.

4. Öppna Visual Studio Code.

5. Välj **fil** > **Öppna mapp...** och välj **C:\\källa\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Högerklicka på Dockerfile och välj **Bygg avbildning**.

7. I dialog rutan godkänner du standardvärdet för avbildningens namn och tagg: **createcertificates: senaste**.

8. Vänta tills skapandet har slutförts.

    > [!NOTE]
    > Du kan se en varning om en offentlig nyckel som saknas. Det är säkert att ignorera den här varningen. På samma sätt visas en säkerhets varning som rekommenderar att du kontrollerar/återställer behörigheter för din avbildning, vilket är säkert att ignorera för avbildningen.

9. I terminalfönstret i Visual Studio Code kör du behållaren createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker kommer att uppmanas att få åtkomst till enheten **c:\\** . Välj **dela den**.

11. Ange dina autentiseringsuppgifter när du uppmanas till det.

12. När behållaren har körts klart kontrollerar du att följande filer finns i **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certifikat\\Azure-IoT-test-Only. root. ca. cert. pem
    * c:\\edgeCertificates\\certifikat\\New-Edge-Device-full-Chain. cert. pem
    * c:\\edgeCertificates\\certifikat\\New-Edge-Device. cert. pem
    * c:\\edgeCertificates\\certifikat\\New-Edge-Device. cert. pfx
    * c:\\edgeCertificates\\privat\\New-Edge-Device. Key. pem

## <a name="upload-certificates-to-azure-key-vault"></a>Ladda upp certifikat till Azure Key Vault

För att lagra våra certifikat säkert och för att göra dem tillgängliga från flera enheter laddar vi upp certifikaten till Azure Key Vault. Som du kan se i listan ovan har vi två typer av certifikatfiler: PFX och PEM. Vi kommer att behandla PFX-filen som Key Vault certifikat som ska överföras till Key Vault. PEM-filerna är oformaterad text och vi kommer att behandla dem som Key Vault hemligheter. Vi kommer att använda den Key Vault som är kopplad till arbets ytan Azure Machine Learning som vi skapade genom att köra [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Från [Azure Portal](https://portal.azure.com)navigerar du till arbets ytan Azure Machine Learning.

2. På sidan Översikt i arbets ytan Azure Machine Learning söker du efter namnet på **Key Vault**.

    ![Kopiera nyckel valvs namn](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Överför certifikaten till Key Vault på din utvecklings dator. Ersätt **\<subscriptionId\>** och **\<keyvaultname\>** med din resursinformation.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Logga in på Azure om du uppmanas till det.

5. Skriptet körs i några minuter med utdata som visar de nya Key Vault posterna.

    ![Key Vault skriptets utdata](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Skapa IoT Edge-enhet

För att ansluta en Azure IoT Edge-enhet till en IoT-hubb skapar vi först en identitet för enheten i hubben. Vi tar anslutnings strängen från enhetens identitet i molnet och använder den för att konfigurera körningen på vår IoT Edge-enhet. När enheten har kon figurer ATS och anslutits till hubben kan vi distribuera moduler och skicka meddelanden. Vi kan också ändra konfigurationen för den fysiska IoT Edge enheten genom att ändra konfigurationen för motsvarande enhets identitet i IoT Hub.

I den här självstudien skapar vi den nya enhets identiteten med hjälp av Visual Studio Code. Du kan också utföra dessa steg med hjälp av [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)eller [Azure CLI](how-to-register-device.md#register-with-the-azure-cli).

1. Öppna Visual Studio Code på utvecklings datorn.

2. Öppna ramen för **Azure IoT Hub-enheter** från Visual Studio Code Explorer-vyn.

3. Klicka på ellipsknappen och välj **skapa IoT Edge enhet**.

4. Ge enheten ett namn. För enkelhetens skull använder vi **aaTurbofanEdgeDevice** så att de sorteras före alla klient enheter som vi skapade tidigare genom enhets nätet för att skicka test data.

5. Den nya enheten kommer att visas i listan över enheter.

    ![Visa nya aaTurbofanEdgeDevice i VS Code-Utforskaren](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Distribuera virtuell Azure-dator

Vi använder [Azure IoT Edge på Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) -avbildningen från Azure Marketplace för att skapa vår IoT Edge-enhet för den här självstudien. Azure IoT Edge avbildningen på Ubuntu installerar den senaste Azure IoT Edge-körningen och dess beroenden vid start. Vi distribuerar den virtuella datorn med ett PowerShell-skript `Create-EdgeVM.ps1`; en Resource Manager-mall, `IoTEdgeVMTemplate.json`; och ett gränssnitts skript `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Aktivera program distribution

Om du vill använda avbildningen från Marketplace i en skriptad distribution måste du aktivera program distribution för avbildningen.

1. Logga in på Azure Portal.

1. Välj **Alla tjänster**.

1. Skriv och välj **Marketplace**i Sök fältet.

1. I Sök fältet skriver du och väljer **Azure IoT Edge på Ubuntu**.

1. Välj den **vill distribuera program mässigt? Kom igång** -hyperlänk.

1. Välj knappen **Aktivera** och sedan **Spara**.

    ![Aktivera program distribution för virtuell dator](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Du ser ett meddelande som visar att det är klart.

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

Kör sedan skriptet för att skapa den virtuella datorn för din IoT Edge-enhet.

1. Öppna ett PowerShell-fönster och gå till **EdgeVM** -katalogen.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Kör skriptet för att skapa den virtuella datorn.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Ange värden för varje parameter när du uppmanas till det. För prenumeration, resurs grupp och plats rekommenderar vi att du använder samma som för alla resurser i den här självstudien.

    * **ID för Azure-prenumeration**: hittades i Azure Portal
    * **Resurs grupp namn**: minnes minnes namn för att gruppera resurserna i den här kursen
    * **Plats**: Azure-plats där den virtuella datorn kommer att skapas. Till exempel westus2 eller europanorra. Mer information finns på alla [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: namnet på det administratörs konto som du ska använda för att logga in på den virtuella datorn
    * **AdminPassword**: lösen ordet som ska anges för AdminUsername på den virtuella datorn

4. För att skriptet ska kunna konfigurera den virtuella datorn måste du logga in på Azure med de autentiseringsuppgifter som är associerade med den Azure-prenumeration som du använder.

5. Skriptet bekräftar informationen för att skapa den virtuella datorn. Välj **y** eller **RETUR** för att fortsätta.

6. Skriptet körs i flera minuter eftersom det utför följande steg:

    * Skapa resurs gruppen om den inte redan finns
    * Skapa den virtuella datorn
    * Lägg till undantag för NSG för den virtuella datorn för port 22 (SSH), 5671 (AMQP), 5672 (AMPQ) och 443 (SSL)
    * Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Skriptet matar ut SSH-anslutningssträngen för att ansluta till den virtuella datorn. Kopiera anslutnings strängen för nästa steg.

    ![Kopiera SSH-anslutningssträng för virtuell dator](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ansluta till din IoT Edge-enhet

De följande flera avsnitten konfigurerar den virtuella Azure-dator vi skapade. Det första steget är att ansluta till den virtuella datorn.

1. Öppna en kommando tolk och klistra in SSH-anslutningssträngen som du kopierade från skriptets utdata. Ange din egen information för användar namn, suffix och region enligt de värden som du angav i PowerShell-skriptet i föregående avsnitt.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. När du uppmanas att validera värdens äkthet skriver du **Ja** och väljer **RETUR**.

3. Ange ditt lösen ord när du uppmanas till det.

4. Ubuntu visar ett välkomst meddelande och du bör se en prompt som `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Ladda ned Key Vault certifikat

Tidigare i den här artikeln laddade vi upp certifikat till Key Vault för att göra dem tillgängliga för vår IoT Edge-enhet och vår löv enhet, som är en underordnad enhet som använder den IoT Edge enheten som en gateway för att kommunicera med IoT Hub. Vi kommer att hantera löv enheten senare i självstudien. I det här avsnittet laddar du ned certifikaten till den IoT Edge enheten.

1. Från SSH-sessionen på den virtuella Linux-datorn loggar du in på Azure med Azure CLI.

    ```bash
    az login
    ```

1. Du uppmanas att öppna en webbläsare för att <https://microsoft.com/devicelogin> och ange en unik kod. Du kan utföra de här stegen på den lokala datorn. Stäng webbläsarfönstret när du är klar med autentiseringen.

1. När du har autentiserat loggas den virtuella Linux-datorn in och du får en lista över dina Azure-prenumerationer.

1. ASet den Azure-prenumeration som du vill använda för Azure CLI-kommandon.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Skapa en katalog på den virtuella datorn för certifikaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Hämta de certifikat som du har lagrat i nyckel valvet: New-Edge-Device-full-Chain. cert. PEM, New-Edge-Device. Key. pem och Azure-IoT-test-Only. root. ca. cert. pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Uppdatera IoT Edge enhets konfiguration

Den IoT Edge körningen använder filen/etc/iotedge/config.yaml för att spara konfigurationen. Vi måste uppdatera tre delar av informationen i den här filen:

* **Enhets anslutnings sträng**: anslutnings strängen från den här enhetens identitet i IoT Hub
* **Certifikat:** certifikat som ska användas för anslutningar som görs med underordnade enheter
* **Värdnamn:** det fullständigt kvalificerade domän namnet (FQDN) för den virtuella datorns IoT Edge enhet.

Den *Azure IoT Edge på Ubuntu* -avbildningen som vi använde för att skapa IoT Edge virtuella datorn levereras med ett gränssnitts skript som uppdaterar config. yaml med anslutnings strängen.

1. Högerklicka på den IoT Edge enheten i Visual Studio Code och välj sedan **Kopiera enhets anslutnings sträng**.

    ![Kopiera anslutnings sträng från Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Kör kommandot i SSH-sessionen för att uppdatera filen config. yaml med enhets anslutnings strängen.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Nu ska vi uppdatera certifikaten och värd namnet genom att redigera filen config. yaml direkt.

1. Öppna filen config. yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Uppdatera avsnittet certifikat i config. yaml genom att ta bort det inledande `#` och ange sökvägen så att filen ser ut som i följande exempel:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Se till att "certifikat:" inte har något föregående blank steg och att vart och ett av certifikaten föregås av två blank steg.

    Om du högerklickar på nano klistras innehållet i Urklipp till den aktuella markör positionen. Om du vill ersätta strängen använder du piltangenterna för att navigera till den sträng som du vill ersätta, tar bort strängen och högerklickar sedan för att klistra in från bufferten.

3. I Azure Portal navigerar du till den virtuella datorn. Kopiera DNS-namnet (FQDN för datorn) från **översikts** avsnittet.

4. Klistra in FQDN i hostname-avsnittet i filen config. yml. Kontrol lera att namnet är gemener.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Spara och Stäng filen (`Ctrl + X`, `Y`, `Enter`).

6. Starta om iotedge daemon.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Kontrol lera status för IoT Edge daemon (efter kommandot skriver du ": q" för att avsluta).

    ```bash
    systemctl status iotedge
    ```

8. Om du ser fel (färgad text som föregås av "\[fel\]") i status granska daemon-loggar för detaljerad fel information.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Nästa steg

Vi har precis slutfört konfigurationen av en virtuell Azure-dator som Azure IoT Edge transparent Gateway. Vi startade genom att generera test certifikat, som vi laddade upp till Azure Key Vault. Därefter använde vi en skript-och Resource Manager-mall för att distribuera den virtuella datorn med avbildningen "Ubuntu Server 16,04 LTS + Azure IoT Edge runtime" från Azure Marketplace. Skriptet tog det extra steget för att installera Azure CLI ([Installera Azure CLI med apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Med den virtuella datorn igång via SSH är du inloggad på Azure, hämtat certifikat från Key Vault och gjort flera uppdateringar i konfigurationen av IoT Edge runtime genom att uppdatera filen config. yaml. Mer information om hur du använder IoT Edge som en gateway finns i [hur en IoT Edge enhet kan användas som en gateway](iot-edge-as-gateway.md). Mer information om hur du konfigurerar en IoT Edge enhet som en transparent Gateway finns i [Konfigurera en IoT Edge enhet som fungerar som en transparent Gateway](how-to-create-transparent-gateway.md).

Fortsätt till nästa artikel för att bygga IoT Edge moduler.

> [!div class="nextstepaction"]
> [Skapa och distribuera anpassade IoT Edge-moduler](tutorial-machine-learning-edge-06-custom-modules.md)
