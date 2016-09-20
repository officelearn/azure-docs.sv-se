## Hämta och förstå ARM-mallen

Du kan hämta den befintliga ARM-mallen för att skapa ett VNet och två undernät från github, göra de ändringar du vill och återanvända den. Följ stegen nedan för att göra det.

1. Gå till [exempelmallsidan](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Klicka på **azuredeploy.json** och klicka sedan på **RAW**.
3. Spara filen på en lokal mapp på datorn.
4. Om du är bekant med ARM-mallar, kan du hoppa till steg 7.
5. Öppna filen som du just har sparat och titta på innehållet i **parametrar** på rad 5. ARM-mallparametrarna agerar platshållare för värden som kan anges under distributionen.

    | Parameter | Beskrivning |
    |---|---|
    | **location** | Azure-region där VNet kommer att skapas |
    | **vnetName** | Namn för det nya VNet |
    | **addressPrefix** | Adressutrymmet för VNet, i CIDR-format |
    | **subnet1Name** | Namn för det första VNet |
    | **subnet1Prefix** | CIDR-block för det första undernätet |
    | **subnet2Name** | Namn för den andra VNet |
    | **subnet2Prefix** | CIDR-block för andra undernätet |

    >[AZURE.IMPORTANT] ARM-mallar i github kan ändras med tiden. Var noga med att kontrollera mallen innan den används.
    
6. Kontrollera innehållet i **resurser** och observera följande:

    - **type**. Typ av resurs som skapas av mallen. I det här fallet **Microsoft.Network/virtualNetworks**, vilket motsvarar ett VNet.
    - **name**. Namn på den virtuella resursen. Observera användningen av **[parameters('vnetName')]**, vilket betyder att namnet kommer att anges som indata från användaren eller en parameterfil vid distributionen.
    - **properties**. Lista över egenskaper för resursen. Den här mallen använder adressutrymmet och undernätsegenskaperna vid skapandet av ett VNet.

7. Gå tillbaks till [exempelmallsidan](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Klicka på **azuredeploy-parameters.json** och klicka sedan på **RAW**.
9. Spara filen i en lokal mapp på datorn.
10. Öppna filen som du just har sparat och redigera värdena för parametrarna. Använd värdena nedan för att distribuera det VNet som beskrivs i vårt scenario.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Spara filen.
  


<!--HONumber=sep16_HO1-->


