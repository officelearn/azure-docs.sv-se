# <a name="persist-files-in-azure-cloud-shell"></a>Spara filer i Azure Cloud Shell
Molnet Shell använder Azure File storage för att bevara filer mellan sessioner.

## <a name="set-up-a-clouddrive-file-share"></a>Ställ in en clouddrive filresurs
På första start uppmanas molnet Shell du att associera en ny eller befintlig filresurs för att bevara filer mellan sessioner.

> [!NOTE]
> Dela samma filresurs Bash och PowerShell. Endast en filresurs kan associeras med automatisk montering i molnet Shell.

### <a name="create-new-storage"></a>Skapa nya lagringsenheter

När du använder grundläggande inställningar och välj endast en prenumeration skapar molnet Shell tre resurser å dina vägnar i stöds region som ligger närmast du:
* Resursgrupp:`cloud-shell-storage-<region>`
* Storage-konto:`cs<uniqueGuid>`
* Filresurs:`cs-<user>-<domain>-com-<uniqueGuid>`

![Inställningen för prenumeration](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Filresursen monterar som `clouddrive` i din `$Home` directory. Detta är en engångsåtgärd och filresursen automatiskt monterar i efterföljande sessioner. 

I Bash, filresursen även innehåller en 5 GB-avbildning som skapas för dig som automatiskt kvarstår data i din `$Home` directory. 

### <a name="use-existing-resources"></a>Använda befintliga resurser

Du kan associera befintliga resurser med hjälp av avancerade alternativ. När den lagring installationsprogrammet uppmanas att välja **visa avancerade inställningar** att visa ytterligare alternativ. Nedrullningsbara menyer filtreras för tilldelade moln Shell region och lokalt redundant lagring och konton för geo-redundant lagring.

Befintliga filresurser i Bash, ta emot en 5 GB-avbildning som skapats att spara din `$Home` directory.

![Inställningen för grupp](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Begränsa att skapa en resurs med en princip för Azure-resurs
Storage-konton som du skapar i molnet Shell märks med `ms-resource-usage:azure-cloud-shell`. Om du vill neka användare att skapa storage-konton i molnet Shell, skapa en [Azure resursprincip för taggar](../articles/azure-policy/json-samples.md) som utlöses av den här specifika taggen.

## <a name="supported-storage-regions"></a>Regioner som stöds
Associerade konton måste finnas i samma region som molntjänster Shell-dator som du montera dem till Azure-lagring.

Hitta din tilldelade region som du kan:
* Visa anteckningen i dialogrutan ”Avancerade Lagringsinställningar”
* Referera till namnet på det lagringskonto som skapats åt dig (ex: `cloud-shell-storage-westus`)
* Kör `env` och leta upp variabeln`ACC_LOCATION`

Molnet Shell dator finns i följande områden:
|Område|Region|
|---|---|
|Nord- och Sydamerika|Östra USA, södra centrala USA, västra USA|
|Europa|Norra Europa, västra Europa|
|Asien och stillahavsområdet|Indien Central, sydöstra Asien|

