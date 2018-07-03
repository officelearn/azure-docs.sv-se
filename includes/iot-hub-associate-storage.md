## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associera ett Azure Storage-konto till IoT Hub

Eftersom den simulerade enhetsappen överför en fil till en blob, måste du ha en [Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) kontot som är associerat till IoT Hub. När du kopplar ett Azure Storage-konto med en IoT-hubb, genererar IoT-hubben en SAS-URI. En enhet kan använda den här SAS-URI för att på ett säkert sätt överföra en fil till en blob-behållare. Tjänsten IoT Hub och SDK: er för enhetens samordna processen som genererar SAS-URI och gör dem tillgängliga för en enhet som du använder för att ladda upp en fil.

Följ instruktionerna i [konfigurera filöverföringar med Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md) att associera ett Azure Storage-konto till din IoT hub. Se till att en blob-behållare är associerad med din IoT-hubb och att filen meddelanden har aktiverats.

![Aktivera meddelanden för filen i portalen](media/iot-hub-associate-storage/enable-file-notifications.png)