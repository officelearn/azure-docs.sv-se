## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen i blobbehållaren adftutorial. Filen emp.txt kopieras från indatamappen till utdatamappen. 

1. På behållarsidan **adftutorial** i Azure-portalen klickar du på **Uppdatera** för att se utdatamappen. 
    
    ![Uppdatera](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)
2. Klicka på **output** i mapplistan. 
2. Kontrollera att **emp.txt** har kopierats till utdatamappen. 

    ![Uppdatera](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Rensa resurser
Du kan rensa de resurser som du skapade i snabbstarten på två sätt. Du kan ta bort den [Azure-resursgrupp](../articles/azure-resource-manager/resource-group-overview.md) som innehåller alla resurser i resursgruppen. Om du vill behålla de andra resurserna intakta ska du bara ta bort den datafabrik du har skapat i den här självstudiekursen.

När du tar bort en resursgrupp tas alla resurser inklusive datafabriker i den bort. Kör följande kommando om du vill ta bort hela resursgruppen: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Om du bara vill ta bort datafabriken och inte hela resursgruppen kör du följande kommando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```