## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associera ett Azure Storage-konto till IoT-hubb

Eftersom den simulerade enhetsapp överför en fil till en blob, måste du ha en [Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) kontot som är kopplat till IoT-hubben. När du kopplar ett Azure Storage-konto med en IoT-hubb genererar IoT-hubben en SAS-URI. En enhet kan använda den här SAS-URI att på ett säkert sätt överföra en fil till en blobbbehållare. Tjänsten IoT Hub och SDK: er för enheten samordna processen som genererar SAS-URI och gör den tillgänglig för en enhet som ska användas för att överföra en fil.

Följ instruktionerna i [konfigurera filöverföringar med hjälp av Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md) att associera ett Azure Storage-konto till din IoT-hubb. Se till att en blob-behållare är associerad med din IoT-hubb och att filen meddelanden är aktiverade.

![Aktivera meddelanden för filen i portalen](media/iot-hub-associate-storage/enable-file-notifications.png)