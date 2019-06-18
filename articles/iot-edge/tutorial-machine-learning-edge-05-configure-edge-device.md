---
title: Konfigurera IoT Edge-enhet – Machine Learning på Azure IoT Edge | Microsoft Docs
description: Konfigurera en Azure virtuell dator som kör Linux som en Azure IoT Edge-enhet som fungerar som en transparent gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a2096004a7b389f627c528a8dfb4768ac001f390
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155626"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Självstudier: Konfigurera en IoT Edge-enhet

> [!NOTE]
> Den här artikeln ingår i en serie med en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har hamnat på den här artikeln direkt, rekommenderar vi att du börja med den [först artikel](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln konfigurerar vi en Azure virtuell dator som kör Linux ska vara en Azure IoT Edge-enhet som fungerar som en transparent gateway. Transparent gateway-konfigurationen tillåter enheter att ansluta till Azure IoT Hub via gatewayen utan att känna till att gatewayen finns. En användare som interagerar med enheter i IoT Hub är ovetande om mellanliggande gateway-enheten på samma gång. Slutligen kan vi använda en transparent gateway att lägga till gränsanalyser i vårt system genom att lägga till IoT Edge-moduler till gatewayen.

Stegen i den här artikeln är vanligtvis utförs av en utvecklare i molnet.

## <a name="generate-certificates"></a>Generera certifikat

För en enhet ska fungera som en gateway krävs för att kunna ansluta säkert till efterföljande enheter. Azure IoT Edge kan du använda en public key infrastructure (PKI) för att konfigurera säkra anslutningar mellan enheter. I det här fallet vi så att en underordnad enhet att ansluta till en IoT Edge-enhet som fungerar som en transparent gateway. Om du vill skydda rimliga, bör underordnade enheten bekräfta identiteten på IoT Edge-enhet. Mer information om hur IoT Edge-enheter använder certifikat finns i [användningsinformation för Azure IoT Edge certifikat](iot-edge-certs.md).

I det här avsnittet ska skapa vi självsignerade certifikat med hjälp av en Docker-avbildning som vi sedan skapar och kör. Vi har valt att använda en Docker-avbildning för att slutföra det här steget eftersom det avsevärt minskar antalet steg som behövs för att skapa certifikaten på Windows-utvecklingsdator. Se [generera certifikat med Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) för information om hur du genererar certifikat utan att använda en behållare. [Generera certifikat med Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) har uppsättning instruktioner som vi automatiserade med Docker-avbildningen.

1. Logga in på en virtuell utvecklingsdator.

2. Öppna Kommandotolken och kör följande kommando för att skapa en katalog på den virtuella datorn.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Starta **Docker för Windows** från Windows Start-menyn.

4. Öppna Visual Studio Code.

5. Välj **filen** > **Öppna mapp...**  och välj **C:\\källa\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Högerklicka på dockerfile och välj **Skapa avbildning**.

7. I dialogrutan godkänner du standardvärdet för avbildningsnamn och tagg: **createcertificates:latest**.

8. Vänta tills versionen att slutföra.

    > [!NOTE]
    > Du kan se en varning för om en offentlig nyckel som saknas. Det är säkert att ignorera den här varningen. På samma sätt, visas en säkerhetsvarning som rekommenderar att du kontrollera/reset behörigheterna för din avbildning, där man kan ignorera för den här avbildningen.

9. Kör behållaren createcertificates i terminalfönstret för Visual Studio Code.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker efterfrågar åtkomst till den **c:\\**  enhet. Välj **dela den**.

11. Ange dina autentiseringsuppgifter när du tillfrågas.

12. En gång i behållaren har slutförts kör, Sök efter följande filer i **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Ladda upp certifikat till Azure Key Vault

Att lagra vårt certifikat på ett säkert sätt och för att göra dem åtkomliga från flera enheter, kommer vi att överföra certifikat i Azure Key Vault. Du kan se i listan ovan, har vi två typer av certifikatfiler: PFX-filen och PEM. Vi behandlar den PFX-filen som Key Vault-certifikat som ska överföras till Key Vault. PEM-filer är oformaterad text och vi ska behandla dem som Key Vault-hemligheter. Vi kommer att använda det Nyckelvalv som är kopplade till arbetsytan för Azure Machine Learning-tjänsten som vi skapade genom att köra den [Azure anteckningsböcker](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Från den [Azure-portalen](https://portal.azure.com), navigera till din arbetsyta för Azure Machine Learning-tjänsten.

2. På översiktssidan för arbetsytan Azure Machine Learning-tjänsten, hitta namnet på den **Key Vault**.

    ![Kopiera key vault-namn](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Ladda upp certifikat till Key Vault på din utvecklingsdator. Ersätt **\<subscriptionId\>** och **\<keyvaultname\>** med information om dina resurser.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Om du uppmanas logga in på Azure.

5. Skriptet ska köras till några minuter med utdata som visar de nya Key Vault-posterna.

    ![Key Vault skriptutdata](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Skapa IoT Edge-enhet

För att ansluta en Azure IoT Edge-enhet till IoT hub måste skapa vi först en identitet för enheten i hubben. Vi kan anslutningssträngen från enhetens identitet i molnet och använda den för att konfigurera körningen på vår IoT Edge-enhet. När enheten har konfigurerats och ansluter till hubben, kommer du att distribuera moduler och skicka meddelanden. Vi kan också ändra konfigurationen av den fysiska IoT Edge-enheten genom att ändra konfigurationen av motsvarande enhetsidentitet i IoT hub.

I den här självstudiekursen skapar vi nya enhetens identitet med hjälp av Visual Studio Code. Du kan också utföra dessa steg med hjälp av den [Azure-portalen](how-to-register-device-portal.md), eller [Azure CLI](how-to-register-device-cli.md).

1. Öppna Visual Studio Code på din utvecklingsdator.

2. Öppna den **Azure IoT Hub-enheter** bildruta från Utforskarvy för Visual Studio Code.

3. Klicka på knappen med tre punkter och välj **skapa IoT Edge-enhet**.

4. Ge enheten ett namn. För enkelhetens skull använder vi **aaTurbofanEdgeDevice** så sorteras före alla klientenheter som vi skapade tidigare via en stomme för enheten att skicka testdata.

5. Den nya enheten visas i listan över enheter.

    ![Visa nya aaTurbofanEdgeDevice i VS Code-Utforskaren](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Distribuera Azure-dator

Vi använder den [Azure IoT Edge på Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) avbildning från Azure Marketplace för att skapa vår IoT Edge-enhet för den här självstudiekursen. Azure IoT Edge på Ubuntu-avbildning installerar den senaste Azure IoT Edge-körningen och dess beroenden vid start. Vi distribuera den virtuella datorn med hjälp av ett PowerShell-skript `Create-EdgeVM.ps1`; en Resource Manager-mall `IoTEdgeVMTemplate.json`; och ett kommandoskript `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Aktivera programdistribution

Om du vill använda avbildningen från marketplace i en skriptdistribution, måste vi du aktivera programdistribution för avbildningen.

1. Logga in på Azure Portal.

1. Välj **Alla tjänster**.

1. Ange i sökfältet och välj **Marketplace**.

1. Ange i sökfältet och välj **Azure IoT Edge på Ubuntu**.

1. Välj den **vill distribuera via programmering? Kom igång** hyperlänk.

1. Välj den **aktivera** knappen sedan **spara**.

    ![Aktivera programdistribution för den virtuella datorn](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Du ser ett meddelande om lyckad.

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

Kör skriptet för att skapa den virtuella datorn för din IoT Edge-enhet.

1. Öppna ett PowerShell-fönster och navigera till den **EdgeVM** directory.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Kör skriptet för att skapa den virtuella datorn.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. När du uppmanas, anger du värden för varje parameter. För prenumeration använder resursgrupp och plats som vi rekommenderar att du samma som du har för alla resurser i den här självstudien.

    * **Azure prenumerations-ID**: finns i Azure-portalen
    * **Namn på resursgrupp**: lätt att komma ihåg namn för att gruppera resurserna för den här självstudiekursen
    * **Plats**: Azure-plats där den virtuella datorn kommer att skapas. Till exempel usavästra2 eller europanorra. Mer information finns alla [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: namnet på administratörskontot som du använder för att logga in på den virtuella datorn
    * **AdminPassword**: lösenordet som angetts för AdminUsername på den virtuella datorn

4. Du måste logga in på Azure med de autentiseringsuppgifter som är associerade med Azure-prenumeration som du använder för ett skript för att kunna konfigurera den virtuella datorn.

5. Skriptet bekräftar information för att skapa den virtuella datorn. Välj **y** eller **RETUR** att fortsätta.

6. Skriptet körs i flera minuter som den utför följande steg:

    * Skapa resursgruppen om den inte redan finns
    * Skapa den virtuella datorn
    * Lägga till NSG-undantag för den virtuella datorn för portarna 22 (SSH), 5671 (AMQP), 5672 (AMPQ) och 443 (SSL)
    * Installera den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Skriptet matar ut SSH-anslutningssträng för att ansluta till den virtuella datorn. Kopiera anslutningssträngen för nästa steg.

    ![Kopiera SSH-anslutningssträng för virtuell dator](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ansluta till din IoT Edge-enhet

I nästa avsnitt för flera konfigurera Azure-dator som vi skapade. Det första steget är att ansluta till den virtuella datorn.

1. Öppna en kommandotolk och klistra in SSH-anslutningssträng som du kopierade från utdata från skriptet. Ange din egen information användarnamn, suffix och region enligt de värden som du angav i PowerShell-skriptet i föregående avsnitt.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. När du uppmanas att verifiera värdens äkthet, skriver **Ja** och välj **RETUR**.

3. När du uppmanas, anger du ditt lösenord.

4. Ubuntu visar ett välkomstmeddelande och då bör du se ett meddelande liknande `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Ladda ned Key Vault-certifikat

Tidigare i den här artikeln ska överföra vi certifikat till Key Vault för att göra dem tillgängliga för våra IoT Edge-enhet och våra löv-enheten, vilket är en underordnad enhet som använder IoT Edge-enhet som en gateway för att kommunicera med IoT Hub. Vi kommer omfattar löv enheten senare under kursen. Hämta certifikaten till IoT Edge-enheten i det här avsnittet.

1. Från SSH-session på Linux-dator att logga in på Azure med Azure CLI.

    ```bash
    az login
    ```

1. Du uppmanas att öppna en webbläsare till <https://microsoft.com/devicelogin> och ange en unik kod. Du kan utföra dessa steg på den lokala datorn. Stäng webbläsarfönstret när du är klar autentiseras.

1. När du har autentiserar Linux VM logga in och visa dina Azure-prenumerationer.

1. ASet Azure-prenumerationen som du vill använda för Azure CLI-kommandon.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Skapa en katalog på den virtuella datorn för certifikaten.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Ladda ned de certifikat som du har lagrat i nyckelvalvet: nya-edge-enhet-full-chain.cert.pem, nya edge device.key.pem och azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Uppdatera konfigurationen av IoT Edge-enhet

IoT Edge-körningen använder filen /etc/iotedge/config.yaml för att spara konfigurationen. Vi behöver uppdatera tre typer av information i den här filen:

* **Enhetens anslutningssträng**: anslutningssträngen från den här enhetens identitet i IoT Hub
* **Certifikat:** de certifikat som kan användas för anslutningar med efterföljande enheter
* **Värdnamn:** VM IoT Edge-enheten fullständigt kvalificerade domännamnet (FQDN).

Den *Azure IoT Edge på Ubuntu* avbildning som vi använde för att skapa den virtuella datorn IoT-Edge levereras med ett kommandoskript som uppdaterar config.yaml med anslutningssträngen.

1. I Visual Studio Code högerklickar du på IoT Edge-enheten och välj sedan **kopia enhetens anslutningssträng**.

    ![Kopiera anslutningssträngen från Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Kör kommandot för att uppdatera filen config.yaml med enhetens anslutningssträng i SSH-sessionen.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Vi kan sedan uppdatera certifikat och värdnamnet genom att redigera config.yaml.

1. Öppna filen config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Uppdatera certifikat-avsnittet i config.yaml genom att ta bort ledande `#` och ange sökvägen så att fil som ser ut som i följande exempel:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Kontrollera att den ”certifikat”: inte har någon föregående blanksteg och att var och en av certifikat som föregås av två blanksteg.

    Att högerklicka i nano, klistra in innehållet i Urklipp till den aktuella pekarpositionen. Använd ditt tangentbord pilarna för att navigera till strängen som du vill ersätta, ta bort strängen för att ersätta strängen och högerklicka för att klistra in från bufferten.

3. Navigera till den virtuella datorn i Azure-portalen. Kopiera DNS-namn (FQDN för datorn) från den **översikt** avsnittet.

4. Klistra in det fullständiga Domännamnet i avsnittet värdnamnet i config.yml. Kontrollera att namnet är små bokstäver.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Spara och stäng filen (`Ctrl + X`, `Y`, `Enter`).

6. Starta om daemon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Kontrollera status för IoT Edge-Daemon (efter kommandot, skriver du ”: frågor och” avsluta).

    ```bash
    systemctl status iotedge
    ```

8. Om du får felmeddelanden (färgas text med prefixet ”\[fel\]”) i status granska daemon loggarna för Detaljerad felinformation.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Nästa steg

Vi klar är att konfigurera en Azure virtuell dator som Transparent Gateway för Azure IoT Edge. Vi igång genom att generera testcertifikat som vi laddade upp till Azure Key Vault. Nu ska vi använde ett skript och Resource Manager-mall för att distribuera den virtuella datorn med ”Ubuntu Server 16.04 LTS + Azure IoT Edge-körningen” avbildning från Azure marketplace. Skriptet tog extra steg för att installera Azure CLI ([installera Azure CLI med apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Med den virtuella datorn upp och körs vi är anslutna via SSH, loggat in på Azure, ned certifikat från Key Vault och gjort flera uppdateringar genom att uppdatera filen config.yaml konfigurationen av IoT Edge-körningen. Mer information om hur du använder IoT Edge som en gateway finns i [hur en IoT Edge-enhet kan användas som en gateway](iot-edge-as-gateway.md). Mer information om hur du konfigurerar en IoT Edge-enhet som en transparent gateway finns i [konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md).

Fortsätt till nästa artikel för att skapa IoT Edge-moduler.

> [!div class="nextstepaction"]
> [Skapa och distribuera anpassade IoT Edge-moduler](tutorial-machine-learning-edge-06-custom-modules.md)
