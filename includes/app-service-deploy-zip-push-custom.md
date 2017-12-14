## <a name="deployment-customization"></a>Distributionsanpassning

Distributionsprocessen förutsätter att ZIP-filen som du överför innehåller en klar och kör appen. Som standard körs utan anpassningar. Du kan aktivera samma build processer som du får med kontinuerlig integration genom att lägga till följande programinställningarna:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

När du använder .zip push-distribution kan den här inställningen är **FALSKT** som standard. Standardvärdet är **SANT** för kontinuerlig integration distributioner. Om värdet är **SANT**, din distribution-relaterade inställningar som används under distributionen. Dessa inställningar kan anges som app-inställningar eller i en `.deployment` konfigurationsfilen i roten av zip-filen. Mer information finns i [databasen och distribution-relaterade inställningar](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) i referens för distribution.