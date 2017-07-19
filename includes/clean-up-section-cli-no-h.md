I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden, kan du ta bort dem genom att ta bort resursgruppen.
 
1. Kör följande kommando för att kontrollera att resursgruppen inte innehåller några resurser som du vill spara:

  ```azurecli
  az group show --name myResourceGroup
  ```

2. Om resurserna som visas är de som du vill ta bort, kör du följande kommando:
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
