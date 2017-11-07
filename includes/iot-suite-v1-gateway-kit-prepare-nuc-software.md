## <a name="build-iot-edge"></a>Skapa IoT kant

Den här kursen använder anpassade IoT kant-moduler för att kommunicera med fjärråtkomst övervakning förkonfigurerade lösningen. Därför måste du skapa IoT-Edge moduler från anpassade källkoden. I följande avsnitt beskrivs hur du installerar IoT kant och skapa anpassade IoT kant-modul.

### <a name="install-iot-edge"></a>Installera IoT kant

Följande steg beskriver hur du installerar programmet före kompilerade IoT kant på Intel NUC:

1. Konfigurera databaser för smart paket som krävs genom att köra följande kommandon på Intel NUC:

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Ange `y` när kommandot uppmanar dig att **innehåller den här kanalen?**.

1. Uppdatera smart package manager genom att köra följande kommando:

    ```bash
    smart update
    ```

1. Installera Azure IoT kant-paketet genom att köra följande kommando:

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Verifiera installationen genom att köra exemplet ”Hello world”. Det här exemplet skriver en hello world-meddelande till filen log.txt var femte sekund. Följande kommandon för att köra exemplet ”Hello world”:

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    Ignorera alla **ogiltigt argument** meddelanden när du stoppar exemplet.

    Använd följande kommando för att visa innehållet i filen:

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Felsökning

Om du får felet ”inga erbjuder util-linux-utveckling”, startar du om datorn Intel NUC.
